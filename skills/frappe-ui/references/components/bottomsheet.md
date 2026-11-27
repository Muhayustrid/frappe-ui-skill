<!-- Upstream: https://github.com/frappe/frappe-ui/blob/b435c66b51c0b19425a2a049b43e3ef2e308a8ab/src/components/BottomSheet/BottomSheet.md -->

# BottomSheet

A panel that slides up from the bottom edge of a mobile screen and blocks the
page until it is closed. On desktop, render [`Dialog`](dialog.md) instead: the
two close in different ways, so they are separate components.

```vue
<script setup lang="ts">
import { ref } from 'vue'
import { BottomSheet, Button } from 'frappe-ui'

const open = ref(false)
const options = ['Rename', 'Duplicate', 'Move to folder', 'Delete']
</script>

<template>
  <Button label="Actions" icon-left="lucide-ellipsis" @click="open = true" />

  <BottomSheet v-model:open="open" title="report.pdf">
    <div class="flex flex-col p-2 pb-8">
      <button
        v-for="option in options"
        :key="option"
        class="rounded-4 px-3 py-2.5 text-start text-p-base text-ink-gray-8 hover:bg-surface-gray-2"
        @click="open = false"
      >
        {{ option }}
      </button>
    </div>
  </BottomSheet>
</template>
```

## Examples

### Pick from a long list

The sheet grows with its content up to 90% of the screen height, then the
content scrolls inside it.

```vue
<script setup lang="ts">
import { ref } from 'vue'
import { BottomSheet, Button } from 'frappe-ui'

const open = ref(false)
const current = ref('Design')
const spaces = [
  'Announcements',
  'Customer success',
  'Design',
  'Engineering',
  'Finance',
  'Hiring',
  'Infrastructure',
  'Legal',
  'Marketing',
  'Mobile',
  'Operations',
  'Partnerships',
  'Product',
  'Research',
  'Sales',
  'Security',
  'Support',
  'Website',
]

function pick(space: string) {
  current.value = space
  open.value = false
}
</script>

<template>
  <Button
    :label="`Space: ${current}`"
    icon-right="lucide-chevron-down"
    @click="open = true"
  />

  <BottomSheet v-model:open="open" title="Move to space">
    <div class="flex flex-col p-2 pb-8">
      <button
        v-for="space in spaces"
        :key="space"
        type="button"
        class="flex items-center justify-between rounded-4 px-3 py-2.5 text-start text-p-base text-ink-gray-8 hover:bg-surface-gray-2"
        @click="pick(space)"
      >
        {{ space }}
        <span
          v-if="space === current"
          class="lucide-check size-4 text-ink-gray-7"
          aria-hidden="true"
        />
      </button>
    </div>
  </BottomSheet>
</template>
```

### Discard a draft

`:dismissible="false"` keeps the sheet open until the person picks one of the
buttons.

```vue
<script setup lang="ts">
import { ref } from 'vue'
import { BottomSheet, Button } from 'frappe-ui'

const open = ref(false)
</script>

<template>
  <Button label="Close editor" @click="open = true" />

  <BottomSheet v-model:open="open" title="Discard draft?" :dismissible="false">
    <div class="p-4 pb-8">
      <p class="text-p-base text-ink-gray-7">
        Your reply to Jane has not been sent. Discard it, or keep editing to
        send it later.
      </p>
      <div class="mt-4 flex gap-2">
        <Button class="flex-1" label="Keep editing" @click="open = false" />
        <Button
          class="flex-1"
          variant="solid"
          theme="red"
          label="Discard"
          @click="open = false"
        />
      </div>
    </div>
  </BottomSheet>
</template>
```

## Behavior

### Open state

Bind `v-model:open`. The sheet takes its height from its content, up to 90% of
the screen height. Past that, the default slot scrolls.

### Title

`title` shows a centered heading below the grab handle.

### Closing

The sheet closes on a click outside it, on `Escape`, and on a swipe down.
`dismissible` is `true` by default. Set it to `false` to turn off all three at
once, for a sheet that needs an answer.

### Dragging

A drag works from anywhere on the sheet, not only from the handle. A drag that
starts inside a scrolled list scrolls the list instead. It becomes a drag of
the sheet once the list is back at its top. A drag closes the sheet when it
goes far enough or ends in a fast flick. Otherwise the sheet moves back up.

Put `data-no-sheet-drag` on an element that has its own gesture, such as a
carousel, a map or a slider. A drag that starts there never moves the sheet:

```vue
<BottomSheet v-model:open="open">
  <ImageCarousel data-no-sheet-drag />
</BottomSheet>
```

### Buttons

The sheet has no `actions` prop. Put buttons in the default slot, inside the
scrolling content or below it, as the layout needs.

### Events

`update:open` fires as soon as the sheet starts to close. `after-leave` fires
when the close animation has finished. Use it to reset state that would
visibly change while the sheet is still sliding away.

## Accessibility

The sheet is a modal dialog. Focus stays inside it while it is open, and the
page behind it does not scroll. `title` is the sheet's accessible name. Without
a title, screen readers read "Bottom sheet".

## API reference

```ts
const propsData = [
  {
    name: 'open',
    description: 'Controls whether the sheet is open (`v-model:open`).',
    required: false,
    type: 'boolean'
  },
  {
    name: 'title',
    description: 'Optional centered title rendered in the drag-handle area.',
    required: false,
    type: 'string'
  },
  {
    name: 'dismissible',
    description: 'Allow outside-click, Escape, and swipe-down to close. Default `true`.',
    required: false,
    type: 'boolean',
    default: 'true'
  }
]

  const slotsData = [
  {
    name: 'default',
    description: '',
    type: '{}'
  }
]

  const emitsData = [
  {
    name: 'update:open',
    description: 'Fired when the open state changes via `v-model:open`.',
    type: '[value: boolean]'
  },
  {
    name: 'after-leave',
    description: 'Fired after the close animation finishes.',
    type: '[]'
  }
]
```
