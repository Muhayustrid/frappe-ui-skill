---
name: frappe-ui
description: Build consistent Frappe-style user interfaces using the frappe-ui Vue 3 component library and its design tokens. Use when scaffolding pages, forms, dialogs, lists, or any UI inside a Frappe-based app, when the user mentions frappe-ui, Frappe Cloud / Gameplan / Desk / Drive / Insights styling, or asks to "use frappe-ui components".
---

# frappe-ui

Build UIs that look and feel like Frappe products by composing **frappe-ui** components and styling with the library's **semantic Tailwind tokens**.

## Quick start

```vue
<script setup>
import { Button, Dialog, FormControl } from 'frappe-ui'
import { ref } from 'vue'

const open = ref(false)
const name = ref('')
</script>

<template>
  <div class="p-4 bg-surface-base text-ink-gray-8">
    <Button variant="solid" theme="gray" @click="open = true">New Task</Button>
    <Dialog v-model:open="open" title="Create Task">
      <FormControl v-model="name" label="Title" required />
    </Dialog>
  </div>
</template>
```

## Where to look

Prop names, prop value types, slot names and token names live in these files, not here. Open the one you need before you write markup.

| Writing this | Open this first |
|---|---|
| picking a component; "does this library have…?" | [INDEX.md](INDEX.md) |
| any component, any page, any Tailwind class | [CORE.md](CORE.md) |
| anything that fetches or writes data | [DATA.md](DATA.md) |
| a fresh Vite + Vue 3 project | [SETUP.md](SETUP.md) |

**Version.** These files describe frappe-ui **1.x**. If the app pins
`frappe-ui@0.1.x` (many ERPNext and older Frappe apps still do), most names
here do not exist there — read that version's own `node_modules/frappe-ui` and
its README, or migrate first. Check `package.json` before the first import.

## Contracts

These hold across components. Everything else is per-component, and lives in
[CORE.md](CORE.md).

1. **Pick the component.** Every interactive element comes from `CORE.md`. Raw
   HTML is for layout: grids, flex, spacing wrappers.
2. **Color is two props: `variant` and `theme`.** The value sets differ per
   component — `CORE.md` lists each one. In your own markup, colors come from
   the semantic scales: `bg-surface-*`, `text-ink-*`, `border-outline-*`.
3. **Two-way state is `v-model`.** Inputs take `v-model`. Overlays take
   `v-model:open`. `Combobox` and `MultiSelect` add optional `v-model:query`.
   `List` adds `v-model:selection` and `v-model:active`.
4. **Icons are CSS classes.** `<span class="lucide-edit size-4" aria-hidden="true" />`.
   A prop named `icon` takes the string `"lucide-edit"`.
5. **Slot names are per component, never universal.** Read the component's
   entry in `CORE.md` before writing a `<template #…>`.
6. **Frappe API calls go through the `use*` composables** — `useCall`,
   `useList`, `useDoc`, `useDoctype`, `useNewDoc`, all exported from
   `frappe-ui`. Each one takes its own option set and returns its own shape;
   [DATA.md](DATA.md) lists them separately.

## Install in another harness

One directory, plain `SKILL.md` + Markdown, no harness-specific syntax, so the
same folder works everywhere. It lives in its own repository, so any harness
can clone it:

```bash
git clone https://github.com/Muhayustrid/frappe-ui-skill.git ~/frappe-ui-skill

ln -s ~/frappe-ui-skill/skills/frappe-ui ~/.agents/skills/frappe-ui          # DSH, Claude Code, Codex
ln -s ~/frappe-ui-skill/skills/frappe-ui ~/.claude/skills/frappe-ui          # Claude Code
ln -s ~/frappe-ui-skill/skills/frappe-ui ~/.codex/skills/frappe-ui           # Codex
ln -s ~/frappe-ui-skill/skills/frappe-ui ~/.config/opencode/skills/frappe-ui # opencode
```

DeepSeek Harness also discovers a project's `.agents/skills` root and Claude
Code a project's `.claude/skills`, so a per-repo symlink points at the same
folder. Keep the reference files next to `SKILL.md`: relative links are how an
agent finds them, and no harness resolves them any other way.
