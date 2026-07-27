# ModernSaveInstance

Fork of [UniversalSynSaveInstance](https://github.com/luau/UniversalSynSaveInstance) with decompilation prepass & optimizations.

**Created by Sashenka** — [Discord](https://discord.gg/QjRZrbSUeV)

## Files

| File | Description |
|---|---|
| `saveinstance.luau` | Core saveinstance engine (serializer + options) |
| `prepass.luau` | Decompilation prepass + auto-loads saveinstance |

## Usage

### Basic (saveinstance only)

```lua
local s = loadstring(game:HttpGet("https://raw.githubusercontent.com/achmdfzn/ModernSaveInstance/main/saveinstance.luau"))()
s({ SafeMode = true })
```

### With PrePass (recommended)

PrePass caches decompiled scripts first, then loads saveinstance — avoids timeout on large games.

```lua
local p = loadstring(game:HttpGet("https://raw.githubusercontent.com/achmdfzn/ModernSaveInstance/main/prepass.luau"))()
p({ SafeMode = true })
```

### PrePass only (skip saveinstance)

```lua
local p = loadstring(game:HttpGet("https://raw.githubusercontent.com/achmdfzn/ModernSaveInstance/main/prepass.luau"))()
p(nil, { SkipSaveInstance = true })
```

### PrePass options

| Option | Type | Default | Description |
|---|---|---|---|
| `SkipPrepass` | bool | false | Skip decompilation prefetch |
| `SkipSaveInstance` | bool | false | Skip loading saveinstance after prepass |
| `RequestsPerMinute` | number | 1400 | Decompile API rate limit |
| `MaxInFlight` | number | 30 | Parallel decompile requests |
| `RequestTimeout` | number | 20 | Per-request timeout (seconds) |
| `ApiUrl` | string | `https://api.lua.expert/decompile` | Decompilation API endpoint |
| `UssiRepoURL` | string | *(this repo)* | Where to fetch `saveinstance.luau` |
| `UssiScript` | string | `saveinstance` | Script name (`.luau` appended) |
| `Verbose` | bool | true | Log progress to console |

### saveinstance Options

All [USSI options](https://luau.github.io/UniversalSynSaveInstance/api/SynSaveInstance) are supported, including:

- `SafeMode` — Most stable mode (recommended for beginners)
- `DecompileTimeout` / `BytecodeTimeout` — Timeout guards
- `IgnoreSharedStrings` — Skip SharedString processing
- `TreatUnionsAsParts` — Convert UnionOperations to Parts (Solara auto-enabled)
- `IsModel` — Save as `.rbxmx` instead of `.rbxlx`
- `FilePath` — Custom output path (auto-appends extension)
- `ExtraInstances` — Save additional non-`game` instances

Full list: see `--- @field` declarations in `saveinstance.luau` or the [USSI docs](https://luau.github.io/UniversalSynSaveInstance/api/SynSaveInstance).

## Optimizations

- **Iterative stack machine** — replaces recursive tree walk, no stack overflow on deep hierarchies
- **Appendfile direct write** — writes XML incrementally instead of accumulating in memory
- **GetInheritedProps chain-of-arrays** — faster property inheritance lookup via `table.move`
- **Auto-update** — checks VERSION file on GitHub at load
- **Weak referents table** — prevents memory leaks
