<h1> Collection Service</h1>
The collection service is a convenient way to manage multiple instances in game by attaching tags to instances, such as scripts, levels, sounds, etc. This will be particularly useful in this scenario as our game involves a lot of level loading, and so levels will need to be cloned from the server and then destroyed once the round is done. In normal circumstances, we would need to iterate through workspaces and instances to destroy things in the game and add some form of logic that deals with the identification of the associated instances. With the collection service however, we are able to automatically locate these instances and apply some functions to it without the need to search for them.

<h2> Level Loader - Tagging Objects </h2>
The current Studio does not develop a way to manually tag items, but tagging items can be achieved through the use of plug-ins or by programming. This document will highlight how we can clone levels from the Server Storage into the workspace, which will then be automatically replicated to all clients via the game engine. The first objective is to first locate the level we want to clone. To do this, we will create a function that takes in a string - the level name - and searches the Levels folder in the Server storage for the particular instance with the matching level name. The level will then be cloned, and then it's parent will be moved from the folder to the workspace.

```
function LoadLevel(LevelName)
  -- Locate level and clone level to workspace
  local LevelFolder = game.ServerStorage.Levels
  local TargetLevel = LevelFolder:FindFirstChild(LevelName)
  local NewLevel = TargetLevel:Clone()
  NewLevel.Parent = workspace
end
```

Although the level is currently grouped together, our level requires them to be degrouped for the game to function properly. The next task is to set all children of the level's parent to the workspace while also tagging the object with the "level" tag so that we are able to track which items we should remove once the round has ended. This will cover the level loading portion aspect of our game. However, some games may additionally spawn some items into the game which will not be tagged through the level loader - such as a bullet being instantiated - and so can be accounted for by tagging during instantiation or by using the Instance:ChildAdded() listener on the workspace and binding a tagging function to it.

```
function LoadLevel(LevelName)
  -- Locate level and clone level to workspace
  local LevelFolder = game.ServerStorage.Levels
  local TargetLevel = LevelFolder:FindFirstChild(LevelName)
  local NewLevel = TargetLevel:Clone()
  NewLevel.Parent = workspace

  -- Add Level tag to each child of the level and ungroup to workspace
  local CollectionService = game:GetService("CollectionService")
  for _, obj in pairs(NewLevel:GetChildren()) do
    obj.Parent = workspace
    CollectionService:AddTag(obj, "Level")
  end
  -- Destroy empty parent object
  NewLevel:Destroy()
end
```

<h2> Level Remover - Identifying Tagged Objects </h2>

Once we have a functioning game and a round is over - be it through firing an event or a waiting mechanism - we will eventually need to clean up a level. Using out tagging mechanism, it becomes extremely trivial to identify which parts have been tagged with "level". Here, the GetTagged("Level") function will return an array (Lua refers to these as tables) of instances with the tag "level". All that we would need to do is to iterate through these and apply some function to them.

```
function RemoveLevel()	
	print("Removing level")
	local CollectionService = game:GetService("CollectionService")
	for _, LevelObjs in pairs(CollectionService:GetTagged("Level")) do 		-- iterate over tagged objects
		LevelObjs:Destroy()							-- destroy tagged object
	end
end
```

<h2> Listening to Tags being Added </h2>
In our first code with the level loader, we managed to move the level to the workspace due to the fact that the level objects were grouped together. However, we can listen for an event in which tags are being added, and apply some function once the tag is being added. Here we define a different approach into moving the game into the workspace altogether. The following code will use some similar logic in our level loader in the first 2 blocks, but will outsource the parenting logic to the listener function.

```
function LoadLevel(LevelName)
  -- Locate level and clone level INTO THE SAME FOLDER.
  local LevelFolder = game.ServerStorage.Levels
  local TargetLevel = LevelFolder:FindFirstChild(LevelName)
  local NewLevel = TargetLevel:Clone()

  -- Add Level tag to each child of the level only
  local CollectionService = game:GetService("CollectionService")
  for _, obj in pairs(NewLevel:GetChildren()) do
    obj.Parent = workspace
    CollectionService:AddTag(obj, "Level")
  end
  -- Destroy empty parent object
  NewLevel:Destroy()
end

-- Define a listener to listen for level tag being added and apply some function
-- This block will deal with instance being moved into the workspace
game:GetService("CollectionService"):GetInstanceAddedSignal("Level"):Connect(function()
	object.Parent = workspace
end)
```

	
