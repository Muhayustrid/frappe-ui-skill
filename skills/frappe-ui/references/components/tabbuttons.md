<!-- Upstream: https://github.com/frappe/frappe-ui/blob/b435c66b51c0b19425a2a049b43e3ef2e308a8ab/src/components/TabButtons/TabButtons.md -->

# TabButtons

A row of buttons that picks one value, such as a filter, a setting or a form
field. To switch between panels or pages, use [Tabs](tabs.md) instead.



## Examples

### Settings rows

A value picker at the end of each settings row.

```vue
<script setup>
import { ref } from 'vue'
import { TabButtons } from 'frappe-ui'

const timeFormat = ref('12hrs')
const startDay = ref('sunday')

const timeFormats = [
  { label: '12hrs', value: '12hrs' },
  { label: '24hrs', value: '24hrs' },
]

const startDays = [
  { label: 'Sunday', value: 'sunday' },
  { label: 'Monday', value: 'monday' },
]
</script>

<template>
  <div class="w-full max-w-lg">
    <h3 class="text-lg font-semibold text-ink-gray-9">General</h3>
    <div class="mt-2 divide-y divide-outline-gray-1">
      <div class="flex items-center justify-between gap-4 py-3">
        <div>
          <div class="text-base font-medium text-ink-gray-8">Time format</div>
          <div class="mt-1 text-base text-ink-gray-6">
            Choose the way times are displayed
          </div>
        </div>
        <TabButtons v-model="timeFormat" :options="timeFormats" />
      </div>
      <div class="flex items-center justify-between gap-4 py-3">
        <div>
          <div class="text-base font-medium text-ink-gray-8">Start day</div>
          <div class="mt-1 text-base text-ink-gray-6">
            Choose what day the calendar week should start
          </div>
        </div>
        <TabButtons v-model="startDay" :options="startDays" />
      </div>
    </div>
  </div>
</template>
```

### Calendar toolbar

The default `sm` size has the same height as the toolbar buttons around it.

```vue
<script setup>
import { ref } from 'vue'
import { Button, TabButtons } from 'frappe-ui'

const filter = ref('events')

const filters = [
  { label: 'All', value: 'all' },
  { label: 'Events', value: 'events' },
  { label: 'Tasks', value: 'tasks' },
]
</script>

<template>
  <div class="flex w-full flex-wrap items-center gap-2">
    <Button variant="ghost" label="Jun 2026" icon-right="lucide-chevron-down" />
    <div class="flex items-center">
      <Button variant="ghost" icon="lucide-chevron-left" label="Previous" />
      <Button variant="ghost" label="Today" />
      <Button variant="ghost" icon="lucide-chevron-right" label="Next" />
    </div>
    <Button label="Week" icon-right="lucide-chevron-down" />
    <TabButtons v-model="filter" :options="filters" />
    <Button
      label="Lead"
      icon-left="lucide-users"
      icon-right="lucide-chevron-down"
    />
  </div>
</template>
```

### List or calendar view

Options with `icon` show only the icon. The `label` becomes the name screen
readers announce.

```vue
<script setup>
import { ref } from 'vue'
import { TabButtons } from 'frappe-ui'

const view = ref('list')

const views = [
  { label: 'List view', value: 'list', icon: 'lucide-list' },
  { label: 'Calendar view', value: 'calendar', icon: 'lucide-calendar' },
]
</script>

<template>
  <div class="flex w-full items-center justify-between gap-4">
    <div>
      <h3 class="text-lg font-semibold text-ink-gray-9">Holidays</h3>
      <p class="mt-1 text-base text-ink-gray-6">
        Add holidays here to make sure they're excluded from SLA calculations
      </p>
    </div>
    <TabButtons v-model="view" :options="views" />
  </div>
</template>
```

### Report period

With `fluid`, the buttons share the container width equally.

```vue
<script setup>
import { ref } from 'vue'
import { TabButtons } from 'frappe-ui'

const period = ref('week')

const periods = [
  { label: 'Day', value: 'day' },
  { label: 'Week', value: 'week' },
  { label: 'Month', value: 'month' },
  { label: 'Year', value: 'year' },
]
</script>

<template>
  <div class="w-full max-w-md">
    <TabButtons v-model="period" :options="periods" fluid />
  </div>
</template>
```

### Property panel

Fixed-width `fluid` controls in a property panel. The options mix icon-only and
text items.

```vue
<script setup>
import { ref } from 'vue'
import { TabButtons, TextInput } from 'frappe-ui'

const direction = ref('vertical')
const overflowX = ref('hide')
const align = ref('start')
const style = ref('alphabet')
const radius = ref('0')
const radiusMode = ref('all')
const wrap = ref('no')

const directions = [
  { label: 'Horizontal', value: 'horizontal', icon: 'lucide-move-horizontal' },
  { label: 'Vertical', value: 'vertical', icon: 'lucide-move-vertical' },
]

const overflows = [
  { label: 'Visible', value: 'visible', icon: 'lucide-eye' },
  { label: 'Hidden', value: 'hide', icon: 'lucide-eye-off' },
  { label: 'Scroll', value: 'scroll', icon: 'lucide-arrow-down-to-line' },
  { label: 'Auto', value: 'auto' },
]

const aligns = [
  { label: 'Align start', value: 'start', icon: 'lucide-align-start-vertical' },
  {
    label: 'Align center',
    value: 'center',
    icon: 'lucide-align-center-vertical',
  },
  { label: 'Align end', value: 'end', icon: 'lucide-align-end-vertical' },
]

const styles = [
  { label: 'Alphabet list', value: 'alphabet', icon: 'lucide-letter-text' },
  { label: 'Bullet list', value: 'bullet', icon: 'lucide-list' },
  { label: 'Numbered list', value: 'numbered', icon: 'lucide-list-ordered' },
]

const radiusModes = [
  { label: 'All corners', value: 'all', icon: 'lucide-square' },
  { label: 'Individual corners', value: 'individual', icon: 'lucide-scan' },
]

const wraps = [
  { label: 'Yes', value: 'yes' },
  { label: 'No', value: 'no' },
]
</script>

<template>
  <div class="flex w-72 flex-col gap-3">
    <div class="flex items-center justify-between gap-4">
      <div class="text-base text-ink-gray-7">Direction</div>
      <TabButtons
        v-model="direction"
        :options="directions"
        fluid
        class="w-36"
      />
    </div>
    <div class="flex items-center justify-between gap-4">
      <div class="text-base text-ink-gray-7">Overflow X</div>
      <TabButtons v-model="overflowX" :options="overflows" fluid class="w-36" />
    </div>
    <div class="flex items-center justify-between gap-4">
      <div class="text-base text-ink-gray-7">Align</div>
      <TabButtons v-model="align" :options="aligns" fluid class="w-36" />
    </div>
    <div class="flex items-center justify-between gap-4">
      <div class="text-base text-ink-gray-7">Style</div>
      <TabButtons v-model="style" :options="styles" fluid class="w-36" />
    </div>
    <div class="flex items-center justify-between gap-4">
      <div class="text-base text-ink-gray-7">Border radius</div>
      <div class="flex w-36 items-center gap-2">
        <TextInput v-model="radius" class="min-w-0 flex-1" />
        <TabButtons v-model="radiusMode" :options="radiusModes" />
      </div>
    </div>
    <div class="flex items-center justify-between gap-4">
      <div class="text-base text-ink-gray-7">Wrap</div>
      <TabButtons v-model="wrap" :options="wraps" fluid class="w-36" />
    </div>
  </div>
</template>
```

### Notification filter with counts

The `#prefix` slot puts an icon before each label, and the `#suffix` slot shows
a count after it. Both receive `{ button, active, disabled }`.

```vue
<script setup>
import { ref } from 'vue'
import { TabButtons } from 'frappe-ui'

const tab = ref('unread')

const tabs = [
  { label: 'Unread', value: 'unread' },
  { label: 'Mentions', value: 'mentions' },
  { label: 'All', value: 'all' },
]

const icons = {
  unread: 'lucide-mail',
  mentions: 'lucide-at-sign',
  all: 'lucide-inbox',
}

const counts = { unread: 8, mentions: 3, all: 124 }
</script>

<template>
  <div class="flex w-full max-w-lg items-center justify-between gap-4">
    <h3 class="text-lg font-semibold text-ink-gray-9">Notifications</h3>
    <TabButtons v-model="tab" :options="tabs">
      <template #prefix="{ button }">
        <span
          :class="icons[button.value]"
          class="size-4 shrink-0"
          aria-hidden="true"
        />
      </template>
      <template #suffix="{ button, active }">
        <span
          class="rounded-full px-1.5 text-xs"
          :class="
            active
              ? 'bg-surface-gray-3 text-ink-gray-8'
              : 'bg-surface-gray-2 text-ink-gray-6'
          "
        >
          {{ counts[button.value] }}
        </span>
      </template>
    </TabButtons>
  </div>
</template>
```

## Behavior

### TabButtons or Tabs

TabButtons and Tabs share the same variants, sizes and option fields, and look
the same at the same `variant` and `size`. The difference is meaning:
TabButtons is a radio group that picks a value, and Tabs switches the content
shown below it.

### Options

Each option needs a `value` and a `label`.

- `icon` makes an icon-only tab. The `label` becomes its `aria-label` and
  `title`.
- `iconLeft` shows an icon before the visible label.
- `route` renders the tab as a router link. `href` renders it as an `<a>` that
  opens in a new tab.
- `disabled` makes the tab a disabled `<button>`, even when it has a `route` or
  `href`.
- `onClick` runs when the tab is clicked.

### Without v-model

Without a `v-model`, TabButtons keeps the selected value itself and still
emits `update:modelValue`.

### Vertical

`vertical` stacks the buttons in a column. With `variant="browser-tab"`,
`edge="start"` or `edge="end"` sets the edge of the list the selected tab
attaches to. `start` is the left edge in left-to-right text.

### Attributes

Attributes and classes on `TabButtons` go to the root element. Each tab has a
`data-value` attribute set to its option's `value`, so CSS can target one tab.
Options have no `class` field.

## Accessibility

TabButtons is a `radiogroup`, and each tab is a radio. The group is a single
tab stop: `Tab` moves focus to the selected option, or to the first enabled
option when nothing is selected.

| Keys                                     | Action                                    |
| ---------------------------------------- | ----------------------------------------- |
| `Tab`                                    | Move focus into the group, then out of it |
| `ArrowRight` (`ArrowDown` when vertical) | Select the next option                    |
| `ArrowLeft` (`ArrowUp` when vertical)    | Select the previous option                |
| `Space`                                  | Select the focused option                 |

The arrow keys skip disabled options.

## API reference

```ts
const propsData = [
  {
    name: 'options',
    description: 'List of options to render.',
    required: false,
    type: 'TabButton[]'
  },
  {
    name: 'modelValue',
    description: '',
    required: false,
    type: 'TabValue'
  },
  {
    name: 'variant',
    description: 'Visual variant, shared with the Tabs family.',
    required: false,
    type: 'TabsVariant',
    default: '"subtle"'
  },
  {
    name: 'size',
    description: '',
    required: false,
    type: 'TabsSize',
    default: '"sm"'
  },
  {
    name: 'vertical',
    description: '',
    required: false,
    type: 'boolean',
    default: 'false'
  },
  {
    name: 'edge',
    description: 'browser-tab + vertical only: the edge of the list the tabs attach to.\n`start` is the left edge in left-to-right text.',
    required: false,
    type: 'TabsEdge',
    default: '"start"'
  },
  {
    name: 'fluid',
    description: 'Buttons stretch to fill the container width.',
    required: false,
    type: 'boolean',
    default: 'false'
  }
]

  const slotsData = [
  {
    name: 'prefix',
    description: 'Slot before the tab button label.',
    type: '{ button: TabButton; active: boolean; disabled: boolean; }'
  },
  {
    name: 'suffix',
    description: 'Slot after the tab button label.',
    type: '{ button: TabButton; active: boolean; disabled: boolean; }'
  }
]

  const emitsData = [
  {
    name: 'update:modelValue',
    description: 'Fired when the model value changes.',
    type: '[value: TabValue]'
  }
]

  const exposedData = [
  {
    name: 'focus',
    description: 'Moves focus to the selected tab, or to the first enabled tab when nothing\nis selected. That is the tab `Tab` reaches, since the group is one tab\nstop. Disabled tabs are skipped, so a group with no enabled tab does\nnothing.',
    type: '(options?: FocusOptions) => void'
  }
]
```
