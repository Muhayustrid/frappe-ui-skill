<!-- Upstream: https://github.com/frappe/frappe-ui/blob/b435c66b51c0b19425a2a049b43e3ef2e308a8ab/src/components/KeyboardShortcut/KeyboardShortcut.md -->

# KeyboardShortcut

Shows a key combination, such as `Mod+K`, as keys. It only shows the
shortcut: to make the shortcut work, use
[`useKeyboardShortcut`](../other/composables.md#usekeyboardshortcut).



## Examples

### Search button

The shortcut that opens search, at the end of a search button.

```vue
<script setup>
import { KeyboardShortcut } from 'frappe-ui'
</script>

<template>
  <button
    type="button"
    class="flex h-7 w-60 items-center gap-2 rounded-4 bg-surface-gray-2 px-2 text-base text-ink-gray-5 hover:bg-surface-gray-3"
  >
    <span class="lucide-search size-4" aria-hidden="true" />
    <span class="flex-1 text-left">Search</span>
    <KeyboardShortcut combo="Mod+K" />
  </button>
</template>
```

### Command palette hints

Hints at the bottom of a command palette. `bg` draws each key as a chip, and
`alt-combos` lists another combo that does the same thing.

```vue
<script setup>
import { KeyboardShortcut } from 'frappe-ui'

const hints = [
  { combo: 'ArrowUp', altCombos: ['ArrowDown'], label: 'to navigate' },
  { combo: 'Enter', label: 'to open' },
  { combo: 'Escape', label: 'to close' },
]
</script>

<template>
  <div
    class="flex w-full max-w-md items-center gap-4 rounded-6 border border-outline-gray-2 px-3 py-2 text-sm text-ink-gray-5"
  >
    <div
      v-for="hint in hints"
      :key="hint.combo"
      class="flex items-center gap-1.5"
    >
      <KeyboardShortcut
        :combo="hint.combo"
        :alt-combos="hint.altCombos"
        bg
      />
      <span>{{ hint.label }}</span>
    </div>
  </div>
</template>
```

### Task menu

Each menu row ends with its shortcut. On the delete row, `alt-combos` adds
`Delete` as an alternative to `Mod+Backspace`.

```vue
<script setup>
import { KeyboardShortcut } from 'frappe-ui'

const rows = [
  { label: 'Edit', icon: 'lucide-pencil', combo: 'E' },
  { label: 'Duplicate', icon: 'lucide-copy', combo: 'Mod+D' },
]
</script>

<template>
  <div
    class="w-64 rounded-6 bg-surface-elevation-2 p-1.5 text-base shadow-2xl ring-1 ring-outline-gray-1"
  >
    <div
      v-for="row in rows"
      :key="row.label"
      class="flex items-center justify-between rounded-4 px-2 py-1.5 text-ink-gray-7 hover:bg-surface-gray-3"
    >
      <span class="flex items-center gap-2">
        <span :class="row.icon" class="size-4" aria-hidden="true" />
        {{ row.label }}
      </span>
      <KeyboardShortcut :combo="row.combo" />
    </div>
    <div
      class="flex items-center justify-between rounded-4 px-2 py-1.5 text-ink-red-7 hover:bg-surface-red-1"
    >
      <span class="flex items-center gap-2">
        <span class="lucide-trash-2 size-4" aria-hidden="true" />
        Delete task
      </span>
      <KeyboardShortcut
        combo="Mod+Backspace"
        :alt-combos="['Delete']"
      />
    </div>
  </div>
</template>
```

## Behavior

### Key names

`Mod` shows `⌘` on macOS and `Ctrl` elsewhere.

`combo` reads the same key names as `useKeyboardShortcut`, so a combo written
for that composable renders here unchanged: `Digit1` shows `1`, `Slash` shows
`/`, `Backtick` shows `` ` ``.

It reads **only** those names. `combo` is typed `string`, because callers
usually compute it, so the check happens at runtime: an unknown key name
renders as written and warns once in development. Write `Mod+K`, not `cmd+k`.
Import the `KeyboardShortcutCombo` type to get the check at compile time where
the value is known.

### Icons

`useIcons` controls the icons on the arrow, Enter, Backspace and Delete keys, in
both `bg` and plain mode. ⌘ is always an icon. Plain mode draws Shift and Alt
as icons, and `bg` mode draws them as text.

In plain mode, a `+` appears only between two keys drawn as text, as in
"Ctrl+K". A key drawn as an icon needs none, so `Mod+K` shows "⌘K" on macOS.
`bg` mode draws no `+`.

### Alternative combos

`altCombos` lists other combos after a `/`. A combo that shows the same keys as
the main combo, or as an earlier alternative, is left out.

### Without a combo

With no `combo`, the component renders its default slot.

## Accessibility

Each key is a picture of a key, so the root is a labelled `role="img"` that
spells the whole sequence, for example "Shortcut Control + Backspace, or
Delete". That role replaces everything inside it, so a screen reader reads
each key once instead of once per chip. With no `combo`, the root has no role,
and the default slot reads normally.

## API reference

```ts
const propsData = [
  {
    name: 'bg',
    description: 'Background chip style.',
    required: false,
    type: 'boolean'
  },
  {
    name: 'combo',
    description: 'Shortcut combo string, e.g. `"Mod+Shift+K"`. `Mod` resolves to Cmd on macOS, Ctrl elsewhere.',
    required: false,
    type: 'string'
  },
  {
    name: 'showPlus',
    description: 'Draw `+` between keys. `\'auto\'` draws it only between two text keys: "Ctrl+K", but "⌘K" and "⇧F2". Plain mode only.',
    required: false,
    type: 'boolean | "auto"',
    default: '"auto"'
  },
  {
    name: 'altCombos',
    description: 'Alternative equivalent combos, rendered after a `/` separator (display only).',
    required: false,
    type: 'string[]',
    default: '[]'
  },
  {
    name: 'useIcons',
    description: 'Render icons for the arrow, Enter, Backspace and Delete keys.',
    required: false,
    type: 'boolean',
    default: 'true'
  }
]

  const slotsData = [
  {
    name: 'default',
    description: 'Fallback content shown when no `combo` is given.',
    type: 'any'
  }
]
```
