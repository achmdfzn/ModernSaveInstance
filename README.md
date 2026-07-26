# ModernSaveInstance

Fork of [UniversalSynSaveInstance](https://github.com/luau/UniversalSynSaveInstance) with decompilation prepass.

## Files

- `saveinstance.luau` — Core saveinstance engine (optimized)
- `prepass.luau` — Prefetch script decompiles via API, then runs saveinstance

## Usage

### Basic

```lua
local synsaveinstance = loadstring(
    game:HttpGet("https://raw.githubusercontent.com/achmdfzn/ModernSaveInstance/main/saveinstance.luau", true),
    "saveinstance"
)()

synsaveinstance({ SafeMode = true })
```

### With PrePass (recommended)

```lua
local prepass = loadstring(
    game:HttpGet("https://raw.githubusercontent.com/achmdfzn/ModernSaveInstance/main/prepass.luau", true),
    "prepass"
)()

prepass({ SafeMode = true })
```

### PrePass with custom config

```lua
prepass(
    { SafeMode = true, mode = "optimized" },   -- saveinstance options
    {
        RequestsPerMinute = 800,
        MaxInFlight = 20,
        SkipPrepass = false,
    }
)
```

### PrePass only (skip saveinstance)

```lua
prepass(nil, { SkipSaveInstance = true })
```

## Options

See [USSI docs](https://luau.github.io/UniversalSynSaveInstance/api/SynSaveInstance) for all options.
