# ModernSaveInstance

Fork of [UniversalSynSaveInstance](https://github.com/luau/UniversalSynSaveInstance) with decompilation prepass.

**Created by Sashenka** — [Discord](https://discord.gg/QjRZrbSUeV)

## Files

- `saveinstance.luau` — Core saveinstance engine
- `prepass.luau` — Decompilation prepass + saveinstance runner

## Usage

### Basic

```lua
	local s = loadstring(game:HttpGet("https://raw.githubusercontent.com/achmdfzn/ModernSaveInstance/main/saveinstance.luau"))()
s({ SafeMode = true })
```

### With PrePass (recommended)

```lua
local p = loadstring(game:HttpGet("https://raw.githubusercontent.com/achmdfzn/ModernSaveInstance/main/prepass.luau"))()
p({ SafeMode = true })
```

### PrePass only (skip saveinstance)

```lua
p(nil, { SkipSaveInstance = true })
```

## Options

All [USSI options](https://luau.github.io/UniversalSynSaveInstance/api/SynSaveInstance) supported.
