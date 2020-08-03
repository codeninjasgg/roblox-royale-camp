# Day 3

## Overview 👀
Today we will utilize *BindableEvents* and script out the logic for our game's timer.

## Follow Along! 👍
First, we will download today's file [here](https://education.roblox.com/assets/bltf337a667bbe03989/battleroyale_lesson3_final.rbxl?disposition=inline). Once downloaded, let's open it up in Roblox studio and begin our quest for today.

### Fun Quiz 🧠
> Here are some quiz questions that we will be able to answer as we complete today's instructions.

<details>
  <summary>What does 'WaitForChild' do?</summary>
  The method 'WaitForChild' is used by variables that represent folders in our explorer. This method allows us to reach into the folder that it's instance represents and pick a file programmatically.
</details>

<details>
  <summary>TBD</summary>
  TBD
</details>

---

Then we will work on programming by following [these](https://education.roblox.com/en-us/resources/battle-royale/timers-and-custom-events) instructions.

When we are done, our scripts should resemble the following samples:

### `ServerScriptService > GameManager`
```lua
-- Services
local ServerStorage = game:GetService("ServerStorage")
local Players = game:GetService("Players")
 
-- Module Scripts
local moduleScripts = ServerStorage:WaitForChild("ModuleScripts")
local matchManager = require(moduleScripts:WaitForChild("MatchManager"))
local gameSettings = require(moduleScripts:WaitForChild("GameSettings"))
 
-- Events
local events = ServerStorage:WaitForChild("Events")
local matchEnd = events:WaitForChild("MatchEnd")
 
while true do
	repeat
		wait(gameSettings.intermissionDuration)
		print("Restarting intermission")
	until Players.NumPlayers >= gameSettings.minimumPlayers
 
	print("Intermission over")
	wait(gameSettings.transitionTime)
	
	matchManager.prepareGame()
	-- Placeholder wait for the length of the game.
	matchEnd.Event:Wait()
 
end
```

### `ServerStorage > ModuleScripts > MatchManager`
```lua
local MatchManager = {}
 
-- Services
local ServerStorage = game:GetService("ServerStorage")
 
-- Module Scripts
local moduleScripts = ServerStorage:WaitForChild("ModuleScripts")
local playerManager = require(moduleScripts:WaitForChild("PlayerManager"))
local gameSettings = require(moduleScripts:WaitForChild("GameSettings"))
local timer = require(moduleScripts:WaitForChild("Timer"))
 
-- Events
local events = ServerStorage:WaitForChild("Events")
local matchStart = events:WaitForChild("MatchStart")
local matchEnd = events:WaitForChild("MatchEnd")
 
-- Creates a new timer object to be used to keep track of match time. 
local myTimer = timer.new()
 
-- Local Functions
local function timeUp()
	print("Time is up!")
end
 
local function startTimer()
	print("Timer started")
	myTimer:start(gameSettings.matchDuration)
	myTimer.finished:Connect(timeUp)	
end
 
-- Module Functions
function MatchManager.prepareGame()
	playerManager.sendPlayersToMatch()
	matchStart:Fire()
end
 
matchStart.Event:Connect(startTimer)
 
return MatchManager
```

### `ServerStorage > ModuleScripts > Timer`
```lua
local Timer = {}
Timer.__index = Timer
 
function Timer.new()
	local self = setmetatable({}, Timer)
 
	self._finishedEvent = Instance.new("BindableEvent")
	self.finished = self._finishedEvent.Event
	
	self._running = false
	self._startTime = nil
	self._duration = nil
	
	return self
end
 
function Timer:start(duration)
	if not self._running then
		local timerThread = coroutine.wrap(function()
			self._running = true
			self._duration = duration
			self._startTime = tick()
			while self._running and tick() - self._startTime < duration do
				wait()
			end
			local completed = self._running
			self._running = false
			self._startTime = nil
			self._duration = nil
			self._finishedEvent:Fire(completed)
		end)
		timerThread()
	else
		warn("Warning: timer could not start again as it is already running.")
	end
end
 
function Timer:getTimeLeft()
	if self._running then
		local now = tick()
		local timeLeft = self._startTime + self._duration - now
		if timeLeft < 0 then
			timeLeft = 0
		end
		return timeLeft
	else
		warn("Warning: could not get remaining time, timer is not running.")
	end
end
 
function Timer:isRunning()
	return self._running
end
 
function Timer:stop()
	self._running = false
end
 
return Timer
```

---

A complete example project of the scripting we have done in class today can be found [here](https://education.roblox.com/assets/blt03dc7fe967c0c5f3/battleroyale_lesson4_final.rbxl?disposition=inline).

## Optional Homework 📄
> Read the following articles to acheive a full understanding of what we are working on for day 4.

* [Creating a GUI (Graphical User Interface)](https://education.roblox.com/en-us/resources/battle-royale/displaying-information-with-gui): [Complete](https://education.roblox.com/assets/blt11fb316f976718ea/battleroyale_lesson5_final.rbxl?disposition=inline)

---

See you tomorrow for day 3!

