# ModernSaveInstance

Modern saveinstance script for Roblox executors. Based on UniversalSynSaveInstance with structural improvements and bug fixes.

## Features

- Save Roblox places and models to .rbxlx / .rbxmx XML format
- Three modes: full, optimized, scripts
- Automatic API dump fetching with version matching
- Script decompilation with timeout and caching
- LinkedSource recovery from asset delivery
- NotScriptable property fallback chain (gethiddenproperty, UGCValidationService)
- Nil instances saving with fixes
- Anonymous mode to scrub user info
- Chunked file writing to handle large places

## Usage

### Basic

```lua
local saveinstance = loadstring(
    game:HttpGet("https://raw.githubusercontent.com/achmdfzn/ModernSaveInstance/main/saveinstance.lua", true),
    "saveinstance"
)()
saveinstance()
```

### Save as model

```lua
saveinstance(game.Workspace.Map)
```

### Custom options

```lua
saveinstance({
    mode = "optimized",
    SafeMode = true,
    Decompile = true,
    DecompileTimeout = 15,
    IgnoreList = { "CoreGui" },
    NilInstances = false,
})
```

### Save with callback

```lua
saveinstance({
    Callback = function(xml, chunks, totalSize)
        print("Saved " .. totalSize .. " bytes to callback")
    end,
})
```

## Options

| Option | Type | Default | Description |
|--------|------|---------|-------------|
| mode | string | "optimized" | Save mode: full, optimized, scripts |
| SafeMode | boolean | true | Kicks player before saving for safety |
| Decompile | boolean | true | Decompile scripts |
| DecompileTimeout | number | 10 | Max seconds for decompilation |
| SaveBytecode | boolean | false | Include bytecode in output |
| IgnoreSharedStrings | boolean | true | Skip SharedString properties (crash fix) |
| IgnoreList | table | {CoreGui, CorePackages} | Instances/classes to skip |
| IgnoreProperties | table | {} | Property names to skip |
| NilInstances | boolean | false | Save unparented instances |
| FilePath | string | false | Custom output filename |
| Object | Instance | false | Save as .rbxmx model |
| Callback | function | false | Receive XML data instead of file write |

## Notes

- Requires executor with writefile support (or use Callback option)
- gethiddenproperty recommended for complete property serialization
- Internet connection needed for API dump fetching
