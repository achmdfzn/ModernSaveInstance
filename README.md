# ModernSaveInstance

Modern saveinstance script for Roblox executors. Based on UniversalSynSaveInstance.

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

### Custom filename
```lua
saveinstance({ FilePath = "MyPlace" })
```

### Decompile options
```lua
saveinstance({
    Decompile = true,
    DecompileTimeout = 15,
    SaveBytecode = false,
})
```

### Ignore instances
```lua
saveinstance({
    IgnoreList = { "CoreGui", "Players" },
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

### Callback (no file write)
```lua
saveinstance({
    Callback = function(xml, chunks, totalSize)
        print("Size: " .. totalSize)
    end,
})
```
