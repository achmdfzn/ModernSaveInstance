# ModernSaveInstance

Fork of [UniversalSynSaveInstance](https://github.com/luau/UniversalSynSaveInstance) with a decompilation **prepass** system.

## Files

| File | Description |
|------|-------------|
| `saveinstance.luau` | Core saveinstance engine (upstream USSI) |
| `prepass.luau` | Decompilation prepass — prefetches & caches script decompiles via API, then runs saveinstance |

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

PrePass fetches all client-side scripts via an external decompile API before saving, so saveinstance uses cached results instead of in-executor decompile.

```lua
local prepass = loadstring(
    game:HttpGet("https://raw.githubusercontent.com/achmdfzn/ModernSaveInstance/main/prepass.luau", true),
    "prepass"
)()

-- Runs prepass + saves with options
prepass({ SafeMode = true })
```

### PrePass with custom config

```lua
local prepass = loadstring(
    game:HttpGet("https://raw.githubusercontent.com/achmdfzn/ModernSaveInstance/main/prepass.luau", true),
    "prepass"
)()

prepass(
    { SafeMode = true, mode = "optimized" },   -- saveinstance options
    {
        RequestsPerMinute = 800,                -- API rate limit
        MaxInFlight = 20,                       -- parallel requests
        SkipPrepass = false,                     -- skip prefetch
        Verbose = true,
    }
)
```

### PrePass only (skip saveinstance)

```lua
prepass(nil, { SkipSaveInstance = true })
```

## Options

See the [USSI docs](https://luau.github.io/UniversalSynSaveInstance/api/SynSaveInstance) for all `saveinstance` options.

Key options:

| Option | Type | Default | Description |
|--------|------|---------|-------------|
| `SafeMode` | bool | `true` | Kicks you before saving (safe) |
| `mode` | string | `"optimized"` | `"full"`, `"optimized"`, or `"scripts"` |
| `Decompile` | bool | `true` | Enable decompilation |
| `IgnoreList` | table | `{CoreGui, CorePackages}` | Instances to skip |
| `NilInstances` | bool | `false` | Save unparented instances |
| `Object` | Instance | `false` | Save as `.rbxmx` (model) |
