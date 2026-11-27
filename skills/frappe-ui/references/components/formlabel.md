<!-- Upstream: https://github.com/frappe/frappe-ui/blob/b435c66b51c0b19425a2a049b43e3ef2e308a8ab/src/components/FormLabel/FormLabel.md -->

# FormLabel

The label above a form field: the label text and an optional required marker.
Inputs in the library draw the same label from their own `label` prop, so use
`FormLabel` only for a control you build yourself.



## Examples

### Label for a custom control

`id` links the label to a native color input, so clicking the label opens the
color picker.

```vue
<script setup lang="ts">
import { ref } from 'vue'
import { FormLabel } from 'frappe-ui'

const color = ref('#2563eb')
</script>

<template>
  <div class="flex flex-col gap-1.5">
    <FormLabel id="brand-color" label="Brand color" required />
    <div class="flex items-center gap-2">
      <input
        id="brand-color"
        v-model="color"
        type="color"
        required
        class="h-7 w-10 cursor-pointer rounded-4 border border-outline-gray-2 bg-surface-base p-0.5"
      />
      <span class="text-base text-ink-gray-7 tabular-nums">{{ color }}</span>
    </div>
  </div>
</template>
```

## Behavior

### Linking to a control

`id` is the id of the control, not of the label. It sets the label's `for`
attribute. Give the control the same `id`.

### Required marker

`required` adds a red asterisk after the text. It does not set `required` on
the control, so set that yourself.

## Accessibility

The asterisk is hidden from screen readers. The label includes hidden
"(required)" text instead, so the control's name is announced as, for example,
"Brand color (required)".

## API reference

```ts
const propsData = [
  {
    name: 'label',
    description: 'Text content of the label.',
    required: true,
    type: 'string'
  },
  {
    name: 'id',
    description: 'id of the form control this label is for; wired to the `for` attribute.',
    required: false,
    type: 'string'
  },
  {
    name: 'required',
    description: 'Marks the field as required; renders an asterisk and an accessible "(required)" note.',
    required: false,
    type: 'boolean'
  }
]
```
