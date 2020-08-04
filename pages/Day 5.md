# Day 5

## Overview 👀
Today we will customize our battle royale game, publish it, and play each others' games in random order.

## Follow Along! 👍

### First Half
Download [this](https://education.roblox.com/assets/bltb5d7401ffa20b600/battleroyale_lesson6_final.rbxl?disposition=inline) file and open it in Roblox Studio. Publish it to Roblox (`Alt+P`) as your final Roblox Royale game, so name it something cool! Change the way that the arena looks by changing the colors, materials, and shapes of existing parts and by adding new parts from the toolbox.

### Second Half
Let's open the `GameSettings` script (`ServerStorage > ModuleScripts > GameSettings`) and update a variable:
```lua
GameSettings.matchDuration = 180
```
We are changing the match duration variable from 10 seconds to 180 seconds. 180 seconds was calculated by taking the amount of seconds in one minute (60) and multiplying it by 3 to get the amount of seconds there are in 3 minutes.

Then, let's save our game one last time by publishing to Roblox (`Alt+P`). Then, let's open up the *Game Settings* in Roblox Studio to change our game to *public* so that we can play each other's game. Next, everybody opens roblox.com.