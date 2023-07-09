<h1> Screen Transitions </h1>
Much like the camera manipulation, we do not want the user to be surprised with sudden changes on the screen, and this can be applied to transitions 
between the User Interface or the real world space. Hence we can make use of a temporary user-interface to transition between different states in our
game.

<h2> Intergrated Transition Approach </h2>
A simple frame can be integrated into the an existing GUI and then trigerred whenever the screen state changes. This gives the added benefit that both 
the existing user interface (such as the main menu) and the transition frame can be referenced, allowing you to execute GUI related code after a transition
completes, and many other events. However, this introduces the downside of low modularity as it is difficult to reuse the function for another main menu.

We can utilise the transparency component of a tween to create a fade animation. The following details an example of how frames become visible after a 
tween (transition/animation) is completed.


``` 
-- Fade frame animation...
local FadeFrame = script.Parent.Parent.FadeFrame
local TweenServ = game:GetService("TweenService")
local TInfo = TweenInfo.new(1,0)
local FadeIn = TweenServ:Create(FadeFrame, TInfo, {BackgroundTransparency = 0})
local FadeOut = TweenServ:Create(FadeFrame, TInfo, {BackgroundTransparency = 1})


CurrentScreenRef.Changed:Connect(function()
	
	if CurrentScreenRef.Value == "MainMenu" then        -- If the screen state changes to MainMenu
		FadeIn:Play()                                     -- Play the fade in animation
		newFade = FadeIn.Completed:Connect(function()     -- Once, completed, execute logic and play fade out animation
			LevelSelectMenu.Visible = false
			LevelSelectControls.Enabled = false

			MainMenuRef.Visible = true
			MainMenuControls.Enabled = true
			newFade:Disconnect()
			FadeOut:Play()
		end)
  elseif...
``` 

<h2> Short Lived Transition Approach </h2>
Another approach would be to have a seperate User Interface with the animation built in stored in the ServerStorage. Once cloned into the 
appropriate player, the LocalScript will then activate, playing the transition before deleting itself. This is especially useful as this
is more self-manageable and modular relying on the developer to simply clone the script and parent it to the player. However, this comes with
the added drawback of being independent from the target User Interfaces, so a transition may fade in too early (leading players to see unwanted
aspects of the game) or it may transition too late (extremely inconvenient for time-sensitive functions or games). 

We can utilise the positional components of animations/tweens to create a slide-in transition. The following shows the code for a triple-bar slide in screen.


``` 
local TweenServ = game:GetService("TweenService")
local TInfo = TweenInfo.new(0.5,0)
local AnimFrame = script.Parent

local ExtraUDIM = UDim2.new(1,0,0,0)
local FramesToAnimate = {}

table.insert(FramesToAnimate, AnimFrame.TopSlide)
table.insert(FramesToAnimate, AnimFrame.MiddleSlide)
table.insert(FramesToAnimate, AnimFrame.BottomSlide)

-- Set starting positions
for _, frame in FramesToAnimate do
	frame.Position = frame.Position - ExtraUDIM
end

-- Create and play tweens
for _, frame in FramesToAnimate do
	local newTween = TweenServ:Create(frame, TInfo, {Position = (frame.Position + ExtraUDIM)})
	newTween:Play()
	wait(0.25)
end

wait(7.25)
for _, frame in FramesToAnimate do
	local newTween = TweenServ:Create(frame, TInfo, {Position = (frame.Position + ExtraUDIM)})
	newTween:Play()
	wait(0.25)
end
script.Parent:Destroy()   -- Destroy animation screen UI once completed
``` 

