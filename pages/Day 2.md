# Day 2

## Overview 👀
Today we are going to create the game loop by adding scripts that manage the game's players, starts and stops the game's timer feature, and other important mechanics.

## Follow Along! 👍
First, we will download today's file [here](https://education.roblox.com/assets/blt8fcd8f6d09c6c78e/roundBasedGame_lesson1_final.rbxl?disposition=inline). Once downloaded, let's open it up in Roblox studio and begin our quest for today.

### Fun Quiz 🧠
> Here are some quiz questions that we will be able to answer as we complete today's instructions.

<details>
  <summary>What is the difference between a normal script and a module script?</summary>
  Module scripts are not ran automatically; only when they are summoned (via the 'require' statement).
  Utilizing module scripts organizes your code in a clean, professional manner.
</details>

<details>
  <summary>What is the purpose of our GameManager script?</summary>
  It actually is responsible for running the game loop itself!
  It utilizes our MatchManager and GameSettings modules to run the loop.
</details>

<details>
  <summary>What is the purpose of our MatchManager  script?</summary>
  The MatchManager script contains functions that are responsible for sending players into an arena and keeping track of time in a match.
</details>

---

Then we will work on programming the basic functionality of our game's game loop by following [these](https://education.roblox.com/en-us/resources/battle-royale/coding-the-game-loop) instructions.

When we are done, our scripts should resemble the following samples:

### `ServerStorage > ModuleScripts > GameSettings`
```lua
local GameSettings = {}
 
-- Game Variables
GameSettings.intermissionDuration = 5
GameSettings.matchDuration = 10
GameSettings.minimumPlayers = 2
GameSettings.transitionTime = 5
 
return GameSettings
```

### `ServerStorage > ModuleScripts > MatchManager`
```lua
local MatchManager = {}
 
function MatchManager.prepareGame()
	print("Game starting!")
end
 
return MatchManager
```

### `ServerScriptService > GameManager`
```lua
-- Services
local ServerStorage = game:GetService("ServerStorage")
local Players = game:GetService("Players")
 
-- Module Scripts
local moduleScripts = ServerStorage:WaitForChild("ModuleScripts")
local matchManager = require(moduleScripts:WaitForChild("MatchManager"))
local gameSettings = require(moduleScripts:WaitForChild("GameSettings"))
 
while true do
	repeat
		wait(gameSettings.intermissionDuration)
		print("Restarting intermission")
	until Players.NumPlayers >= gameSettings.minimumPlayers
	print("Intermission over")
	wait(gameSettings.transitionTime)
	matchManager.prepareGame()
end
```

---

A complete example project of the scripting we have done in class today can be found [here](https://education.roblox.com/assets/blt7fffb3d25cb1a1dd/battleroyale_lesson2_final.rbxl?disposition=inline).

## Optional Homework 📄
> Read the following articles to acheive a full understanding of what we are working on for day 3.

* [Managing Players](https://education.roblox.com/en-us/resources/battle-royale/managing-players)

---

See you tomorrow for day 3!