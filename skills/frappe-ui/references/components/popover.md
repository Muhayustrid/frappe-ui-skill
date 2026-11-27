<!-- Upstream: https://github.com/frappe/frappe-ui/blob/b435c66b51c0b19425a2a049b43e3ef2e308a8ab/src/components/Popover/Popover.md -->

# Popover

A floating panel that opens from a trigger on click. For a panel that opens on
hover, such as a profile preview, use [`HoverCard`](hovercard.md) instead.

```vue
<script setup lang="ts">
import { ref } from 'vue'
import { Button, Checkbox, Popover } from 'frappe-ui'

const statuses = ['Open', 'Replied', 'Resolved', 'Closed']
const selected = ref<string[]>(['Open'])
const draft = ref<Record<string, boolean>>({})

function load() {
  draft.value = Object.fromEntries(
    statuses.map((s) => [s, selected.value.includes(s)]),
  )
}

function apply(close: () => void) {
  selected.value = statuses.filter((s) => draft.value[s])
  close()
}
</script>

<template>
  <Popover side="bottom" align="start" @open="load">
    <template #trigger>
      <Button
        icon-left="lucide-list-filter"
        :label="selected.length ? `Status: ${selected.length}` : 'Filter'"
      />
    </template>
    <template #default="{ close }">
      <div class="w-56 p-3">
        <p class="text-sm-medium text-ink-gray-5">Status</p>
        <div class="mt-2 flex flex-col gap-2">
          <Checkbox
            v-for="status in statuses"
            :key="status"
            v-model="draft[status]"
            :label="status"
          />
        </div>
        <div class="mt-3 flex justify-end gap-2">
          <Button label="Cancel" @click="close()" />
          <Button variant="solid" label="Apply" @click="apply(close)" />
        </div>
      </div>
    </template>
  </Popover>
</template>
```

## Examples

### Workspace switcher

`match-trigger-width` makes the panel at least as wide as the trigger, so the
list lines up under the button.

```vue
<script setup lang="ts">
import { ref } from 'vue'
import { Avatar, Popover } from 'frappe-ui'

const workspaces = ['Acme Inc', 'Globex', 'Initech']
const current = ref(workspaces[0])

function pick(value: string, close: () => void) {
  current.value = value
  close()
}
</script>

<template>
  <Popover match-trigger-width>
    <template #trigger>
      <button
        type="button"
        class="flex h-8 w-60 items-center gap-2 rounded-4 bg-surface-gray-2 px-2 text-base text-ink-gray-8 hover:bg-surface-gray-3"
      >
        <Avatar :label="current" size="sm" shape="square" theme="blue" />
        <span class="flex-1 truncate text-left">{{ current }}</span>
        <span
          class="lucide-chevrons-up-down size-4 text-ink-gray-5"
          aria-hidden="true"
        />
      </button>
    </template>
    <template #default="{ close }">
      <div class="p-1">
        <button
          v-for="workspace in workspaces"
          :key="workspace"
          type="button"
          class="flex w-full items-center gap-2 rounded-4 px-2 py-1.5 text-left text-base text-ink-gray-8 hover:bg-surface-gray-2"
          @click="pick(workspace, close)"
        >
          <Avatar :label="workspace" size="sm" shape="square" theme="blue" />
          <span class="flex-1">{{ workspace }}</span>
          <span
            v-if="workspace === current"
            class="lucide-check size-4 text-ink-gray-7"
            aria-hidden="true"
          />
        </button>
      </div>
    </template>
  </Popover>
</template>
```

### Set a status

`:dismissible="false"` keeps the panel open on an outside click, so a half-typed
status is not lost. `v-model:open` closes it from the Cancel and Save buttons.

```vue
<script setup lang="ts">
import { ref } from 'vue'
import { Button, Popover, TextInput } from 'frappe-ui'

const status = ref('In a meeting')
const draft = ref('')
const open = ref(false)

function save() {
  status.value = draft.value
  open.value = false
}
</script>

<template>
  <Popover v-model:open="open" :dismissible="false" @open="draft = status">
    <template #trigger>
      <Button
        icon-left="lucide-message-circle"
        :label="status || 'Set status'"
      />
    </template>
    <template #default>
      <div class="w-64 p-3">
        <TextInput v-model="draft" placeholder="What are you up to?" />
        <div class="mt-3 flex justify-end gap-2">
          <Button label="Cancel" @click="open = false" />
          <Button variant="solid" label="Save" @click="save" />
        </div>
      </div>
    </template>
  </Popover>
</template>
```

### Color picker

`bare` removes the panel's background, border, shadow and rounding, so the
content draws its own surface.

```vue
<script setup lang="ts">
import { ref } from 'vue'
import { Popover } from 'frappe-ui'

const colors = [
  { name: 'Gray', class: 'bg-surface-gray-7' },
  { name: 'Blue', class: 'bg-surface-blue-7' },
  { name: 'Green', class: 'bg-surface-green-7' },
  { name: 'Amber', class: 'bg-surface-amber-7' },
  { name: 'Red', class: 'bg-surface-red-7' },
  { name: 'Violet', class: 'bg-surface-violet-7' },
]
const color = ref(colors[1])

function pick(value: typeof color.value, close: () => void) {
  color.value = value
  close()
}
</script>

<template>
  <div class="flex items-center gap-2 text-base text-ink-gray-8">
    Label color
    <Popover bare>
      <template #trigger>
        <button
          type="button"
          class="size-6 rounded-full"
          :class="color.class"
          :aria-label="`Label color: ${color.name}`"
        />
      </template>
      <template #default="{ close }">
        <div
          class="grid grid-cols-6 gap-2 rounded-5 bg-surface-gray-10 p-2 shadow-xl"
        >
          <button
            v-for="option in colors"
            :key="option.name"
            type="button"
            class="size-6 rounded-full"
            :class="option.class"
            :aria-label="option.name"
            @click="pick(option, close)"
          />
        </div>
      </template>
    </Popover>
  </div>
</template>
```

### Field help

`side="right"` opens the panel beside the icon, and `arrow` points it back at
the icon.

```vue
<script setup lang="ts">
import { Popover } from 'frappe-ui'
</script>

<template>
  <div class="flex items-center gap-1.5 text-base text-ink-gray-8">
    Billing email
    <Popover side="right" arrow>
      <template #trigger>
        <button
          type="button"
          class="grid size-5 place-items-center rounded-full text-ink-gray-5 hover:bg-surface-gray-2"
          aria-label="About billing email"
        >
          <span class="lucide-circle-help size-4" aria-hidden="true" />
        </button>
      </template>
      <template #default>
        <p class="w-60 p-3 text-p-sm text-ink-gray-7">
          Invoices and payment receipts go to this address. It can differ from
          the account owner's email.
        </p>
      </template>
    </Popover>
  </div>
</template>
```

### Search suggestions

`trigger="manual"` and `:auto-focus="false"` let typing in the input open the
panel while the caret stays in the input.

```vue
<script setup lang="ts">
import { computed, ref } from 'vue'
import { Popover, TextInput } from 'frappe-ui'

const fruits = [
  'Apricot',
  'Blackberry',
  'Cherry',
  'Date',
  'Elderberry',
  'Fig',
  'Grape',
]

const query = ref('')
const open = ref(false)

const matches = computed(() =>
  fruits.filter((f) => f.toLowerCase().includes(query.value.toLowerCase())),
)

function onInput(value: string) {
  query.value = value
  open.value = value.length > 0 && matches.value.length > 0
}

function pick(fruit: string) {
  query.value = fruit
  open.value = false
}
</script>

<template>
  <!--
    manual: clicking the input places the caret instead of toggling the panel.
    auto-focus false: the panel opens without taking focus off the input.
  -->
  <Popover
    v-model:open="open"
    trigger="manual"
    :auto-focus="false"
    match-trigger-width
  >
    <template #trigger>
      <TextInput
        :model-value="query"
        placeholder="Search fruit"
        class="w-56"
        @update:model-value="onInput"
      />
    </template>
    <template #default>
      <div class="p-1">
        <button
          v-for="fruit in matches"
          :key="fruit"
          type="button"
          class="block w-full rounded-4 px-2 py-1.5 text-left text-base text-ink-gray-8 hover:bg-surface-gray-2"
          @click="pick(fruit)"
        >
          {{ fruit }}
        </button>
      </div>
    </template>
  </Popover>
</template>
```

## Behavior

### Trigger and content

Put the trigger in `#trigger` and the panel content in `#default`. A click on
the trigger opens and closes the panel. Both slots receive
`{ open, setOpen, close }`, where `close()` is the same as `setOpen(false)`.

### Open state

Bind `v-model:open` only when something outside the popover needs to open or
close it. The popover emits `open` when it opens and `close` when it closes.

### Position

`side` (`top`, `right`, `bottom`, `left`) picks the edge of the trigger the
panel opens on, and `align` (`start`, `center`, `end`) its position along that
edge. `offset` sets the gap in pixels. The panel flips and shifts to stay
inside the screen. `collisionPadding` sets the space it keeps from the screen
edge.

### Closing

The popover closes on a click outside it and on `Escape`. Set
`:dismissible="false"` to turn off both, for a panel with its own Save and
Cancel buttons.

### Reference element

The panel is placed against the trigger. Pass an element as `reference` to
place it against that element instead. Use it when the trigger is a labelled
field: pass the input row, and the panel opens under the input instead of
under the field's description. It works with both `trigger` values.

### Typing in the trigger

Two props stop a panel from getting in the way of the input that opens it.
`trigger="manual"` turns off the click toggle: a click on the input only places
the caret, and only `v-model:open` opens and closes the panel.
`:auto-focus="false"` keeps focus in the input when the panel opens.

### Attributes

`<Popover>` renders no element of its own, so a `class` or `style` on it goes
nowhere. Put them on the element inside `#trigger`.

## Accessibility

<kbd>Enter</kbd> or <kbd>Space</kbd> on a focused trigger opens and closes the
panel, and <kbd>Escape</kbd> closes it. The trigger gets `aria-expanded` and
`aria-controls`.

`trigger="manual"` removes `aria-expanded` and `aria-controls`. Add the
combobox pattern yourself, or use [`Combobox`](combobox.md), which has it.

## API reference

```ts
const propsData = [
  {
    name: 'open',
    description: 'Controls visibility (v-model:open).',
    required: false,
    type: 'boolean'
  },
  {
    name: 'side',
    description: 'Side of the trigger to render the content on.',
    required: false,
    type: 'PopoverSide',
    default: '"bottom"'
  },
  {
    name: 'align',
    description: 'Alignment of the content along the chosen side.',
    required: false,
    type: 'PopoverAlign',
    default: '"start"'
  },
  {
    name: 'offset',
    description: 'Distance in px between the trigger and the content.',
    required: false,
    type: 'number',
    default: '4'
  },
  {
    name: 'portalTo',
    description: 'Where to portal the content. Unset, an embedding host\'s target is used, else `body`.',
    required: false,
    type: 'PortalTarget'
  },
  {
    name: 'collisionPadding',
    description: 'Padding in px kept from the viewport edge during collision handling.',
    required: false,
    type: 'number',
    default: '10'
  },
  {
    name: 'dismissible',
    description: 'Whether the popover closes on outside interaction (click/focus).',
    required: false,
    type: 'boolean',
    default: 'true'
  },
  {
    name: 'autoFocus',
    description: 'Whether the content takes focus when it opens. Set to `false` when typing\nin the trigger drives the panel, so the caret stays in the input.\nDefault: `true`.',
    required: false,
    type: 'boolean',
    default: 'true'
  },
  {
    name: 'trigger',
    description: 'What opens the popover. `click` toggles it from the trigger element.\n`manual` does nothing on click; only `v-model:open` opens and closes it.\nIn `manual` mode the trigger gets no `aria-expanded` or `aria-controls`.\nDefault: `click`.',
    required: false,
    type: '"click" | "manual"',
    default: '"click"'
  },
  {
    name: 'reference',
    description: 'Element to position the content against, instead of the trigger. Use it\nwhen the trigger is a labelled field, so the panel sits under the input row\nrather than under the description.',
    required: false,
    type: 'Element'
  },
  {
    name: 'matchTriggerWidth',
    description: 'Whether the content\'s min-width matches the trigger width.',
    required: false,
    type: 'boolean',
    default: 'false'
  },
  {
    name: 'bare',
    description: 'Render `#default` without the panel shell (no background, border, shadow,\nor rounding). The content brings its own surface. Mirrors Dialog\'s `bare`.',
    required: false,
    type: 'boolean',
    default: 'false'
  },
  {
    name: 'arrow',
    description: 'Render a small arrow pointing at the trigger. Styled to match the panel\nsurface.',
    required: false,
    type: 'boolean',
    default: 'false'
  }
]

  const slotsData = [
  {
    name: 'trigger',
    description: 'Trigger element. Rendered via reka PopoverTrigger as-child.',
    type: 'PopoverSlotProps'
  },
  {
    name: 'default',
    description: 'Popover content, rendered inside the shared panel shell unless `bare`.',
    type: 'PopoverSlotProps'
  }
]

  const emitsData = [
  {
    name: 'open',
    description: 'Fired when the component opens.',
    type: '[]'
  },
  {
    name: 'update:open',
    description: 'Fired when the open state changes.',
    type: '[value: boolean]'
  },
  {
    name: 'close',
    description: 'Fired when the component closes.',
    type: '[]'
  }
]

  const exposedData = [
  {
    name: 'open',
    description: 'Opens the popover. Does nothing when it is already open.',
    type: '() => void'
  },
  {
    name: 'close',
    description: 'Closes the popover. Does nothing when it is already closed.',
    type: '() => void'
  },
  {
    name: 'contentEl',
    description: 'The content element. `null` while the popover is closed.',
    type: 'HTMLElement | null'
  }
]
```
