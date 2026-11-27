<!-- Upstream: https://github.com/frappe/frappe-ui/blob/b435c66b51c0b19425a2a049b43e3ef2e308a8ab/src/components/ScrollArea/ScrollArea.md -->

# ScrollArea

A scroll container with scrollbars that look the same in every browser. The
scrollbars show while the pointer is over the area.

```vue
<script setup lang="ts">
import { ScrollArea } from 'frappe-ui'

const items = Array.from({ length: 30 }, (_, i) => `Item ${i + 1}`)
</script>

<template>
  <ScrollArea class="h-64 w-64 rounded-5 border bg-surface-base">
    <div class="space-y-1 p-3">
      <div
        v-for="item in items"
        :key="item"
        class="rounded-4 px-2 py-1.5 text-base text-ink-gray-8 hover:bg-surface-gray-2"
      >
        {{ item }}
      </div>
    </div>
  </ScrollArea>
</template>
```

## Examples

### Wide table

`orientation="both"` lets a table scroll in both directions, with one
scrollbar per axis.

```vue
<script setup lang="ts">
import { ScrollArea } from 'frappe-ui'

const months = [
  'Jan',
  'Feb',
  'Mar',
  'Apr',
  'May',
  'Jun',
  'Jul',
  'Aug',
  'Sep',
  'Oct',
  'Nov',
  'Dec',
]
const regions = [
  'North America',
  'South America',
  'Western Europe',
  'Eastern Europe',
  'Middle East',
  'Africa',
  'South Asia',
  'East Asia',
  'Southeast Asia',
  'Oceania',
  'Central America',
  'Caribbean',
]
const rows = regions.map((region, r) => ({
  region,
  values: months.map((_, m) => ((r * 7 + m * 13) % 90) + 10),
}))
</script>

<template>
  <ScrollArea
    orientation="both"
    class="h-72 w-full max-w-lg rounded-5 border bg-surface-base"
  >
    <table class="text-base text-ink-gray-8">
      <thead>
        <tr class="border-b text-ink-gray-5">
          <th class="whitespace-nowrap px-3 py-2 text-left font-medium">
            Region
          </th>
          <th
            v-for="month in months"
            :key="month"
            class="px-3 py-2 text-right font-medium"
          >
            {{ month }}
          </th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="row in rows" :key="row.region" class="border-b">
          <td class="whitespace-nowrap px-3 py-2">{{ row.region }}</td>
          <td
            v-for="(value, i) in row.values"
            :key="i"
            class="px-3 py-2 text-right tabular-nums"
          >
            {{ value }}k
          </td>
        </tr>
      </tbody>
    </table>
  </ScrollArea>
</template>
```

## Behavior

### Orientation

`orientation` picks which scrollbars render. `vertical` (the default) and
`horizontal` render one. `both` renders two, one per axis, for content that can
overflow either way, such as a wide table or a canvas.

### Hiding the scrollbars

The scrollbars hide `scrollHideDelay` milliseconds after the pointer leaves
the area. The default is 600.

### Viewport classes

The element that scrolls is inside the root, so a `class` on `ScrollArea` does
not reach it. `viewportClass` adds classes to that element, for layout that has
to sit on the scrolling element itself: padding, a grid, a minimum width, or
`h-full` on its child.

### Where it is used

`ScrollArea` is a general component, not only part of the app shell.
`DesktopShell` uses it for its content area, and `SettingsBody` uses it for a
settings panel.

## API reference

```ts
const propsData = [
  {
    name: 'orientation',
    description: 'Which scrollbars to render.',
    required: false,
    type: '"vertical" | "horizontal" | "both"',
    default: '"vertical"'
  },
  {
    name: 'scrollHideDelay',
    description: 'Idle delay (ms) before the thumb fades out.',
    required: false,
    type: 'number',
    default: '600'
  },
  {
    name: 'viewportClass',
    description: 'Extra classes for the scrolling viewport (e.g. padding).',
    required: false,
    type: 'string'
  }
]

  const slotsData = [
  {
    name: 'default',
    description: 'The scrolling content.',
    type: 'any'
  }
]

  const exposedData = [
  {
    name: 'viewportElement',
    description: 'The element that actually scrolls. Null until mounted.',
    type: 'HTMLElement | null'
  }
]
```
