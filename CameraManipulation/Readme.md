<h1> Camera Basics</h1>
Different games may have differing camera functions depending upon the functionality or the style of a game - some games may utilise a arial camera such as ones seen in top-down shooters or roguelite games such as Enter the Gungeon or Hades. Some will use a side-angle camera such as Battleblock Theater or Darkest Dungeon. The Roblox engine by default supports a free-roam camera that is attached to the player's character.

One important thing to note is that the camera can only be manipulated client side and hence through LocalScripts - for a server to control the camera, it would need to fire a Remote Event or a Remote function to the client to signal the camera manipulation. The following article will detail some useful features of the Camera instance in the engine.

<h2> Creating a Remote Event</h2>
<p>
A remote event is an instance that can be created to enable communication between Server-side code (referred to as Scripts) and Client-side code (referred to as LocalScripts) - a function can be binded on the client side (and hence on client event) or on the server side (on server event), and both sides are capable of firing the event. This event is typically stored in the ReplicatedStorage service of the game as both the servers and all clients should be able to acess the remote event. LocalScripts are typically stored and can function in the PlayerGUI, PlayerCharacter or PlayerScripts folder. The following shows a simple code in which the server will fire an event to all clients.
</p>

<p>
RemoteEvent must first be created in ReplicatedStorage named "TestRemoteEvent".
</p>

<h3> LocalScript (ClientSide) </h3>

```
game.ReplicatedStorage.TestRemoteEvent.OnClientEvent:Connect(function(StringMessage)
	print(StringMessage)
end)
```

<h3> Script (ServerSide) </h3>

```
game.ReplicatedStorage.TestRemoteEvent:FireAllClients("This is a test.")
```

What should happen is that the message should be printed server side. It is important to note that Instances can also be passed as parameters - Parts, GameObjects, etc - and this can be useful for passing in instances that we may want to create a camera focus on. It is also important to note that functions cannot be passed through these events.

<h2> Shifting Camera Focus </h2>

The following example will detail how to create a camera that can shift focus on objects, while animating the camera to smoothly transition to the next camera position (think of the a shop feature where the camera will pan to an object you would like to buy). To obtain the clientside camera, one will simply need to call workspace.CurrentCamera. The camera comes with many different properties and methods that one can play around with, but we will be namely transitioning the Coordinate frame of the camera. The Coordinate frame not only determines the position of the camera, but also the orientation in which one should focus in. The constructor for the CFrame object also conveniently comes with a way to determine the CFrame required for Object 1 to face Object 2 (by calling CFrame.new(Object1.Position, Object2.Position) see more <a href="https://create.roblox.com/docs/workspace/cframes"> here </a>). With this in mind, we would like to work with 2 objects in the workspace - the object to focus on and the camera starting position.

<h3> LocalScript (ClientSide) </h3>

```
local CurrentCam = workspace.CurrentCamera        -- Get the clientside camera
CurrentCam.Type = "Scriptable"                    -- Change the camera to scriptable mode

local CameraStart = Instance.new("Part")          -- Create a new part in workspace to function as camera position
CameraStart.Parent = workspace
CameraStart.Position = Vector3.new(0,5,10)

local CameraFocus = Instance.new("Part")          -- Create a new part in workspace to function as camera focus
CameraStart.Parent = workspace
CameraStart.Position = Vector3.new(0,5,10)

local CameraCFrame = CFrame.new(CameraStart.Position, CameraFocus.Position)        -- Calculate Coordinate Frame value for CameraStart to face CameraFocus.
CurrentCam.CFrame = CameraCFrame                  -- Change the orientation of the camera object
```

The following is a very simple way to create a fixed camera angle, such as a CCTV. However, we can generalise the code by encapsulating it as a function, and passing the two instances as a parameter to enable modularity. We can also bind the function to the original remote so that the server is able to trigger the clients camera function via a Server Script.

LocalScript (ClientSide)
```
function ChangeCamera(CameraStart, CameraFocus)
  local CurrentCam = workspace.CurrentCamera                                        -- Get the clientside camera
  CurrentCam.Type = "Scriptable"                    -- Change the camera to scriptable mode
  local CameraCFrame = CFrame.new(CameraStart.Position, CameraFocus.Position)       -- Calculate Coordinate Frame value for CameraStart to face CameraFocus.
  CurrentCam.CFrame = CameraCFrame                                                  -- Change the orientation of the camera object
end

game.ReplicatedStorage.TestRemoteEvent.OnClientEvent:Connect(function(Object1, Object2)  -- Bind the function to the original remote event
  ChangeCamera(Object1, Object2)
end)
```

<h3> Script (ServerSide) </h3>

```
-- We have 3 pairs of two parts in the workspace to function as three different camera scenes. I will avoid
-- the instantiation process as it is a bit primitive, but we will assume these will be parts are named
-- CS1, CS2, CS3 and CF1, CF2, CF3 to represent Camera Start and Camera Focus respectively.

game.ReplicatedStorage.TestRemoteEvent:FireAllClients(CS1, CF1)
wait(5)
game.ReplicatedStorage.TestRemoteEvent:FireAllClients(CS2, CF2)
wait(5)
game.ReplicatedStorage.TestRemoteEvent:FireAllClients(CS2, CF2)
wait(5)
```

By executing the server scripts, this will fire off the test remote event which in turn will trigger the clientside camera change and trigger 3 different 'cutscenes'. It is important to note the serverscript will execute for all players, but can be fired for specific players by using the :FireClient(player, ..params) in place of :FireAllClients(..Params) method instead. What one would also notice is that the current camera will cause an immediate 'switch', 'snap' or 'jerk' effect in the camera change, much like a person switching views in a CCTV. One would may opt for a smooth transition to a different view and hence some animation may be required which can be achieved by using the TweenService that the game provides.

<h2> Smooth Transitions </h2>
To animate a camera this involves first defining a TweenInfo - which specifies the time of the animation and the easing style i.e. linear or a bounce transition - before calling the TweenService to create a new tween. This tween will need to be defined using what Instance is to be animated, the previous TweenInfo, and also the property change given as a string. The following is a small change in our ClientSide script to allow linear transitions.

<h3> LocalScript (ClientSide) </h3>

```
function ChangeCamera(CameraStart, CameraFocus)
  local CurrentCam = workspace.CurrentCamera                                        -- Get the clientside camera
  CurrentCam.Type = "Scriptable"                    -- Change the camera to scriptable mode
  local CameraCFrame = CFrame.new(CameraStart.Position, CameraFocus.Position)       -- Calculate Coordinate Frame value for CameraStart to face CameraFocus.

  local TweenSv = game:GetService("TweenService")                                    -- Calling the Tween Service
  local TweenInf = TweenInfo.new(1,0)                                                -- Creating a new tween info of Time 1 second and EasingStyle Enum 0 (linear)
  local newTween = TweenSv:Create(CurrentCam, TweenInf, {CFrame = CameraCFrame})      -- Creating the new tween
	newTween:Play()                                                                    -- Play the new tween
end

game.ReplicatedStorage.TestRemoteEvent.OnClientEvent:Connect(function(Object1, Object2)  -- Bind the function to the original remote event
  ChangeCamera(Object1, Object2)
end)
```
