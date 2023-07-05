<h1>Creating a Detailed Main Menu</h1>

There seems to be a number of different approaches to creating a main menu. One approach would be to build a heirachical menu leading to different options (Creating a Level Select Screen, a Settings Screen or PlayerList screen) and to be activated with a sense of onclick functions. This comes with the advantage of being easy to build and is convenient for quick startups or simple menus, but may become more difficult to build and maintain for complex menus with deeper submenus.

Some careful consideration must also be put in instances with player menus that are synced with other users menus - such as a lobby seeing what map  a host is selecting - and require a mix of client-server programming.

In this scenario, we will be building a menu that stores the state of the menu (a string) and by creating a listener for the change of values in this state. A function will be binded that decides what menus are visible and are enabled according to this state. This is especially advantageous as we can now bind multiple events to the closing of the menu by simply changing the state instead of manually referencing the specific player's User Interfaces and disabling them one by one. This can be used in cases where if a user is inactive in a lobby screen, where the menu changes after the game detects no userinput after x amount of seconds.

<h2> Server-Client Architecture </h2>

Some games may introduce methods of programming on both server-side and client-side. It is important to understand how the scripts affect the game on the client side, and how it affects the server-side (and how this is effectively replicated to the client side). In some circumstances, code will only happen on particular clients instead of the server (such as opposing enemies not being able to see your ping or minimap) and hence some careful thought is required on how your code should be structured.

The game engine we are working with has the ability to fire Events and Functions for similar boundary interaction (Server -> Server or Own Client -> Own Client) or to use 
Remote Events and Functions for cross-boundary communication (Server -> Client or Client -> Server or chain of these to form Client -> Server -> Client)

<h2> Creating a Listener for our State </h2>

The first thing to do is to create the String Value object and reference it. Once done, we can connect the StringValue.Changed event and then pass a function or execute a block responsible for the UI visibilities. The StringValue will be referred to as our Screen State from here on.

```
local CurrentScreenRef = script.Parent            -- Object to listen for changes on
CurrentScreenRef.Changed:Connect(function()        -- Bind a function to the Value Changed event

	if CurrentScreenRef.Value == "MainMenu" then
		-- Make all other screens invisible and MainMenu screen visible
	elseif CurrentScreenRef.Value == "LevelSelect" then
		-- Make all other screens invisible and LevelSelect screen visible
	end
end)
```

<h2> Console Compatibility </h2>
As we mentioned before, we can now easily change the player menu now that the visibility is binded to the screen state. UIs on screen which involve a series of mouse clicks can be highly convoluted for console players. However, the same menu can now be controlled by a controller without dragging a cursor by listening for a gamepad input.

```
local UIS = game:GetService("UserInputService")      -- Call the user input service to detect gamepad input

-- Back function:
local LevelSelectControllerBind = Enum.KeyCode.ButtonA     -- Create a simple reference to the user pressing the A button on the gamepad

UIS.InputBegan:Connect(function(input,gameprocessed)    -- Bind the user input to a logic block that determines what key the user has pressed and what actions to take
	if gameprocessed then return end
	
	if input.KeyCode == LevelSelectControllerBind then      
		script.Parent.CurrentScreen.Value = "LevelSelect"    -- Change value of the Screen State
	end
end)
```

