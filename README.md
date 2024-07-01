# LuaSPTLib
A Lua Library, designed for Lucas' Simpsons Hit & Run Mod Launcher, to allow for parsing and modifying SPT files.

# Installation
1. Download the latest `SPT.zip` from https://github.com/Hampo/LuaSPTLib/releases.
2. Extact the contents to the `/Resources/lib` folder of your mod.
3. Add `dofile(GetModPath() .. "/Resources/lib/SPTParser.lua")` to the `CustomFiles.lua` of your mod.
4. Add the following Authors to your `Meta.ini`:
```ini
[Author]
Name=Proddy
Notes=SPT parser and class system
Group=SPTParser
Credits=1

[Author]
Name=EnAppelsin
Website=https://github.com/EnAppelsin
Notes=Type Info format
Group=SPTParser
Credits=1
```

# Example
You can find complete mods... Nowhere because I haven't made any yet.

## Code examples

### Fixing the Audi TT invalid engine sound
1. Add `RequiredHack=FileSystemRCFs` to your `Meta.ini`.
  - *This is required because the sound scripts are embedded in `.rcf` files, and to `ReadFile` them you must extract the files to the Virtual File System.*
2. Add a `PathHandler` to your `CustomFiles.ini` for `sound/scripts/car_tune.spt`.
3. In the `PathHandler`'s Lua file, use the following:
```lua
-- Get the game paths
local Path = GetPath()
local GamePath = "/GameData/" .. Path

-- Load the SPT file
local SPT = SPTParser.SPTFile(GamePath)

-- Loop through all `carSoundParameters`
for carSoundParameters in SPT:GetClasses("carSoundParameters") do
	-- Loop all `SetEngineClipName` methods
	for SetEngineClipName in carSoundParameters:GetMethods("SetEngineClipName") do
		--- If the filename is `tt` (which doesn't exist), replace with `apu_car`
		if SetEngineClipName.Parameters[1] == "tt" then
			SetEngineClipName.Parameters[1] = "apu_car"
		end
	end
	
	-- Repeat for `SetEngineIdleClipName`
	for SetEngineIdleClipName in carSoundParameters:GetMethods("SetEngineIdleClipName") do
		--- If the filename is `tt` (which doesn't exist), replace with `apu_car`
		if SetEngineIdleClipName.Parameters[1] == "tt" then
			SetEngineIdleClipName.Parameters[1] = "apu_car"
		end
	end
end

-- Output the modifications to the game
Output(tostring(SPT))
```