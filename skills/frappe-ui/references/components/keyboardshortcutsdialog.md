<!-- Upstream: https://github.com/frappe/frappe-ui/blob/b435c66b51c0b19425a2a049b43e3ef2e308a8ab/src/components/KeyboardShortcutsDialog/KeyboardShortcutsDialog.md -->

# KeyboardShortcutsDialog

A dialog that lists every shortcut registered with
[`useKeyboardShortcut`](../other/composables.md#usekeyboardshortcut), in groups,
with search.

```vue
<script setup lang="ts">
import { ref } from 'vue'
import { Button, KeyboardShortcutsDialog, useKeyboardShortcut } from 'frappe-ui'

const open = ref(false)
const readOnly = ref(false)

// Every registration lists itself in the dialog. `enabled: false` makes a
// shortcut inert and hides its row, so read-only mode drops the editing group.
// "Redo" is registered twice and renders as one row with two combos.
//
// This story runs inside the docs page, not in a frame. Every row that does
// nothing sets `preventDefault: false`, so the browser's own Save, Undo and
// Zoom shortcuts keep working for the reader.
useKeyboardShortcut([
  {
    combo: 'Mod+Shift+Slash',
    description: 'Show keyboard shortcuts',
    group: 'General',
    allowInDialog: true,
    handler: () => (open.value = !open.value),
  },
  {
    combo: 'Mod+S',
    description: 'Save',
    group: 'General',
    preventDefault: false,
    handler: () => {},
  },
  {
    combo: 'Mod+Z',
    description: 'Undo',
    group: 'Editing',
    enabled: () => !readOnly.value,
    preventDefault: false,
    handler: () => {},
  },
  {
    combo: 'Mod+Shift+Z',
    description: 'Redo',
    group: 'Editing',
    enabled: () => !readOnly.value,
    preventDefault: false,
    handler: () => {},
  },
  {
    combo: 'Mod+Y',
    description: 'Redo',
    group: 'Editing',
    enabled: () => !readOnly.value,
    preventDefault: false,
    handler: () => {},
  },
  {
    combo: 'Mod+Equal',
    description: 'Zoom in',
    group: 'View',
    preventDefault: false,
    handler: () => {},
  },
  {
    combo: 'Mod+Minus',
    description: 'Zoom out',
    group: 'View',
    preventDefault: false,
    handler: () => {},
  },
])
</script>

<template>
  <div class="flex items-center gap-3">
    <Button label="Open shortcuts" @click="open = true" />
    <Button
      :label="readOnly ? 'Leave read-only mode' : 'Enter read-only mode'"
      @click="readOnly = !readOnly"
    />
    <!-- A story has far fewer rows than the default `searchThreshold` of 20.
         Lower it so the search field shows here. -->
    <KeyboardShortcutsDialog v-model:open="open" :search-threshold="3" />
  </div>
</template>
```

## Examples

### Custom help layout

An app draws its own list of shortcuts inside the dialog with the default
slot.

```vue
<script setup lang="ts">
import { ref } from 'vue'
import {
  Button,
  KeyboardShortcut,
  KeyboardShortcutsDialog,
  useKeyboardShortcut,
} from 'frappe-ui'

const open = ref(false)

// The default slot receives the grouped, enabled shortcuts, so an app can
// render its own help surface without reading the registry itself. Both rows
// have an empty handler, so both set `preventDefault: false` and leave the
// docs page they run in alone.
//
// Alt pairs with a digit here, not a letter: macOS rewrites `event.key` while
// Option is held, so an `Alt+<letter>` combo can miss there.
useKeyboardShortcut([
  {
    combo: 'Mod+Alt+Digit1',
    description: 'New page',
    group: 'Pages',
    preventDefault: false,
    handler: () => {},
  },
  {
    combo: 'Mod+Alt+Digit2',
    description: 'Duplicate page',
    group: 'Pages',
    preventDefault: false,
    handler: () => {},
  },
])
</script>

<template>
  <Button label="Open shortcuts" @click="open = true" />
  <KeyboardShortcutsDialog v-model:open="open" v-slot="{ groups }">
    <div class="space-y-4">
      <section v-for="group in groups" :key="group.name">
        <h4 class="mb-2 text-sm-medium text-ink-gray-5">{{ group.name }}</h4>
        <div
          v-for="shortcut in group.shortcuts"
          :key="shortcut.description"
          class="flex items-center justify-between py-1"
        >
          <span class="text-p-base text-ink-gray-7">
            {{ shortcut.description }}
          </span>
          <KeyboardShortcut
            :combo="shortcut.combo"
            :alt-combos="shortcut.altCombos"
            bg
          />
        </div>
      </section>
    </div>
  </KeyboardShortcutsDialog>
</template>
```

## Behavior

### Setup

Mount the dialog once near the app root and bind `v-model:open`. The dialog
has no trigger of its own. Its rows come from the shortcut registry, so it
takes no content props.

### Opening from a shortcut

The preview at the top of this page registers this shortcut. Press
<kbd>Cmd</kbd>/<kbd>Ctrl</kbd> + <kbd>Shift</kbd> + <kbd>/</kbd> to open it.

```vue
<script setup lang="ts">
import { KeyboardShortcutsDialog, useKeyboardShortcut } from 'frappe-ui'
import { ref } from 'vue'

const open = ref(false)

useKeyboardShortcut({
  combo: 'Mod+Shift+Slash',
  description: 'Show keyboard shortcuts',
  group: 'General',
  allowInDialog: true,
  handler: () => (open.value = !open.value),
})
</script>

<template>
  <KeyboardShortcutsDialog v-model:open="open" />
</template>
```

`allowInDialog: true` lets the combo work while the dialog has focus, so the
same keys close it again. That is why the handler toggles.

`Mod+Shift+Slash` is the `?` most apps use. The combo names the physical key,
not the character it types. See
[the combo grammar](https://github.com/frappe/frappe-ui/blob/main/spec/shortcuts.md).

### Disabled shortcuts are hidden

A shortcut whose `enabled` is `false` does not run **and** does not show in the
dialog. A shortcut the user cannot press is not listed. In the preview at the
top, turn on read-only mode to see the Editing group go away.

The dialog reads `enabled` each time it opens, and again whenever a shortcut
registers or unregisters. A getter that reads untracked state, such as
`document.activeElement`, is correct on every open.

### Merged rows

Shortcuts that share a group and a description merge into one row. The first
combo is the row's combo, and the rest show after a `/` as alternatives. In
the preview at the top, Undo and Redo are three registrations and two rows.

### Search

When there are more rows than `searchThreshold`, a search field shows. The
default is 20. It matches the text a row shows and the key name behind it, so a
row for `Mod+Slash` matches both `/` and `slash`. The query clears when the
dialog closes. The preview at the top sets `searchThreshold` to 3, because it
has fewer than 20 rows.

### Custom layout

The default slot receives `{ groups }`: the enabled shortcuts, grouped and
merged. The library exports no other way to read the registry, so use this slot
for a custom help layout.

## API reference

```ts
const propsData = [
  {
    name: 'title',
    description: 'Dialog title (default: "Keyboard Shortcuts").',
    required: false,
    type: 'string',
    default: '"Keyboard Shortcuts"'
  },
  {
    name: 'paddingTop',
    description: 'Top padding while the dialog sits at the top of the screen. Takes the\nsame values as `Dialog`\'s own prop (default: "5vh").',
    required: false,
    type: 'string | number',
    default: '"5vh"'
  },
  {
    name: 'searchThreshold',
    description: 'The search input appears once the number of rows passes this count.\nRows, not registrations: shortcuts that merge count once (default: 20).',
    required: false,
    type: 'number',
    default: '20'
  },
  {
    name: 'open',
    description: 'Controls the visibility of the dialog.',
    required: false,
    type: 'boolean',
    default: 'false'
  }
]

  const slotsData = [
  {
    name: 'default',
    description: 'Replaces the default grid. Receives the grouped, enabled shortcuts the search leaves.',
    type: '{ groups: KeyboardShortcutGroup[]; }'
  }
]

  const emitsData = [
  {
    name: 'update:open',
    description: 'Fired when the open state changes.',
    type: '[value: boolean]'
  }
]
```
