---
"effect": patch
---

`Prompt.file`: give the `filter` predicate enough context to filter entries reliably, closes #3393.

The `filter` callback now receives a `FileFilterInfo` object (`{ name, path, type }`) instead of the bare entry name. `path` is the fully resolved absolute path of the entry and `type` is its `FileSystem.File.Type`, so filters can distinguish files from directories without manually `stat`ing (which previously did not work, since the callback only received a basename with no directory context). The synthetic `".."` parent-directory entry is reported with `type: "Directory"`, making it straightforward to keep directory traversal available while restricting which files may be selected:

```ts
import { Prompt } from "effect/unstable/cli"

const xmlFile = Prompt.file({
  message: "Please select one XML file",
  filter: (entry) => entry.type === "Directory" || entry.name.endsWith(".xml")
})
```
