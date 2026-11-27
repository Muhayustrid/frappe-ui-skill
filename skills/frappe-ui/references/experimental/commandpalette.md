<!-- Upstream: https://github.com/frappe/frappe-ui/blob/b435c66b51c0b19425a2a049b43e3ef2e308a8ab/experimental/CommandPalette/CommandPalette.md -->

# CommandPalette

A searchable list of commands in a dialog. Compose `CommandPaletteInput`,
`CommandPaletteList`, `CommandPaletteGroup`, `CommandPaletteItem`,
`CommandPaletteEmpty` and `CommandPaletteFooter` inside the `CommandPalette`
root. The app writes the rows; the palette owns the dialog, the query, the
keyboard and the filter.

Groups and items go inside `CommandPaletteList`, which is the list itself and
the only part that scrolls. The field, the empty state and the footer are its
siblings: a list may own rows and groups and nothing else, and keeping them
outside it is also what stops them scrolling away.

```vue
<CommandPalette v-model:open="open" @select="select">
  <CommandPaletteInput placeholder="Search commands" />

  <CommandPaletteList>
    <CommandPaletteGroup label="Pages">
      <CommandPaletteItem :value="page">Inbox</CommandPaletteItem>
    </CommandPaletteGroup>
  </CommandPaletteList>

  <CommandPaletteEmpty>No matches</CommandPaletteEmpty>
  <CommandPaletteFooter><KeyboardShortcut combo="Enter" /> to run</CommandPaletteFooter>
</CommandPalette>
```

> **Experimental** — the family ships from
> [`frappe-ui/experimental`](../experimental.md) while its API settles, so it is
> exempt from the usual deprecation policy and can change shape or disappear in
> any release. The root `CommandPalette` was removed in `1.0.0`; see the
> [migration guide](../migration.md#commandpalette).

```ts
import {
  CommandPalette,
  CommandPaletteInput,
  CommandPaletteList,
  CommandPaletteGroup,
  CommandPaletteItem,
  CommandPaletteEmpty,
  CommandPaletteFooter,
} from 'frappe-ui/experimental'
```

```vue
<script setup lang="ts">
import { ref } from 'vue'
import { Button, KeyboardShortcut, useKeyboardShortcut } from 'frappe-ui'
import {
  CommandPalette,
  CommandPaletteEmpty,
  CommandPaletteFooter,
  CommandPaletteGroup,
  CommandPaletteInput,
  CommandPaletteList,
  CommandPaletteItem,
} from '..'
import type { CommandPaletteValue } from '..'

// The client filter runs by default, so this list is written once and the
// palette narrows it as the user types.
const pages = [
  { name: 'inbox', title: 'Inbox', icon: 'lucide-inbox', keywords: ['mail'] },
  { name: 'people', title: 'People', icon: 'lucide-users' },
  { name: 'settings', title: 'Settings', icon: 'lucide-settings' },
]

const actions = [
  {
    name: 'new-task',
    title: 'New task',
    icon: 'lucide-plus',
    combo: 'Mod+N',
  },
  {
    name: 'new-note',
    title: 'New note',
    icon: 'lucide-file-text',
    combo: 'Mod+Shift+N',
  },
]

const open = ref(false)
const picked = ref('')

// The palette no longer registers a shortcut. The app decides when Mod+K
// belongs to it.
useKeyboardShortcut({
  combo: 'Mod+K',
  description: 'Open command palette',
  allowInInput: true,
  handler: () => (open.value = true),
})

// `select` hands back exactly the object the item carried, typed as the
// palette's value union, so the handler narrows it.
function select(value: CommandPaletteValue) {
  picked.value = (value as { title: string }).title
}
</script>

<template>
  <div class="flex flex-col items-start gap-3">
    <Button @click="open = true">Open command palette (or press Mod+K)</Button>
    <p v-if="picked" class="text-p-sm text-ink-gray-6">Picked: {{ picked }}</p>

    <CommandPalette v-model:open="open" @select="select">
      <CommandPaletteInput placeholder="Search commands" />

      <CommandPaletteList>
        <CommandPaletteGroup label="Pages">
          <CommandPaletteItem
            v-for="page in pages"
            :key="page.name"
            :value="page"
            :keywords="page.keywords"
          >
            <template #prefix>
              <span :class="[page.icon, 'me-3 size-4 text-ink-gray-7']" />
            </template>
            {{ page.title }}
          </CommandPaletteItem>
        </CommandPaletteGroup>

        <CommandPaletteGroup label="Actions">
          <CommandPaletteItem
            v-for="action in actions"
            :key="action.name"
            :value="action"
          >
            <template #prefix>
              <span :class="[action.icon, 'me-3 size-4 text-ink-gray-7']" />
            </template>
            {{ action.title }}
            <template #suffix>
              <KeyboardShortcut :combo="action.combo" />
            </template>
          </CommandPaletteItem>
        </CommandPaletteGroup>
      </CommandPaletteList>

      <CommandPaletteEmpty v-slot="{ query }">
        Nothing matches "{{ query }}"
      </CommandPaletteEmpty>

      <CommandPaletteFooter>
        <KeyboardShortcut combo="Enter" /> to run
        <KeyboardShortcut combo="Escape" /> to close
      </CommandPaletteFooter>
    </CommandPalette>
  </div>
</template>
```

## Opening it

The palette registers no shortcut. Write the one line yourself, so the app
decides when `Mod+K` belongs to the palette and when it belongs to a focused
editor:

```js
useKeyboardShortcut({
  combo: 'Mod+K',
  description: 'Open command palette',
  // Guards are off by default, so without this the shortcut dies as soon as
  // any field has focus.
  allowInInput: true,
  handler: () => (open.value = true),
})
```

## Filtering

`filterable` is on by default. Each item matches when the query is a substring
of its text, the same rule [`Combobox`](../components/combobox.md) applies to
its options.

An item filters on the text it renders in its default slot. `#prefix` and
`#suffix` are left out, so a trailing shortcut hint or badge never becomes
searchable. Add `keywords` for aliases the row does not show, and set `label`
when the default slot draws more than the label.

```vue
<CommandPaletteItem :value="page" :keywords="['mail', 'unread']">
  Inbox
  <template #suffix><KeyboardShortcut combo="Mod+I" /></template>
</CommandPaletteItem>
```

A group hides itself, heading and all, once the filter empties it.

## Server-side search

Set `:filterable="false"` and refetch on `update:query`. The backend has already
decided what matches, so a second pass on the client would drop its fuzzy and
relevance-ranked rows.

```vue
<script setup lang="ts">
import { ref, watch } from 'vue'
import { Button } from 'frappe-ui'
import {
  CommandPalette,
  CommandPaletteEmpty,
  CommandPaletteGroup,
  CommandPaletteInput,
  CommandPaletteList,
  CommandPaletteItem,
} from '..'

// `:filterable="false"` hands the matching to the server. Without it, a
// second literal substring pass on the client would drop the fuzzy and
// relevance-ranked rows the backend just returned (ADR-0009).
const everything = [
  { name: 'design-review', title: 'Design review', team: 'Product' },
  { name: 'release-plan', title: 'Release plan', team: 'Engineering' },
  { name: 'onboarding', title: 'Onboarding checklist', team: 'People' },
  { name: 'q3-budget', title: 'Q3 budget', team: 'Finance' },
]

const open = ref(false)
const query = ref('')
const results = ref<typeof everything>([])
const loading = ref(false)

let timer: ReturnType<typeof setTimeout> | undefined

// A debounced refetch stands in for the real request.
watch(query, (text) => {
  clearTimeout(timer)
  if (!text) {
    results.value = []
    loading.value = false
    return
  }
  loading.value = true
  timer = setTimeout(() => {
    const needle = text.toLowerCase()
    results.value = everything.filter(
      (row) =>
        row.title.toLowerCase().includes(needle) ||
        row.team.toLowerCase().includes(needle),
    )
    loading.value = false
  }, 300)
})
</script>

<template>
  <div class="flex flex-col items-start gap-3">
    <Button @click="open = true">Search the server</Button>

    <CommandPalette
      v-model:open="open"
      v-model:query="query"
      :filterable="false"
    >
      <CommandPaletteInput placeholder="Search documents" />

      <!-- The palette's own slot is open, so a loading row needs no extra
      part. It sits outside the list, which owns options and groups only. -->
      <div
        v-if="loading"
        class="px-4.5 py-8 text-center text-base text-ink-gray-5"
      >
        Searching…
      </div>

      <CommandPaletteList v-else>
        <CommandPaletteGroup label="Documents">
          <CommandPaletteItem
            v-for="row in results"
            :key="row.name"
            :value="row"
          >
            {{ row.title }}
            <template #suffix>
              <span class="text-ink-gray-5">{{ row.team }}</span>
            </template>
          </CommandPaletteItem>
        </CommandPaletteGroup>
      </CommandPaletteList>

      <CommandPaletteEmpty v-if="!loading" v-slot="{ query: text }">
        {{ text ? `No documents match "${text}"` : 'Type to search' }}
      </CommandPaletteEmpty>
    </CommandPalette>
  </div>
</template>
```

## Items that are links

Give an item `as="a"` and an `href` and it renders a real anchor, so
middle-click and modifier-click open a new tab natively. `select` carries the
click that picked the row in `event.detail.originalEvent`, which is where the
modifier keys are.

```vue
<script setup lang="ts">
import { ref } from 'vue'
import { Button } from 'frappe-ui'
import {
  CommandPalette,
  CommandPaletteEmpty,
  CommandPaletteGroup,
  CommandPaletteInput,
  CommandPaletteList,
  CommandPaletteItem,
} from '..'
import type { CommandPaletteSelectEvent, CommandPaletteValue } from '..'

// `as="a"` plus `href` makes the row a real link, so middle-click,
// Cmd-click and "open in a new tab" all work the way the browser does
// them. Nothing here re-implements that.
const docs = [
  { link: '/docs/components/button', text: 'Button' },
  { link: '/docs/components/dialog', text: 'Dialog' },
  { link: '/docs/components/combobox', text: 'Combobox' },
]

const open = ref(false)
const visited = ref('')

function select(value: CommandPaletteValue, event: CommandPaletteSelectEvent) {
  const item = value as { link: string }
  const click = event.detail.originalEvent
  // Let the browser take modifier-clicks through the `href`.
  if (click.metaKey || click.ctrlKey || click.shiftKey || click.button === 1) {
    return
  }
  click.preventDefault()
  visited.value = item.link
}
</script>

<template>
  <div class="flex flex-col items-start gap-3">
    <Button @click="open = true">Search the docs</Button>
    <p v-if="visited" class="text-p-sm text-ink-gray-6">
      Would navigate to {{ visited }}
    </p>

    <CommandPalette v-model:open="open" @select="select">
      <CommandPaletteInput placeholder="Search documentation" />

      <CommandPaletteList>
        <CommandPaletteGroup label="Components">
          <CommandPaletteItem
            v-for="page in docs"
            :key="page.link"
            as="a"
            :href="page.link"
            :value="page"
          >
            {{ page.text }}
          </CommandPaletteItem>
        </CommandPaletteGroup>
      </CommandPaletteList>

      <CommandPaletteEmpty />
    </CommandPalette>
  </div>
</template>
```

## Keeping it open

The palette closes after a pick. Call `event.preventDefault()` in the `select`
handler to keep it open, for a row that switches the palette into a mode instead
of running a command.

```js
function select(value, event) {
  if (value.kind === 'mode') {
    event.preventDefault()
    mode.value = value.name
  }
}
```

## Styling hooks

Every part stamps `data-slot`. An item adds `data-state="active"` while the
keyboard or the pointer is on it, and `data-disabled` when it cannot be picked.
Items hand `active` and `disabled` to every one of their slots.

There is no selected state. A pick closes the palette, and a pick that keeps it
open does so by preventing the event, which is the same signal that tells the
list not to record the row.

## API reference

```ts
const commandPaletteProps = [
  {
    name: 'filterable',
    description: 'Filter the items against the query on the client. Set it to `false` when a\nserver search already decided what matches (ADR-0009), then refetch on\n`update:query` yourself.',
    required: false,
    type: 'boolean',
    default: 'true'
  },
  {
    name: 'title',
    description: 'The dialog\'s accessible name, and the list\'s. It is read by screen readers\nand never drawn, because the palette\'s shell has no header.',
    required: false,
    type: 'string',
    default: '"Command palette"'
  },
  {
    name: 'open',
    description: 'Whether the palette is open.',
    required: false,
    type: 'boolean',
    default: 'false'
  },
  {
    name: 'query',
    description: 'The search text. Cleared when the palette closes.',
    required: false,
    type: 'string',
    default: '""'
  }
]

  const commandPaletteSlots = [
  {
    name: 'default',
    description: 'The palette\'s parts. Receives the query, the active value and the empty state.',
    type: 'CommandPaletteSlotProps'
  }
]

  const commandPaletteEmits = [
  {
    name: 'update:open',
    description: 'Fired when the open state changes.',
    type: '[value: boolean]'
  },
  {
    name: 'select',
    description: 'Fired when the user picks an item. The palette closes right after, unless\nthe handler calls `event.preventDefault()`.',
    type: '[value: AcceptableValue, event: CommandPaletteSelectEvent]'
  },
  {
    name: 'update:query',
    description: 'Fired when the query changes.',
    type: '[value: string]'
  }
]

  const commandPaletteInputProps = [
  {
    name: 'placeholder',
    description: 'Placeholder text for the search field.',
    required: false,
    type: 'string',
    default: '"Search"'
  }
]

  const commandPaletteInputSlots = [
  {
    name: 'prefix',
    description: 'Replaces the leading search icon.',
    type: 'any'
  },
  {
    name: 'suffix',
    description: 'Trailing content, after the field.',
    type: 'any'
  }
]

  const commandPaletteListSlots = [
  {
    name: 'default',
    description: 'The palette\'s groups and items. This element is the listbox itself, and a\nlistbox owns options and groups only, so anything else belongs outside it.',
    type: 'any'
  }
]

  const commandPaletteGroupProps = [
  {
    name: 'label',
    description: 'Heading above the group\'s items. Leave it out to group without a heading.',
    required: false,
    type: 'string'
  }
]

  const commandPaletteGroupSlots = [
  {
    name: 'default',
    description: 'The group\'s `CommandPaletteItem`s.',
    type: 'any'
  }
]

  const commandPaletteItemProps = [
  {
    name: 'value',
    description: 'The value the palette reports in `select`.',
    required: true,
    type: 'AcceptableValue'
  },
  {
    name: 'label',
    description: 'Text the client filter matches. It defaults to the item\'s own rendered\ntext, so set it only when the default slot draws more than the label.\nAn item that draws no text at all has to set it, or the filter can never\nnarrow it away.',
    required: false,
    type: 'string'
  },
  {
    name: 'keywords',
    description: 'Extra words the client filter matches, on top of the label.',
    required: false,
    type: 'string[]'
  },
  {
    name: 'disabled',
    description: 'Stop the user picking this item.',
    required: false,
    type: 'boolean'
  },
  {
    name: 'as',
    description: 'Element the item renders as. Use `a` with an `href` for a real link, so\nmiddle-click and modifier-click open a new tab.',
    required: false,
    type: 'string | Component',
    default: '"div"'
  }
]

  const commandPaletteItemSlots = [
  {
    name: 'default',
    description: 'The item\'s label. The client filter matches its text.',
    type: 'CommandPaletteItemSlotProps'
  },
  {
    name: 'prefix',
    description: 'Leading content, before the label.',
    type: 'CommandPaletteItemSlotProps'
  },
  {
    name: 'suffix',
    description: 'Trailing content, pushed to the end of the row.',
    type: 'CommandPaletteItemSlotProps'
  }
]

  const commandPaletteItemEmits = [
  {
    name: 'select',
    description: 'Fired when this item is picked, before the palette\'s own `select`. Call\n`event.preventDefault()` to keep the palette open.',
    type: '[event: CommandPaletteSelectEvent]'
  }
]

  const commandPaletteEmptySlots = [
  {
    name: 'default',
    description: 'The message. Receives the query so it can quote what the user typed.',
    type: 'CommandPaletteEmptySlotProps'
  }
]

  const commandPaletteFooterSlots = [
  {
    name: 'default',
    description: 'The footer\'s content. Receives the active value, so a hint can follow it.',
    type: 'CommandPaletteFooterSlotProps'
  }
]
```
