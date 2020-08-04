# Day 4

## Overview 👀
Today we will write scripts that handle managing players and the state of the game.

## Follow Along! 👍
First, we will download today's file [here](https://education.roblox.com/assets/blt11fb316f976718ea/battleroyale_lesson5_final.rbxl?disposition=inline). Once downloaded, let's open it up in Roblox studio and begin our quest for today.

### Fun Quiz 🧠
> Here are some quiz questions that we will be able to answer as we complete today's instructions.

<details>
  <summary>In this battle royale, what are the two end-conditions?</summary>
  The match will reset when either there are less than two players remaining or when our timer runs out of time.
</details>

<details>
  <summary>In this battle royale, what should happen when a player is defeated?</summary>
  That player should be sent to the lobby.
</details>

<details>
  <summary>In this battle royale, what should happen when the match resets?</summary>
  The timer must be reset and all players (including the winner) must be sent back to the lobby with full health.
</details>

---

Then we will work on programming by following [these](https://education.roblox.com/en-us/resources/battle-royale/ending-matches) instructions.

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
local displayManager = require(moduleScripts:WaitForChild("DisplayManager"))
 
-- Events
local events = ServerStorage:WaitForChild("Events")
local matchEnd = events:WaitForChild("MatchEnd")
 
while true do
	displayManager.updateStatus("Waiting for Players")
 
	repeat
		wait(gameSettings.intermissionDuration)
	until Players.NumPlayers >= gameSettings.minimumPlayers
 
	displayManager.updateStatus("Get ready!")
	wait(gameSettings.transitionTime)
	
	matchManager.prepareGame()
	local endState = matchEnd.Event:Wait()
	print("Game ended with: " .. endState)
end
```

### `ServerStorage > ModuleScripts > MatchManager`
```lua
local MatchManager = {}
 
-- Services
local ServerStorage = game:GetService("ServerStorage")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
 
-- Module Scripts
local moduleScripts = ServerStorage:WaitForChild("ModuleScripts")
local playerManager = require(moduleScripts:WaitForChild("PlayerManager"))
local gameSettings = require(moduleScripts:WaitForChild("GameSettings"))
local timer = require(moduleScripts:WaitForChild("Timer"))
 
-- Events
local events = ServerStorage:WaitForChild("Events")
local matchStart = events:WaitForChild("MatchStart")
local matchEnd = events:WaitForChild("MatchEnd")
 
-- Values
local displayValues = ReplicatedStorage:WaitForChild("DisplayValues")
local timeLeft = displayValues:WaitForChild("TimeLeft")
 
-- Creates a new timer object to be used to keep track of match time. 
local myTimer = timer.new()
 
-- Local Functions
local function stopTimer()
	myTimer:stop()
end
 
local function timeUp()
	matchEnd:Fire(gameSettings.endStates.TimerUp)
end
 
local function startTimer()
	print("Timer started")
	myTimer:start(gameSettings.matchDuration)
	myTimer.finished:Connect(timeUp)	
 
	while myTimer:isRunning() do
		-- Adding +1 makes sure the timer display ends at 1 instead of 0. 
		timeLeft.Value = (math.floor(myTimer:getTimeLeft() + 1))
		-- By not setting the time for wait, it offers more accurate looping  
		wait()
	end
 
end
 
-- Module Functions
function MatchManager.prepareGame()
	playerManager.sendPlayersToMatch()
	matchStart:Fire()
end
 
matchStart.Event:Connect(startTimer)
matchEnd.Event:Connect(stopTimer)
 
return MatchManager
```

### `ServerStorage > ModuleScripts > GameSettings`
```lua
local GameSettings = {}
 
-- Game Variables
GameSettings.intermissionDuration = 5
GameSettings.matchDuration = 10
GameSettings.minimumPlayers = 2
GameSettings.transitionTime = 5
 
-- Possible ways that the game can end.
GameSettings.endStates = {
	TimerUp = "TimerUp",
	FoundWinner = "FoundWinner"
}
 
return GameSettings
```

### `ServerStorage > ModuleScripts > PlayerManager`
```lua
local PlayerManager = {}
 
-- Services
local Players = game:GetService("Players")
local ServerStorage = game:GetService("ServerStorage")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
 
-- Modules
local moduleScripts = ServerStorage:WaitForChild("ModuleScripts")
local gameSettings = require(moduleScripts:WaitForChild("GameSettings"))
 
-- Events
local events = ServerStorage:WaitForChild("Events")
local matchEnd = events:WaitForChild("MatchEnd")
 
-- Map Variables
local lobbySpawn = workspace.Lobby.StartSpawn
local arenaMap = workspace.Arena
local spawnLocations = arenaMap.SpawnLocations
 
-- Values
local displayValues = ReplicatedStorage:WaitForChild("DisplayValues")
local playersLeft = displayValues:WaitForChild("PlayersLeft")
 
-- Player Variables
local activePlayers = {}
local playerWeapon = ServerStorage.Weapon
 
-- Local Functions
local function checkPlayerCount()
	if #activePlayers == 1 then
		matchEnd:Fire(gameSettings.endStates.FoundWinner)
	end
end
 
local function removeActivePlayer(player)
	for playerKey, whichPlayer in pairs(activePlayers) do
		if whichPlayer == player then
			table.remove(activePlayers, playerKey)
			playersLeft.Value = #activePlayers
			checkPlayerCount()
		end
	end
end
 
local function respawnPlayerInLobby(player)
	player.RespawnLocation = lobbySpawn
	player:LoadCharacter()
end
 
 
local function onPlayerJoin(player)
	player.RespawnLocation = lobbySpawn
end
 
local function preparePlayer(player, whichSpawn)
	player.RespawnLocation = whichSpawn
	player:LoadCharacter()
 
	local character = player.Character or player.CharacterAdded:Wait()
	local sword = playerWeapon:Clone()
	sword.Parent = character
 
	local humanoid = character:WaitForChild("Humanoid")
 
	humanoid.Died:Connect(function()
		respawnPlayerInLobby(player)
		removeActivePlayer(player)
	end)
 
end
 
-- Module Functions 
function PlayerManager.sendPlayersToMatch()
	local arenaSpawns = spawnLocations:GetChildren()
 
	for playerKey, whichPlayer in pairs(Players:GetPlayers()) do
		table.insert(activePlayers,whichPlayer)	
		local spawnLocation = arenaSpawns[1]
		preparePlayer(whichPlayer, spawnLocation)
		table.remove(arenaSpawns, 1)
	end
 
	playersLeft.Value = #activePlayers
end
 
-- Events
Players.PlayerAdded:Connect(onPlayerJoin)
 
return PlayerManager
```

---

A complete example project of the scripting we have done in class today can be found [here](https://education.roblox.com/assets/blt03dc7fe967c0c5f3/battleroyale_lesson4_final.rbxl?disposition=inline).

## Optional Homework 📄
Download [this](https://education.roblox.com/assets/bltb5d7401ffa20b600/battleroyale_lesson6_final.rbxl?disposition=inline) file and open it in Roblox Studio. Publish it to Roblox (`Alt+P`) as your final Roblox Royale game, so name it something cool! Change the way that the arena looks by changing the colors, materials, and shapes of existing parts and by adding new parts from the toolbox.

---

See you tomorrow for day 5, our final session!

