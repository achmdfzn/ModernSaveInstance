## Plan: 3 Optimasi Algoritma di `saveinstance.luau`

Berdasarkan analisis, dari 6 rekomendasi awal, **3 yang berdampak nyata**:

---

### 1. `save_hierarchy` Rekursif → Iterative Stack (High Impact)

**Masalah:** Fungsi traversal instance tree (baris 3470–3853) **rekursif murni**. Tree bisa ribuan node (DataModel penuh) → **stack overflow** di executor dengan C-stack terbatas.

**Solusi:** Explicit stack + state machine.

```
stack = { { list = root, idx = 1 } }
while #stack > 0 do
    top = stack[#stack]
    
    -- Close marker: tulis </Item> dan pop
    if top.closing then
        stack[#stack] = nil
        savebuffer[++savebuffer_size] = "</Item>"
        continue
    end
    
    -- Semua instance di level ini selesai
    if top.idx > #top.list then
        stack[#stack] = nil
        continue
    end
    
    instance = top.list[top.idx++]
    -- ... proses instance (logika eksklusi, ReturnItem, properties) ...
    
    children = (instance override children) or instance:GetChildren()
    if #children > 0 and not skip then
        stack[#stack + 1] = { closing = true }   -- tulis </Item> setelah children
        stack[#stack + 1] = { list = children, idx = 1 }
    else
        savebuffer[++savebuffer_size] = "</Item>"
    end
end
```

**Perubahan:** Hanya body fungsi `save_hierarchy`. Semua logika filtering, property serialization, `DecompileIgnoring`, `SaveCacheInterval` tetap identik. Tidak ada perubahan output.

### 2. Final Write — Hindari String Concat Raksasa (Medium Impact)

**Masalah:** Di akhir (baris 4088–4092), semua chunk digabung jadi 1 string:
```lua
local totalstr = header
for _, chunk in chunks do totalstr ..= chunk.str end  -- O(n²) memory, spike >500MB
```

**Solusi:** Tulis header dulu via `writefile`, lalu `appendfile` tiap chunk.

```lua
writefile(placename, header)
for _, chunk in chunks do
    appendfile(placename, chunk.str)
end
```

Untuk executor tanpa `appendfile`, fallback ke `AlternativeWritefile` (segmented appendfile) atau concat lama.

### 3. `GetInheritedProps` — Hindari `table.move` Redundan (Low Impact)

**Masalah:** `table.move` menyalin semua properti parent ke child list (baris 3405). Overhead **O(total_properties)** per class pertama kali diakses. Dengan caching, ini cuma sekali per class, tapi bisa dihindari.

**Solusi:** Simpan chain of arrays, iterasi nested loop saat properti dibaca.

```lua
-- Sebelum: copy semua parent props ke flat array
-- Sesudah: iterate parent chain langsung
local function GetInheritedProps(className)
    local cached = inherited_properties[className]
    if cached then return cached end
    local chain = {}
    local layer = ClassList[className]
    while layer do
        chain[#chain + 1] = layer.Properties
        layer = ClassList[layer.Superclass]
    end
    inherited_properties[className] = chain
    return chain
end

-- Iterasi jadi nested loop di save_hierarchy
for _, layer_props in GetInheritedProps(className) do
    for _, Property in layer_props do
        -- ... existing logic ...
    end
end
```

---

### Files affected
Hanya **`saveinstance.luau`** — 3 perubahan. Tidak sentuh `prepass.luau`.

### Testing
- Output file harus identik sebelum/sesudah (byte-for-byte untuk input yang sama)
- Tidak ada error baru dari panggilan `GetChildren()`, `save_cache`, atau property reading
