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

LocalScript (ClientSide)
```
game.ReplicatedStorage.TestRemoteEvent.OnClientEvent:Connect(function(StringMessage)
print(StringMessage)
end)
```

Script (ServerSide)
```
game.ReplicatedStorage.TestRemoteEvent:Fire("This is a test.")
```

