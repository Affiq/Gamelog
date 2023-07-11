<h1> Development Log </h1>

The following repository is to document the development of different components for a game which may be applicable to other users, or general insight for people looking to develop a game. Each folder contains a readme that serves as an article or pointers on how to develop the specific component.

The following documents will highlight game development in a Object-Oriented approach, with some focus on Event-Based approaches to designing a game. It is developed in Lua using the Roblox game engine via Studio.

<h2> Game Development Tools </h2>
<ul>
  <li> <b> Game Engines: </b> Unity, Godot, Roblox, GameMaker </li>
  <li> <b> Graphics: </b> GIMP, Paint.net, Aseprite (pixel graphics) </li>
  <li> <b> Meshes/Animation: </b> Blender, Maya </li>
  <li> <b> Audio: </b> Audacity, BFXR, SSFXR </li>
  <li> <b> Music: </b> LMMS, Youtube Audio Library</li>
</ul>

<h2> Useful Resources </h2>
<ul>
  <li> <a href="https://www.lua.org/start.html"> Learn more about Lua </a> </li>
  <li> <a href="https://create.roblox.com/docs/projects/client-server"> Learn about Roblox's Client-Server model</a> </li>
  <li> <a href="https://create.roblox.com/docs/studio"> Learn about Instances with the official documentation</a> </li>
</ul>

<h2> Brief Outline of Main Services </h2>
<ol>
  <li> <h4> Workspace </h4>
    <p> The main area which holds the parts and instances that will be visible and enabled in the real world game. The Server workspace is also replicated
    to the client's version of the workspace, so critical code in the workspace should be minimised. Instances that are generated via clientcode will also
    only exist in the client's version and will not replicate to the server (and hence other clients)</p>
  </li>
  <li> <h4> Players </h4>
    <p> Holds the ingame list of players, and their associated player scripts, GUIs and backpack (inventory).</p>
  </li>
  <li> <h4> ReplicatedFirst </h4>
    <p> Instances that are immediately replicated to the client - important for crucial aspects such as Loading Screens, Lobbies, Core LocalScripts, etc</p>
  </li>
  <li> <h4> ReplicatedStorage </h4>
    <p> Instances that are replicated to the client after the game has loaded - more for less crucial aspects of the game such as remote events, backdrops or animations</p>
  </li>
    <li> <h4> ServerStorage </h4>
    <p> Instances that can be stored that will not be replicated to the client, crucial for core aspects that should not be visible to the user.</p>
  </li>
    <li> <h4> ServerScriptService </h4>
    <p> Much like ServerStorage, but allows a secure place for scripts (code) to run without being present in the workspace and hence visible to any client.</p>
  </li>
</ol>
