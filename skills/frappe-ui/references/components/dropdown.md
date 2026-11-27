<!-- Upstream: https://github.com/frappe/frappe-ui/blob/b435c66b51c0b19425a2a049b43e3ef2e308a8ab/src/components/Dropdown/Dropdown.md -->

# Dropdown

A menu of actions that opens from a button. To open a menu on right-click, use
[ContextMenu](contextmenu.md) instead.



## Examples

### More actions button

A plain actions menu with icons. The `button` prop configures the default
trigger `<Button>`.

```vue
<script setup lang="ts">
import { ref } from 'vue'
import { Dropdown, type DropdownOptions } from 'frappe-ui'

const lastAction = ref('')

const actions: DropdownOptions = [
  {
    label: 'Edit',
    icon: 'lucide-pen',
    onClick: () => (lastAction.value = 'Edited'),
  },
  {
    label: 'Duplicate',
    icon: 'lucide-copy',
    onClick: () => (lastAction.value = 'Duplicated'),
  },
  {
    label: 'Delete',
    icon: 'lucide-trash-2',
    theme: 'red',
    onClick: () => (lastAction.value = 'Deleted'),
  },
]
</script>

<template>
  <div class="grid gap-3 justify-items-center">
    <Dropdown
      :options="actions"
      :button="{ icon: 'lucide-more-horizontal', label: 'Options' }"
    />

    <div class="text-sm text-ink-gray-5">
      {{ lastAction || 'Pick an action' }}
    </div>
  </div>
</template>
```

### Row actions

An icon-only `ghost` button on each row of a list opens a grouped menu. The
`#trigger` slot replaces the default button.

```vue
<script setup lang="ts">
import { Button, Dropdown, type DropdownOptions } from 'frappe-ui'

type Task = { title: string; meta: string }

const tasks: Task[] = [
  { title: 'Ship Combobox v1', meta: 'Due Friday · Engineering' },
  { title: 'Write migration guide', meta: 'In review · Design' },
  { title: 'Audit selection components', meta: 'Backlog · Platform' },
]

function rowActions(task: Task): DropdownOptions {
  return [
    {
      group: 'Manage',
      options: [
        {
          label: 'Rename',
          icon: 'lucide-pen',
          onClick: () => console.log('rename', task.title),
        },
        {
          label: 'Duplicate',
          icon: 'lucide-copy',
          onClick: () => console.log('duplicate', task.title),
        },
        {
          label: 'Share',
          icon: 'lucide-share-2',
          onClick: () => console.log('share', task.title),
        },
      ],
    },
    {
      group: 'Move',
      options: [
        {
          label: 'Archive',
          icon: 'lucide-archive',
          onClick: () => console.log('archive', task.title),
        },
        {
          label: 'Delete',
          icon: 'lucide-trash-2',
          theme: 'red',
          onClick: () => console.log('delete', task.title),
        },
      ],
    },
  ]
}
</script>

<template>
  <div
    class="w-80 divide-y divide-outline-gray-1 rounded-6 border border-outline-gray-1 bg-surface-base"
  >
    <div
      v-for="task in tasks"
      :key="task.title"
      class="flex items-center gap-3 px-3 py-2.5"
    >
      <div class="min-w-0 flex-1">
        <div class="truncate text-base text-ink-gray-8">{{ task.title }}</div>
        <div class="truncate text-p-sm text-ink-gray-5">{{ task.meta }}</div>
      </div>

      <Dropdown align="end" :options="rowActions(task)">
        <template #trigger>
          <Button
            variant="ghost"
            icon="lucide-more-horizontal"
            label="Row actions"
          />
        </template>
      </Dropdown>
    </div>
  </div>
</template>
```

### File menu with shortcuts

The `#item-suffix` slot shows a keyboard shortcut at the end of each row. The
shortcut is a custom `shortcut` field on each item.

```vue
<script setup lang="ts">
import { Dropdown } from 'frappe-ui'

type FileAction = {
  label: string
  icon: string
  shortcut: string[]
  theme?: 'red'
  onClick?: () => void
}

const actions: FileAction[] = [
  { label: 'New document', icon: 'lucide-plus', shortcut: ['⌘', 'N'] },
  { label: 'Rename', icon: 'lucide-pen', shortcut: ['F2'] },
  { label: 'Duplicate', icon: 'lucide-copy', shortcut: ['⌘', 'D'] },
  {
    label: 'Delete',
    icon: 'lucide-trash-2',
    shortcut: ['⌫'],
    theme: 'red',
  },
]
</script>

<template>
  <Dropdown :options="actions" :button="{ label: 'File' }">
    <template #item-suffix="{ item }">
      <div
        v-if="(item as FileAction).shortcut"
        class="flex items-center gap-px"
      >
        <kbd
          v-for="(key, i) in (item as FileAction).shortcut"
          :key="i"
          class="font-sans text-xs leading-4 text-ink-gray-6"
        >
          {{ key }}
        </kbd>
      </div>
    </template>
  </Dropdown>
</template>
```

### Share and move

Groups of actions with nested submenus. "Share" opens a submenu, and "Invite
people" inside it opens another.

```vue
<script setup lang="ts">
import { Dropdown, type DropdownOptions } from 'frappe-ui'

const actions: DropdownOptions = [
  {
    group: 'Manage',
    options: [
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
            submenu: [
              {
                label: 'By email',
                icon: 'lucide-mail',
                onClick: () => console.log('invite by email'),
              },
              {
                label: 'Send to Slack',
                icon: 'lucide-message-circle',
                onClick: () => console.log('send to slack'),
              },
            ],
          },
        ],
      },
      {
        label: 'Move to',
        icon: 'lucide-folder-input',
        submenu: [
          {
            label: 'Backlog',
            icon: 'lucide-inbox',
            onClick: () => console.log('move to backlog'),
          },
          {
            label: 'Done',
            icon: 'lucide-check-circle',
            onClick: () => console.log('move to done'),
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
  <Dropdown :options="actions" :button="{ label: 'Actions' }" />
</template>
```

### Preference toggles

Items with `switch: true` show a switch. The menu stays open, so the user can
change several settings at once.

```vue
<script setup lang="ts">
import { computed, ref } from 'vue'
import { Dropdown } from 'frappe-ui'

const notifications = ref(true)
const readReceipts = ref(true)
const focusMode = ref(false)
const autoSave = ref(true)

const preferences = computed(() => [
  {
    label: 'Notifications',
    icon: 'lucide-bell',
    switch: true as const,
    switchValue: notifications.value,
    onClick: (value: boolean) => (notifications.value = value),
  },
  {
    label: 'Read receipts',
    icon: 'lucide-eye',
    switch: true as const,
    switchValue: readReceipts.value,
    onClick: (value: boolean) => (readReceipts.value = value),
  },
  {
    label: 'Focus mode',
    icon: 'lucide-zap',
    switch: true as const,
    switchValue: focusMode.value,
    onClick: (value: boolean) => (focusMode.value = value),
  },
  {
    label: 'Auto-save drafts',
    icon: 'lucide-save',
    switch: true as const,
    switchValue: autoSave.value,
    onClick: (value: boolean) => (autoSave.value = value),
  },
])
</script>

<template>
  <Dropdown :options="preferences" :button="{ label: 'Preferences' }" />
</template>
```

### Workspace menu

A custom trigger that shows the app, product name and current user, with
submenus for apps and theme. `selected` marks the current app and theme.

```vue
<script setup lang="ts">
import { computed, ref } from 'vue'
import { Dropdown } from 'frappe-ui'

const theme = ref<'light' | 'dark' | 'system'>('system')

const userMenuItems = computed(() => [
  {
    icon: 'lucide-user',
    label: 'My Profile',
    onClick: () => console.log('My Profile clicked'),
  },
  {
    icon: 'lucide-layout-grid',
    label: 'Apps',
    submenu: [
      {
        label: 'Gameplan',
        selected: true,
        onClick: () => console.log('Gameplan clicked'),
      },
      {
        label: 'Drive',
        onClick: () => console.log('Drive clicked'),
      },
      {
        label: 'Helpdesk',
        onClick: () => console.log('Helpdesk clicked'),
      },
    ],
  },
  {
    icon: 'lucide-settings',
    label: 'Settings & Members',
    onClick: () => console.log('Settings & Members clicked'),
  },
  {
    icon: 'lucide-moon',
    label: 'Theme',
    submenu: [
      {
        label: 'Light Mode',
        selected: theme.value === 'light',
        onClick: () => {
          theme.value = 'light'
        },
      },
      {
        label: 'Dark Mode',
        selected: theme.value === 'dark',
        onClick: () => {
          theme.value = 'dark'
        },
      },
      {
        label: 'System Default',
        selected: theme.value === 'system',
        onClick: () => {
          theme.value = 'system'
        },
      },
    ],
  },
  {
    group: 'Account',
    options: [
      {
        icon: 'lucide-list-restart',
        label: 'Clear cache',
        onClick: () => console.log('Clear cache clicked'),
      },
      {
        icon: 'lucide-info',
        label: 'About',
        onClick: () => console.log('About clicked'),
      },
      {
        icon: 'lucide-log-out',
        label: 'Log out',
        onClick: () => console.log('Log out clicked'),
      },
    ],
  },
])
</script>

<template>
  <div class="flex flex-col items-center gap-4">
    <Dropdown :options="userMenuItems">
      <template #default="{ open }">
        <button
          class="flex w-56 items-center rounded-5 px-2 py-2 text-left transition-colors"
          :class="open ? 'bg-surface-gray-3' : 'hover:bg-surface-gray-2'"
        >
          <!-- Gameplan logo -->
          <svg
            class="size-8 rounded-4"
            xmlns="http://www.w3.org/2000/svg"
            fill="none"
            viewBox="0 0 44 44"
          >
            <path
              fill="#F90"
              d="M31.429 0H12.57C5.628 0 0 5.628 0 12.571V31.43C0 38.372 5.628 44 12.571 44H31.43C38.372 44 44 38.372 44 31.429V12.57C44 5.628 38.372 0 31.429 0Z"
            />
            <path
              fill="#fff"
              d="M12.571 28.082v-8.093H9.43v7.307a3.93 3.93 0 0 0 3.928 3.929h9.554v-3.143h-10.34Z"
            />
            <path
              fill="#fff"
              d="M30.643 12.76H9.429v3.143h22v12.179h-5.045l-3.457 4.305 2.42 1.996 2.53-3.159h2.766a3.93 3.93 0 0 0 3.928-3.928V16.689a3.93 3.93 0 0 0-3.928-3.929Z"
            />
          </svg>
          <div class="ml-2 min-w-0">
            <div
              class="truncate text-base-medium text-ink-gray-8 leading-none"
            >
              Gameplan
            </div>
            <div class="mt-1 truncate text-p-sm text-ink-gray-5 leading-none">
              Faris
            </div>
          </div>
          <span
            class="lucide-chevron-down ml-auto size-4 text-ink-gray-6 transition-transform"
            :class="open ? 'rotate-180' : ''"
          />
        </button>
      </template>

      <template #item-suffix="{ selected }">
        <span
          v-if="selected"
          class="lucide-check size-4 text-ink-gray-7"
          aria-hidden="true"
        />
      </template>
    </Dropdown>

    <div class="text-p-sm text-ink-gray-5">
      Selected theme: <span class="text-ink-gray-7">{{ theme }}</span>
    </div>
  </div>
</template>
```

## Behavior

### Options

`options` is an array of items and groups:

- An item has a `label` and an `onClick`. It can also have an `icon`, a
  `description`, a `theme` (`gray` or `red`), a `route`, `disabled` and
  `selected`.
- An item with `submenu` opens a nested menu. The submenu takes the same array
  shape.
- A group is `{ group, options }`. `hideLabel` hides the group heading.
  Items outside a group form groups without a heading.
- An item with a `condition` shows only while the function returns true. A
  group with no visible items is left out.

### Switch items

An item with `switch: true` shows a switch set to `switchValue`. Clicking it
calls `onClick` with the new boolean value and keeps the menu open.

### Trigger

Without a trigger slot, `Dropdown` renders a `<Button>` with the `button`
props. Its label defaults to "Options". The button stays pressed while the menu
is open.

The `#trigger` and default slots replace the button. They receive
`{ open, disabled, setOpen, close }`. `close()` is shorthand for
`setOpen(false)`.

### Custom rows

Use `#item-prefix`, `#item-label` and `#item-suffix` to change parts of the
standard row. Use the `#item` slot, or `slots: { item: fn }` on a single
option, only when you need to replace the whole row. Those slots render the
outer menu item element themselves, so keep them for exceptional cases.

## Accessibility

| Keys                                           | Action                                         |
| ---------------------------------------------- | ---------------------------------------------- |
| `Enter` / `Space` / `ArrowDown` on the trigger | Open the menu                                  |
| `ArrowDown` / `ArrowUp`                        | Move focus between items                       |
| `Enter` / `Space`                              | Run the focused item, or open its submenu      |
| `ArrowRight`                                   | Open the focused submenu                       |
| `ArrowLeft`                                    | Close the submenu                              |
| `Escape`                                       | Close the menu and return focus to the trigger |

An icon-only trigger needs a `label` so screen readers can name it.

## API reference

```ts
const propsData = [
  {
    name: 'button',
    description: 'Button configuration (label, icon, size, variant, etc.)',
    required: false,
    type: 'ButtonProps'
  },
  {
    name: 'options',
    description: 'Array of dropdown options or grouped options.',
    required: false,
    type: 'MenuOptions',
    default: '[]'
  },
  {
    name: 'open',
    description: 'Controls the visibility of the dropdown.',
    required: false,
    type: 'boolean',
    default: 'false'
  },
  {
    name: 'align',
    description: 'Alignment of the dropdown content along the trigger edge.',
    required: false,
    type: 'DropdownAlign',
    default: '"start"'
  },
  {
    name: 'side',
    description: 'Side of the trigger the dropdown appears on.',
    required: false,
    type: 'DropdownSide',
    default: '"bottom"'
  },
  {
    name: 'offset',
    description: 'Offset in pixels between trigger and dropdown.',
    required: false,
    type: 'number',
    default: '4'
  },
  {
    name: 'matchTriggerWidth',
    description: 'Whether the dropdown width should match the trigger element.',
    required: false,
    type: 'boolean'
  },
  {
    name: 'portalTo',
    description: 'Teleport target for dropdown portal content. Unset, an embedding host\'s target is used, else `body`.',
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
    description: 'Alternate trigger renderer.',
    type: 'DropdownTriggerSlotProps'
  },
  {
    name: 'trigger',
    description: 'Explicit trigger slot renderer.',
    type: 'DropdownTriggerSlotProps'
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
