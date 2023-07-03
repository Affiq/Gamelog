<h1> Introducing a Throwing Mechanic </h1>

<p>
In our game, we would like to introduce a throwing mechanic. These will operate with the help of the ProximityPrompt object. 
The basic premise is that if the user is within range, and presses a specific key, these will fire specific events which will form 
the basis of our throwing mechanic!
</p>

<p>
With the current game engine, we have two specific events that can be detected when a user interacts with the proximity prompt. One will
be an on-hold function (such as holding down on the E button) and the other one is an on-release function (releasing the E button) will
mirror the aiming and throwing mechanics respectively. So our task is as follows, when we aim, the player should remain stationary, and the
player should be able to observe the potential trajectory of the object and when the player releases the button, an impulse or velocity should
be applied to the object that will follow the trajectory.
</p>



<h2> Throwing </h2>
It seems a bit counter-intuitive to describe the implementation of the throwing before the aiming, but it is much easier to implement
the throwing before the aiming. In addition, it would be more convenient for testing as we can observe how closely the object follows the
trajectory to deduce how accurate the aiming is.
