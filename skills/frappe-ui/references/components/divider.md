<!-- Upstream: https://github.com/frappe/frappe-ui/blob/b435c66b51c0b19425a2a049b43e3ef2e308a8ab/src/components/Divider/Divider.md -->

# Divider

A horizontal or vertical line that separates content. It can also hold a small
button on the line.



## Examples

### Toolbar groups

Vertical dividers between groups of toolbar buttons. `flex-item` makes each
divider as tall as its flex row.

```vue
<script setup lang="ts">
import { Button, Divider } from 'frappe-ui'
</script>

<template>
  <div
    class="flex items-center gap-1 rounded-5 border border-outline-gray-2 bg-surface-base p-1"
  >
    <Button variant="ghost" icon="lucide-bold" label="Bold" />
    <Button variant="ghost" icon="lucide-italic" label="Italic" />
    <Button variant="ghost" icon="lucide-underline" label="Underline" />
    <Divider orientation="vertical" flex-item class="mx-1" />
    <Button variant="ghost" icon="lucide-list" label="Bullet list" />
    <Button variant="ghost" icon="lucide-list-ordered" label="Numbered list" />
    <Divider orientation="vertical" flex-item class="mx-1" />
    <Button variant="ghost" icon="lucide-link" label="Link" />
    <Button variant="ghost" icon="lucide-image" label="Image" />
  </div>
</template>
```

### Older comments

A divider with an `action` above a comment thread. The button shows the older
comments.

```vue
<script setup lang="ts">
import { computed, ref } from 'vue'
import { Avatar, Divider } from 'frappe-ui'

type Comment = { name: string; email: string; text: string; time: string }

const older: Comment[] = [
  {
    name: 'Priya Shah',
    email: 'priya@example.com',
    text: 'Can we move the launch to Thursday?',
    time: '3 days ago',
  },
  {
    name: 'Tom Baker',
    email: 'tom@example.com',
    text: 'Thursday works for the design team.',
    time: '2 days ago',
  },
]

const recent: Comment[] = [
  {
    name: 'Jane Cooper',
    email: 'jane@example.com',
    text: 'Release notes are ready for review.',
    time: '1 hour ago',
  },
]

const showOlder = ref(false)

const olderAction = computed(() => ({
  label: `Show ${older.length} older comments`,
  onClick: () => {
    showOlder.value = true
  },
}))
</script>

<template>
  <div class="flex w-full max-w-md flex-col gap-4">
    <Divider v-if="!showOlder" :action="olderAction" />
    <div
      v-for="comment in showOlder ? [...older, ...recent] : recent"
      :key="comment.text"
      class="flex gap-3"
    >
      <Avatar
        :label="comment.name"
        :image="`https://i.pravatar.cc/80?u=${comment.email}`"
        size="md"
      />
      <div class="min-w-0">
        <div class="flex items-baseline gap-2">
          <span class="text-base-medium text-ink-gray-8">
            {{ comment.name }}
          </span>
          <span class="text-sm text-ink-gray-5">{{ comment.time }}</span>
        </div>
        <p class="mt-1 text-p-base text-ink-gray-7">{{ comment.text }}</p>
      </div>
    </div>
  </div>
</template>
```

## Behavior

### Action

`action` puts a button on the line. It takes the same fields as `Button`, such
as `label`, `theme`, `variant`, `size` and icons, plus `onClick`. The button
defaults to `variant="outline"` and `size="sm"`. The line runs the full length
of the divider, behind the button.

`align` places the button at the `start`, `center` or `end` of the line. The
default is `center`. Without an `action`, `align` does nothing.

### Vertical dividers

With `orientation="vertical"`, the divider takes the full height of its own
box. Inside a flex row whose items have different heights, add `flex-item`, so
the divider stretches to the height of the row.

## Accessibility

A plain divider renders an `<hr>`, which screen readers announce as a
separator. A divider with an `action` renders the line as an element with
`role="separator"` and `aria-orientation`.

## API reference

```ts
const propsData = [
  {
    name: 'orientation',
    description: 'Which way the rule runs.',
    required: false,
    type: '"horizontal" | "vertical"',
    default: '"horizontal"'
  },
  {
    name: 'align',
    description: 'Where the action sits along the rule. Ignored without an `action`, since a\nplain rule has nothing to place.',
    required: false,
    type: '"start" | "center" | "end"',
    default: '"center"'
  },
  {
    name: 'flexItem',
    description: 'Stretches the divider to the cross-axis size of its flex parent\n(`align-self: stretch`) instead of taking the full height of its own box.\nUse it for a vertical divider between flex items of differing heights.',
    required: false,
    type: 'boolean'
  },
  {
    name: 'action',
    description: 'Renders a button on the rule instead of a bare `<hr>`. The rule is drawn\nbehind the button and runs the full length of the divider.',
    required: false,
    type: 'DividerAction'
  }
]
```
