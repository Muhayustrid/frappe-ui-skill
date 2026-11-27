<!-- Upstream: https://github.com/frappe/frappe-ui/blob/b435c66b51c0b19425a2a049b43e3ef2e308a8ab/src/components/Icon/Icon.md -->

# Icon

Renders one icon from a `lucide-*` name, an emoji, or a Vue component. Most
components take an `icon` prop and render it for you, so use `Icon` only for
an icon outside those props.

```vue
<script setup lang="ts">
import { Icon } from 'frappe-ui'
</script>

<template>
  <div class="flex items-center gap-4 text-ink-gray-8">
    <Icon icon="lucide-plus" class="size-5" />
    <Icon icon="lucide-trash-2" class="size-5" />
    <Icon icon="lucide-check" class="size-5" />
    <Icon icon="🎉" class="text-xl" />
  </div>
</template>
```

## Examples

### Space list

Each space stores its icon as a string, and people can pick a lucide icon or an
emoji. `Icon` renders either one from the same `icon` prop.

```vue
<script setup lang="ts">
import { Icon } from 'frappe-ui'

// Each space stores its icon as a string. People pick either a lucide icon
// or an emoji, and Icon renders both.
const spaces = [
  { name: 'Engineering', icon: 'lucide-code' },
  { name: 'Design', icon: '🎨' },
  { name: 'Marketing', icon: 'lucide-megaphone' },
  { name: 'Offsite 2026', icon: '🏝️' },
]
</script>

<template>
  <div class="w-60 space-y-0.5">
    <div
      v-for="space in spaces"
      :key="space.name"
      class="flex h-7 items-center gap-2 rounded-4 px-2 text-base text-ink-gray-8 hover:bg-surface-gray-2"
    >
      <Icon :icon="space.icon" class="size-4 text-ink-gray-6" />
      {{ space.name }}
    </div>
  </div>
</template>
```

### Custom SVG

Pass any Vue component as `icon` to render an icon that lucide does not have,
such as a product logo.

```vue
<script setup lang="ts">
import { h } from 'vue'
import { Icon } from 'frappe-ui'

// The escape hatch: pass any Vue component as the icon source.
const CustomIcon = () =>
  h(
    'svg',
    { viewBox: '0 0 24 24', fill: 'none', class: 'size-5' },
    [
      h('circle', {
        cx: 12,
        cy: 12,
        r: 9,
        stroke: 'currentColor',
        'stroke-width': 2,
      }),
    ],
  )
</script>

<template>
  <Icon :icon="CustomIcon" class="text-ink-gray-8" />
</template>
```

## Behavior

### Icon sources

`icon` accepts three forms:

- A `lucide-*` string renders a `<span>` with that icon class.
- A string with no letters or digits, such as an emoji or a symbol, renders
  as text.
- A Vue component renders through `<component :is>`.

Any other string, such as a bare name like `"plus"`, renders nothing and logs
a warning in development. `null`, `undefined` and an empty string also render
nothing.

### `icon` and `name`

`name` accepts the same values as `icon`. When both are set, `icon` wins. An
explicit `icon=""` or `:icon="null"` renders nothing, while
`:icon="undefined"` falls back to `name`.

### Size and color

`Icon` has no size or color props. Set them with classes, such as
`class="size-4 text-ink-gray-6"`. A lucide icon takes the text color. Class,
style and other attributes land on the rendered element.

## Accessibility

Every icon renders with `aria-hidden="true"`, so screen readers skip it. When
an icon is the only content of a button or link, give that element an
`aria-label`.

## API reference

```ts
const propsData = [
  {
    name: 'icon',
    description: 'Icon source. Supported forms:\n- `lucide-*` string  → rendered via the Tailwind mask plugin.\n- emoji / symbol string → rendered as plain text.\n- Vue component → rendered via `<component :is>`.\nAny other string (e.g. a bare feather-style name) is unsupported —\nit renders nothing and warns once in dev. Falsy values render nothing.',
    required: false,
    type: 'string | Component | null'
  },
  {
    name: 'name',
    description: 'Supported alternate name for `icon`. `icon` takes precedence when both\nprops are supplied.',
    required: false,
    type: 'string | Component | null'
  }
]
```
