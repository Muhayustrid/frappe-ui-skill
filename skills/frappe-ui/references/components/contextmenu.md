<!-- Upstream: https://github.com/frappe/frappe-ui/blob/b435c66b51c0b19425a2a049b43e3ef2e308a8ab/src/components/ContextMenu/ContextMenu.md -->

# ContextMenu

A menu that opens at the cursor when the user right-clicks inside its trigger
area. To open a menu from a button, use [Dropdown](dropdown.md) instead.

```vue
<script setup lang="ts">
import { ref } from 'vue'
import { ContextMenu, type ContextMenuOptions } from 'frappe-ui'

const text = 'Ship it tomorrow?'
const deleted = ref(false)
const lastAction = ref('')

const messageActions: ContextMenuOptions = [
  {
    label: 'Reply',
    icon: 'lucide-reply',
    onClick: () => (lastAction.value = 'Replying to Alex'),
  },
  {
    label: 'Copy text',
    icon: 'lucide-copy',
    onClick: () => (lastAction.value = `Copied “${text}”`),
  },
  {
    label: 'Edit',
    icon: 'lucide-pen',
    onClick: () => (lastAction.value = 'Editing message'),
  },
  {
    label: 'Delete',
    icon: 'lucide-trash-2',
    theme: 'red',
    onClick: () => {
      deleted.value = true
      lastAction.value = 'Message deleted'
    },
  },
]

function restore() {
  deleted.value = false
  lastAction.value = ''
}
</script>

<template>
  <div class="grid gap-3 justify-items-center">
    <ContextMenu :options="messageActions">
      <div class="flex flex-col gap-1.5">
        <div class="flex items-baseline gap-2">
          <span class="text-sm-medium text-ink-gray-8">Alex</span>
          <span class="text-xs text-ink-gray-4">9:41 AM</span>
        </div>
        <div
          v-if="!deleted"
          class="w-fit cursor-default select-none rounded-8 rounded-tl-1 bg-surface-gray-3 px-3.5 py-2 text-sm text-ink-gray-8"
        >
          {{ text }}
        </div>
        <div v-else class="flex items-center gap-2 text-sm text-ink-gray-4">
          <span class="italic">Message deleted</span>
          <button
            class="font-medium text-ink-gray-6 underline underline-offset-2"
            @click="restore"
          >
            Undo
          </button>
        </div>
      </div>
    </ContextMenu>

    <div class="text-sm text-ink-gray-5">
      {{ lastAction || 'Right-click the message' }}
    </div>
  </div>
</template>
```

## Examples

### Task card

A card with grouped actions and submenus for Share and Move to. `options` takes
the same items, groups and submenus as Dropdown.

```vue
<script setup lang="ts">
import { ContextMenu, Avatar, Badge, type ContextMenuOptions } from 'frappe-ui'

const actions: ContextMenuOptions = [
  {
    group: 'Actions',
    options: [
      {
        label: 'Edit',
        icon: 'lucide-pen',
        onClick: () => console.log('edit'),
      },
      {
        label: 'Duplicate',
        icon: 'lucide-copy',
        onClick: () => console.log('duplicate'),
      },
      {
        label: 'Share',
        icon: 'lucide-share-2',
        submenu: [
          {
            label: 'Copy link',
            icon: 'lucide-link',
            onClick: () => console.log('copy link'),
          },
          {
            label: 'Invite people',
            icon: 'lucide-user-plus',
            onClick: () => console.log('invite'),
          },
        ],
      },
      {
        label: 'Move to',
        icon: 'lucide-folder-input',
        submenu: [
          {
            label: 'In Progress',
            icon: 'lucide-loader-circle',
            onClick: () => console.log('in progress'),
          },
          {
            label: 'Done',
            icon: 'lucide-circle-check',
            onClick: () => console.log('done'),
          },
          {
            label: 'Backlog',
            icon: 'lucide-inbox',
            onClick: () => console.log('backlog'),
          },
        ],
      },
    ],
  },
  {
    group: 'Danger',
    options: [
      {
        label: 'Delete',
        icon: 'lucide-trash-2',
        theme: 'red',
        onClick: () => console.log('delete'),
      },
    ],
  },
]
</script>

<template>
  <ContextMenu :options="actions">
    <div
      class="w-72 cursor-default select-none rounded-7 border border-outline-gray-2 bg-surface-base p-4 shadow-sm"
    >
      <div class="mb-3 flex items-start justify-between gap-2">
        <Badge theme="amber" label="In Progress" />
        <span class="text-p-sm text-ink-gray-4">Due Friday</span>
      </div>
      <p class="mb-2 text-sm-medium text-ink-gray-8">
        Redesign onboarding flow
      </p>
      <p class="text-p-sm text-ink-gray-5">
        Update the sign-up steps and add the new welcome screen from Figma.
      </p>
      <div class="mt-3 flex">
        <Avatar
          label="John Doe"
          image="https://avatars.githubusercontent.com/u/499550?s=60&v=4"
          size="sm"
        />
      </div>
    </div>
  </ContextMenu>
</template>
```

### File list

One `ContextMenu` wraps the whole list. Each row's `@contextmenu` handler
writes that row's actions into a ref before the menu opens. Folders get
**New file**, files get **Download**, and images also get **Set as cover**.

```vue
<script setup lang="ts">
import { ref } from 'vue'
import { ContextMenu, type ContextMenuOptions } from 'frappe-ui'

type FileItem = {
  name: string
  type: 'folder' | 'doc' | 'image' | 'sheet'
  meta: string
}

const files: FileItem[] = [
  { name: 'Design Assets', type: 'folder', meta: '14 items' },
  { name: 'Q3 Report.docx', type: 'doc', meta: 'Edited 2h ago' },
  { name: 'Campaign Banner.png', type: 'image', meta: '2.4 MB' },
  { name: 'Budget 2025.xlsx', type: 'sheet', meta: 'Edited yesterday' },
  { name: 'Archive', type: 'folder', meta: '3 items' },
]

const iconMap: Record<FileItem['type'], string> = {
  folder: 'lucide-folder',
  doc: 'lucide-file-text',
  image: 'lucide-image',
  sheet: 'lucide-table',
}

function getActions(file: FileItem): ContextMenuOptions {
  const base: ContextMenuOptions = [
    {
      label: 'Open',
      icon: 'lucide-external-link',
      onClick: () => console.log('open', file.name),
    },
    {
      label: 'Rename',
      icon: 'lucide-pen',
      onClick: () => console.log('rename', file.name),
    },
    {
      label: 'Copy link',
      icon: 'lucide-link',
      onClick: () => console.log('copy link', file.name),
    },
  ]
  if (file.type === 'folder') {
    base.push({
      label: 'New file',
      icon: 'lucide-file-plus',
      onClick: () => console.log('new file in', file.name),
    })
  } else {
    base.push({
      label: 'Download',
      icon: 'lucide-download',
      onClick: () => console.log('download', file.name),
    })
  }
  if (file.type === 'image') {
    base.push({
      label: 'Set as cover',
      icon: 'lucide-image',
      onClick: () => console.log('set as cover', file.name),
    })
  }
  base.push({
    label: 'Delete',
    icon: 'lucide-trash-2',
    theme: 'red',
    onClick: () => console.log('delete', file.name),
  })
  return base
}

const activeOptions = ref<ContextMenuOptions>([])
</script>

<template>
  <div
    class="w-80 overflow-hidden rounded-7 border border-outline-gray-2 bg-surface-base shadow-sm"
  >
    <div class="border-b border-outline-gray-1 px-3 py-2">
      <p class="text-p-sm-medium text-ink-gray-5">Recents</p>
    </div>
    <ContextMenu :options="activeOptions">
      <ul class="divide-y divide-outline-gray-1">
        <li
          v-for="file in files"
          :key="file.name"
          class="flex cursor-default select-none items-center gap-2.5 px-3 py-2 hover:bg-surface-gray-1"
          @contextmenu="activeOptions = getActions(file)"
        >
          <span
            class="size-4 shrink-0 text-ink-gray-4"
            :class="iconMap[file.type]"
          />
          <div class="min-w-0 flex-1">
            <p class="truncate text-sm text-ink-gray-8">{{ file.name }}</p>
          </div>
          <span class="shrink-0 text-p-sm text-ink-gray-4">{{
            file.meta
          }}</span>
        </li>
      </ul>
    </ContextMenu>
  </div>
</template>
```

## Behavior

### Trigger

The default slot, or the `#trigger` slot, is the area that opens the menu on
right-click. Both receive `{ open, setOpen, close }`.

`ContextMenu` does not pass attributes through. Put classes and attributes on
the element inside the trigger slot.

### Options per row

A list does not need one `ContextMenu` per row. Wrap the list once, and set the
`options` from the row's `@contextmenu` handler. The handler runs before the
menu opens, so the menu shows the actions for the row that was right-clicked.

### Opening from code

`setOpen(true)` from the slot props opens the menu at the lower-left corner of
the trigger, because there is no cursor position to use.

### Scroll lock

While the menu is open, the page does not scroll with the mouse wheel or touch.
The menu itself still scrolls.

### Portal

The menu renders in `body`, or in the nearest host container that provides a
portal target. Use `portalTo` when the menu must render inside a specific
container.

## Accessibility

| Keys                    | Action                                    |
| ----------------------- | ----------------------------------------- |
| `ArrowDown` / `ArrowUp` | Move focus between items                  |
| `Enter` / `Space`       | Run the focused item, or open its submenu |
| `ArrowRight`            | Open the focused submenu                  |
| `ArrowLeft`             | Close the submenu                         |
| `Escape`                | Close the menu                            |

The menu opens on right-click, or on a long press on touch screens. Users
may not know it is there, so also offer important actions somewhere visible,
such as a row actions [Dropdown](dropdown.md).

## API reference

```ts
const propsData = [
  {
    name: 'options',
    description: 'Array of context menu options or grouped options.',
    required: false,
    type: 'MenuOptions',
    default: '[]'
  },
  {
    name: 'open',
    description: 'Controls the visibility of the context menu.',
    required: false,
    type: 'boolean',
    default: 'false'
  },
  {
    name: 'portalTo',
    description: 'Teleport target for menu content. Unset uses the nearest host target or `body`.',
    required: false,
    type: 'PortalTarget'
  }
]

  const slotsData = [
  {
    name: 'item',
    description: 'Replaces the entire item row.',
    type: 'MenuItemSlotProps'
  },
  {
    name: 'item-prefix',
    description: 'Content rendered before the standard item label.',
    type: 'MenuItemSlotProps'
  },
  {
    name: 'item-label',
    description: 'Content rendered for the standard item label area.',
    type: 'MenuItemSlotProps'
  },
  {
    name: 'item-suffix',
    description: 'Content rendered after the standard item label.',
    type: 'MenuItemSlotProps'
  },
  {
    name: 'group-label',
    description: 'Custom renderer for group labels.',
    type: 'MenuGroupSlotProps'
  },
  {
    name: 'empty',
    description: 'Fallback content rendered when no items are available.',
    type: 'any'
  },
  {
    name: 'default',
    description: 'The right-clickable region that opens the menu.',
    type: 'ContextMenuTriggerSlotProps'
  },
  {
    name: 'trigger',
    description: 'Explicit trigger slot; same as default.',
    type: 'ContextMenuTriggerSlotProps'
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
