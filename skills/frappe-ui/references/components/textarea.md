<!-- Upstream: https://github.com/frappe/frappe-ui/blob/b435c66b51c0b19425a2a049b43e3ef2e308a8ab/src/components/Textarea/Textarea.md -->

# Textarea

A multi-line field for longer text, such as comments, notes and descriptions.
For a single line, use [TextInput](textinput.md).



## Examples

### Comment box

An `outline` textarea with no label, and a button that stays disabled until
there is text. `aria-label` names the field for screen readers.

```vue
<script setup lang="ts">
import { ref } from 'vue'
import { Button, Textarea } from 'frappe-ui'

const comment = ref('')
const comments = ref<string[]>([])

function post() {
  comments.value.push(comment.value.trim())
  comment.value = ''
}
</script>

<template>
  <div class="flex w-full max-w-md flex-col gap-3">
    <p
      v-for="(text, i) in comments"
      :key="i"
      class="rounded-4 bg-surface-gray-2 px-3 py-2 text-p-sm text-ink-gray-8"
    >
      {{ text }}
    </p>
    <Textarea
      v-model="comment"
      variant="outline"
      :rows="3"
      placeholder="Add a comment"
      aria-label="Comment"
    />
    <Button
      variant="solid"
      class="self-end"
      :disabled="!comment.trim()"
      @click="post"
    >
      Comment
    </Button>
  </div>
</template>
```

### Bio with a character limit

`description` counts the characters left. Past the limit, `error` takes its
place.

```vue
<script setup lang="ts">
import { computed, ref } from 'vue'
import { Textarea } from 'frappe-ui'

const limit = 160
const bio = ref('Designer at a small studio. I write about type and layout.')

const left = computed(() => limit - bio.value.length)
const error = computed(() =>
  left.value < 0 ? `Shorten your bio by ${-left.value} characters.` : '',
)
</script>

<template>
  <Textarea
    v-model="bio"
    label="Bio"
    :description="`${left} characters left`"
    :error="error"
    :rows="4"
    class="w-full max-w-md"
  />
</template>
```

## Behavior

### Label, description and error

`label` renders above the field and `description` below it. `error` renders
below the field and hides `description`. It takes a string, an array of strings
(one line each), or an `Error`, the same values as
[ErrorMessage](errormessage.md). An empty string or an empty array means no
error. `required` adds a red asterisk to the label and sets `required` on the
`<textarea>`.

The `#label` slot replaces the label text and the required marker, and receives
`{ required }`. A `#description` slot is not hidden by `error`. It renders
above the error.

### Attributes

`class` and `style` go on the outer element: the wrapper when a label,
description or error shows, and the `<textarea>` otherwise. Every other
attribute and listener goes on the `<textarea>`.

### Rows and size

`rows` sets the visible height in lines. It defaults to `3`. The textarea does
not grow as the text gets longer.

`size` changes the padding, the corner radius and the minimum height. The text
is 13px at every size.

### Debounce

`debounce` delays `update:modelValue` by that many milliseconds after the last
keystroke.

## Accessibility

- The `<label>` is linked to the `<textarea>`, so screen readers announce it as
  the field's name. Pass `aria-label` when there is no visible label.
- The description and the error are linked with `aria-describedby`.
- With `error`, the `<textarea>` gets `aria-invalid="true"` and
  `aria-errormessage`.
- With `required`, the `<textarea>` gets `aria-required="true"`, and the label
  includes hidden "(required)" text. The asterisk itself is hidden from screen
  readers.

## API reference

```ts
const propsData = [
  {
    name: 'size',
    description: 'Controls spacing, corner radius and the minimum height of the textarea.\nTextarea text is a fixed 13px at every size, so `size` does not change\nthe type scale the way it does on a single-line input.',
    required: false,
    type: 'InputSize',
    default: '"sm"'
  },
  {
    name: 'variant',
    description: 'Visual style variant.',
    required: false,
    type: 'InputVariant',
    default: '"subtle"'
  },
  {
    name: 'placeholder',
    description: 'Placeholder text shown when empty.',
    required: false,
    type: 'string'
  },
  {
    name: 'disabled',
    description: 'Disables user interaction.',
    required: false,
    type: 'boolean'
  },
  {
    name: 'modelValue',
    description: 'Bound value of the textarea.',
    required: false,
    type: 'string'
  },
  {
    name: 'debounce',
    description: 'Debounce delay (ms) before emitting value updates.',
    required: false,
    type: 'number'
  },
  {
    name: 'rows',
    description: 'Number of visible text rows.',
    required: false,
    type: 'number',
    default: '3'
  },
  {
    name: 'label',
    description: 'Label rendered above (or beside, for binary controls) the input.',
    required: false,
    type: 'string'
  },
  {
    name: 'description',
    description: 'Helper text rendered below the input.\nHidden when `error` is set. A `#description` slot is not: it renders\nbeside the error, and is referenced alongside it.',
    required: false,
    type: 'string'
  },
  {
    name: 'error',
    description: 'Error message rendered below the input. When set, the control receives\n`aria-invalid="true"` and `data-state="invalid"`. Takes a string, an\narray of strings, or an `Error` whose `messages` are rendered as stacked\nlines (with `Error.message` as the fallback). This is the same value\n`ErrorMessage.message` takes. An empty array and an empty string both\nmean no error.',
    required: false,
    type: 'ErrorMessageValue'
  },
  {
    name: 'required',
    description: 'Marks the field as required. Renders an asterisk next to the label, with\n`sr-only` text that announces it, and forwards `required` /\n`aria-required` to the underlying control where the control\'s role allows\nit. `data-required` is set either way.',
    required: false,
    type: 'boolean'
  },
  {
    name: 'id',
    description: 'HTML id of the underlying control. Auto-generated via `useId()` if omitted.',
    required: false,
    type: 'string'
  }
]

  const slotsData = [
  {
    name: 'label',
    description: 'Overrides the rendered label content. Receives `{ required }`.',
    type: '{ required: boolean; }'
  },
  {
    name: 'description',
    description: 'Overrides the rendered description content.',
    type: 'any'
  }
]

  const emitsData = [
  {
    name: 'update:modelValue',
    description: 'Fired when the textarea value changes.',
    type: '[value: string]'
  }
]

  const exposedData = [
  {
    name: 'inputElement',
    description: 'The native element the user types into. `null` before mount.',
    type: 'HTMLTextAreaElement | null'
  },
  {
    name: 'focus',
    description: 'Moves focus to the textarea.',
    type: '(options?: FocusOptions) => void'
  }
]
```
