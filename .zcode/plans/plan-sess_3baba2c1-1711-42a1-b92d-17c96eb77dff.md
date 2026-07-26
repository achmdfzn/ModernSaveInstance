## Selective Merge: Upstream Fixes + Our Optimizations

### Overview
Ada ~535 baris perbedaan antara versi kita (yg sudah dioptimasi) dengan upstream terbaru (`luau/UniversalSynSaveInstance`). Kita selective merge — ambil perbaikan upstream tanpa menghapus 3 optimasi kita.

### What We Keep (Our Optimizations)
1. **Iterative `save_hierarchy`** — upstream masih recursive, kita pertahankan
2. **`GetInheritedProps` chain-of-arrays** — upstream pakai `table.move`, kita pertahankan
3. **Appendfile final write** — upstream concat chunk dulu, kita langsung `appendfile`

### What We Merge (Upstream Fixes)

**Critical — bug/crash fixes:**
1. **Referents weak table** (`setmetatable({}, { __mode = "ks" })`) — cegah memory leak
2. **`__BREAK` moved earlier** + NotScriptableFixes baru:
   - `VertexCount` di MeshPart (ambil data mesh)
   - `InternalBodyScale`/`InternalHeadScale` pake `__BREAK` guard (cegah error kalo part hilang)
   - `NetworkHumanoidState` (simpan state humanoid)
3. **`isFullDump` + fallback GitHub CDN** — API dump lebih reliable
4. **`BytecodeTimeout` option + `makeTimeoutHandler` worker pool** — timeout lebih stabil
5. **`IgnoreSharedStrings` default false** — upstream fix
6. **`TreatUnionsAsParts` only Solara** — tidak blanket untuk Fluxus/Delta
7. **`Content` or `AssetContentMap`** check — filter properti lebih akurat
8. **`Region3Int16` → `Region3int16`** mapping

**Important — feature improvements:**
9. **`FilePath` extension handling** — auto-detect `.rbxlx`/`.rbxmx` based on IsModel
10. **Option aliases**: `DecompileScripts`, `SaveNilInstances`, `StatusText`
11. **Loading spinner refactored** ke `ensureSpinner` pattern (lebih clean)
12. **`scriptcache` → `USSI_scriptcache`** namespace

### Files Affected
- **`saveinstance.luau`** — selective merge
- **`prepass.luau`** — mungkin update `scriptcache` → `USSI_scriptcache`

### Execution Strategy
Apply changes in order (each commit-able):
1. Prerequisite: `__BREAK`, weak referents, namespace changes
2. NotScriptableFixes: VertexCount, BodyScale, HeadScale, NetworkHumanoidState
3. API dump: isFullDump, fallback CDN
4. Options: BytecodeTimeout, makeTimeoutHandler, new aliases
5. Remaining upstream fixes (FilePath, Content/AssetContentMap, Region3Int16)
6. Loading spinner refactor
7. Final: verify output identical for same input