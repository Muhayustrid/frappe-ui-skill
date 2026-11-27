<!-- Upstream: https://github.com/frappe/frappe-ui/blob/b435c66b51c0b19425a2a049b43e3ef2e308a8ab/src/components/Skeleton/Skeleton.md -->

# Skeleton

A gray placeholder block that pulses while content loads. Put it where the
content will appear, then replace it with the real element when the data
arrives.

```vue
<script setup lang="ts">
import { Skeleton } from 'frappe-ui'
</script>

<template>
  <div class="w-64 space-y-3">
    <div v-for="i in 4" :key="i" class="flex items-center gap-3">
      <Skeleton class="size-10 rounded-full" />
      <div class="flex-1 space-y-2">
        <Skeleton class="h-4 w-40" />
        <Skeleton class="h-4 w-24" />
      </div>
    </div>
  </div>
</template>
```

## Examples

### Article card

Blocks for a cover image, a title and two lines of text, sized like the card
they stand in for.

```vue
<script setup lang="ts">
import { Skeleton } from 'frappe-ui'
</script>

<template>
  <div class="w-64 space-y-4">
    <Skeleton class="h-40 w-full rounded-6" />
    <Skeleton class="h-5 w-3/5" />
    <Skeleton class="h-4 w-full" />
    <Skeleton class="h-4 w-4/5" />
  </div>
</template>
```

### Paragraph

Lines of different widths read as text.

```vue
<script setup lang="ts">
import { Skeleton } from 'frappe-ui'
</script>

<template>
  <div class="space-y-3">
    <Skeleton class="h-5 w-48" />
    <Skeleton class="h-5 w-72" />
    <Skeleton class="h-5 w-60" />
  </div>
</template>
```

## Behavior

### Size and shape

`Skeleton` takes no props. Set its size and shape with classes or inline
styles on the element, such as `class="h-5 w-48"` for a line of text or
`class="size-10 rounded-full"` for an avatar.

## Accessibility

Each block has `aria-hidden="true"`, so screen readers skip it. To announce
the loading state, mark the region that is loading with `aria-busy="true"` or
add a visually hidden status message.

## API reference

```ts

```
