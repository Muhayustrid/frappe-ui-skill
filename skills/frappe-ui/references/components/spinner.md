<!-- Upstream: https://github.com/frappe/frappe-ui/blob/b435c66b51c0b19425a2a049b43e3ef2e308a8ab/src/components/Spinner/Spinner.md -->

# Spinner

Shows that something is loading, with no known end. For a spinner with a text
label, use [LoadingText](loadingtext.md).



## Examples

### Sync status

A spinner without `theme` takes the text color, so it matches the status line
it sits in. `track` draws a faint full circle behind the arc.

```vue
<script setup lang="ts">
import { Spinner } from 'frappe-ui'
</script>

<template>
  <div class="flex w-80 flex-col gap-2">
    <div class="flex items-center gap-2 text-sm text-ink-gray-5">
      <Spinner size="sm" track />
      Syncing 12 files
    </div>
    <div class="flex items-center gap-2 text-sm text-ink-red-7">
      <Spinner size="sm" track />
      Removing 3 members
    </div>
  </div>
</template>
```

### Loading more rows

A `gray` spinner at the foot of a list while the next page loads.

```vue
<script setup lang="ts">
import { Spinner } from 'frappe-ui'

const tasks = [
  'Update onboarding copy',
  'Fix invoice rounding',
  'Review Q3 roadmap',
]
</script>

<template>
  <div
    class="w-80 divide-y divide-outline-gray-1 rounded-6 border border-outline-gray-1"
  >
    <div
      v-for="task in tasks"
      :key="task"
      class="px-3 py-2.5 text-base text-ink-gray-8"
    >
      {{ task }}
    </div>
    <div class="flex justify-center py-3">
      <Spinner size="md" theme="gray" />
    </div>
  </div>
</template>
```

### Loading buttons

`Button` has a `loading` prop that shows a spinner sized to the button, so you
do not place a `Spinner` in a button yourself. `loadingText` replaces the
label while it loads.

```vue
<script setup lang="ts">
import { Button } from 'frappe-ui'
</script>

<template>
  <div class="flex flex-wrap items-center gap-3">
    <Button
      theme="gray"
      variant="solid"
      icon="lucide-save"
      :loading="true"
      size="xs"
    />
    <Button
      theme="gray"
      variant="solid"
      :loading="true"
      loading-text="Discover"
      size="xs"
    />
    <Button
      theme="gray"
      variant="solid"
      :loading="true"
      loading-text="Discover"
    />
    <Button
      theme="red"
      variant="subtle"
      :loading="true"
      loading-text="Deleting"
      size="md"
    />
    <Button
      theme="gray"
      variant="ghost"
      :loading="true"
      loading-text="Loading"
      size="md"
    />
    <Button
      theme="gray"
      variant="subtle"
      :loading="true"
      loading-text="Get app"
      size="lg"
    />
  </div>
</template>
```

## Behavior

### Size

`size` is `xs`, `sm`, `md` or `lg`: 12, 14, 16 and 20px across. Leave `size`
out to size the spinner with a class such as `size-8`. It is 16px when nothing
sets a size, and the ring thickness stays in proportion at any size.

### Color

`theme="gray"` and `theme="red"` set a fixed color. Leave `theme` out to use
the current text color, which you can set with a `text-ink-*` class on the
spinner or a parent.

## Accessibility

The spinner has `role="status"` and the label "Loading".

## API reference

```ts
const propsData = [
  {
    name: 'size',
    description: 'Diameter — xs=12px, sm=14px, md=16px, lg=20px. Omit to size with classes (default 16px).',
    required: false,
    type: 'SpinnerSize'
  },
  {
    name: 'theme',
    description: 'Spinner color. Omit to inherit the text color.',
    required: false,
    type: 'SpinnerTheme'
  },
  {
    name: 'track',
    description: 'Show a faint track behind the arc',
    required: false,
    type: 'boolean',
    default: 'false'
  }
]
```
