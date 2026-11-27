<!-- Upstream: https://github.com/frappe/frappe-ui/blob/b435c66b51c0b19425a2a049b43e3ef2e308a8ab/src/components/LoadingText/LoadingText.md -->

# LoadingText

A [Spinner](spinner.md) with a label next to it, for inline loading states like
"Loading..." or "Saving...".

```vue
<script setup lang="ts">
import { LoadingText } from 'frappe-ui'
</script>

<template>
  <div class="w-80 rounded-6 border border-outline-gray-1 bg-surface-base">
    <div class="border-b border-outline-gray-1 px-4 py-3">
      <div class="text-base-medium text-ink-gray-8">Activity</div>
    </div>
    <div class="px-4 py-6">
      <LoadingText text="Loading activity..." />
    </div>
  </div>
</template>
```

## Examples

### Save status

Swap `LoadingText` with a plain status line while a form saves. Click
**Save** to see it.

```vue
<script setup lang="ts">
import { ref } from 'vue'
import { Button, LoadingText, TextInput } from 'frappe-ui'

const title = ref('Quarterly planning notes')
const saving = ref(false)

function save() {
  saving.value = true
  setTimeout(() => (saving.value = false), 1500)
}
</script>

<template>
  <div class="w-80 space-y-3">
    <TextInput v-model="title" label="Title" />
    <div class="flex items-center justify-between">
      <LoadingText v-if="saving" text="Saving..." />
      <span v-else class="text-base text-ink-gray-4">All changes saved</span>
      <Button label="Save" :disabled="saving" @click="save" />
    </div>
  </div>
</template>
```

## Behavior

### Text

`text` sets the label and defaults to `Loading...`. The text is 14px and uses
`text-ink-gray-4`, and the spinner beside it is 12px.

## Accessibility

The spinner has `role="status"` and the label "Loading". The text beside it is
plain text.

## API reference

```ts
const propsData = [
  {
    name: 'text',
    description: 'Text shown next to the spinner.',
    required: false,
    type: 'string',
    default: '"Loading..."'
  }
]
```
