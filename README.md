# ModernSaveInstance

Modern saveinstance script for Roblox executors. Based on UniversalSynSaveInstance with structural fixes.

## Usage

### Load and run

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

### Save to file with custom name

```lua
saveinstance({
    FilePath = "MyPlace"
})
```

### Decompile settings

```lua
saveinstance({
    Decompile = true,
    DecompileTimeout = 15,
    SaveBytecode = true,
})
```

### Skip instances or properties

```lua
saveinstance({
    IgnoreList = { "CoreGui", "Players", workspace.Part },
    IgnoreProperties = { "CFrame", "Color" },
})
```

### Nil instances

```lua
saveinstance({
    NilInstances = true,
})
```

### Anonymous mode

```lua
saveinstance({
    Anonymous = true,
})
```

### Save without writing file (use callback)

```lua
saveinstance({
    Callback = function(xml, chunks, totalSize)
        print("Data size: " .. totalSize)
    end,
})
```

## Notes

- Supports executors with writefile, gethiddenproperty, decompile
- Uses chunked writing for large places
- Auto-fetches API dump matching your Roblox version
