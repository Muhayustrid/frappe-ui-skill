<!-- Upstream: https://github.com/frappe/frappe-ui/blob/b435c66b51c0b19425a2a049b43e3ef2e308a8ab/src/components/TextInput/TextInput.md -->

# TextInput

A single-line field for text, numbers, email addresses and other short values.
For several lines of text, use [Textarea](textarea.md), and for a password, use
[Password](password.md).



## Examples

### Search box

The `#prefix` slot puts an icon inside the field, before the text.

```vue
<script setup lang="ts">
import { ref } from 'vue'
import { TextInput } from 'frappe-ui'

const query = ref('')
</script>

<template>
  <TextInput
    v-model="query"
    type="search"
    placeholder="Search tasks"
    aria-label="Search tasks"
    class="w-full max-w-xs"
  >
    <template #prefix>
      <span class="lucide-search size-4 text-ink-gray-5" aria-hidden="true" />
    </template>
  </TextInput>
</template>
```

### Price field

`type="number"` with the `.number` modifier, so the model holds a number. The
`#suffix` slot shows the currency after the value.

```vue
<script setup lang="ts">
import { ref } from 'vue'
import { TextInput } from 'frappe-ui'

const price = ref<number | ''>(49)
</script>

<template>
  <div class="flex w-full max-w-xs flex-col gap-3">
    <TextInput
      v-model.number="price"
      type="number"
      label="Monthly price"
      placeholder="0"
    >
      <template #suffix>
        <span class="text-sm text-ink-gray-5">USD</span>
      </template>
    </TextInput>
    <p class="text-p-sm text-ink-gray-5">
      Yearly: {{ price === '' ? '—' : `${price * 12} USD` }}
    </p>
  </div>
</template>
```

### Sign-up email

`label`, `description`, `required` and `error` on one field. The error is set
from a `@blur` listener, which lands on the `<input>`.

```vue
<script setup lang="ts">
import { ref } from 'vue'
import { TextInput } from 'frappe-ui'

const email = ref('')
const error = ref('')

function validate() {
  if (!email.value) error.value = 'Enter your work email.'
  else if (!/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email.value))
    error.value = 'This does not look like an email address.'
  else error.value = ''
}
</script>

<template>
  <TextInput
    v-model="email"
    type="email"
    label="Work email"
    description="We send a confirmation link to this address."
    placeholder="jane@example.com"
    name="email"
    required
    :error="error"
    class="w-full max-w-xs"
    @blur="validate"
  />
</template>
```

### Custom label

The `#label` slot replaces the label text and the required marker. It receives
`{ required }`. The `#description` slot replaces the description text.

```vue
<script setup lang="ts">
import { TextInput } from 'frappe-ui'
</script>

<template>
  <TextInput placeholder="acme" required>
    <template #label="{ required }">
      <span class="font-semibold">Workspace name</span>
      <span v-if="required" class="text-ink-red-5">*</span>
    </template>
    <template #description>
      <span class="italic">Lowercase letters and dashes only.</span>
    </template>
  </TextInput>
</template>
```

## Behavior

### Label, description and error

`label` renders above the field and `description` below it. `error` renders
below the field and hides `description`. It takes a string, an array of strings
(one line each), or an `Error`, the same values as
[ErrorMessage](errormessage.md). An empty string or an empty array means no
error. `required` adds a red asterisk to the label and sets `required` on the
`<input>`.

A `#description` slot is not hidden by `error`. It renders above the error.

### Attributes

`class` and `style` go on the outer element: the wrapper when a label,
description or error shows, and the field box otherwise. Every other attribute
and listener goes on the `<input>`, including `name`, `autocomplete`, `aria-*`
and `@blur`. The `<input>` has `autocomplete="off"` unless you pass your own
`autocomplete`.

### Numbers come back as strings

`modelValue` accepts `string | number`, and the component always emits a
`string`. That is what the browser gives it: `input.value` is a string whatever
the `type` is. Add Vue's `.number` modifier when you want a number back.

```vue
<!-- price is a string: "42" -->
<TextInput type="number" v-model="price" />

<!-- price is a number: 42 -->
<TextInput type="number" v-model.number="price" />
```

An empty field with `.number` gives `''`, not `0`, so check for it before you
do arithmetic.

### Dates and times

`type="date"`, `"time"` and `"datetime-local"` show the browser's own picker.
For the library's pickers, use [DatePicker](datepicker.md) and
[TimePicker](timepicker.md).

### Debounce

`debounce` delays `update:modelValue` by that many milliseconds after the last
keystroke. Use it when each change starts a request, such as a search.

## Accessibility

- The `<label>` is linked to the `<input>`, so screen readers announce it as
  the field's name. Pass `aria-label` when there is no visible label, as in the
  search box above.
- The description and the error are linked with `aria-describedby`.
- With `error`, the `<input>` gets `aria-invalid="true"` and
  `aria-errormessage`.
- With `required`, the `<input>` gets `aria-required="true"`, and the label
  includes hidden "(required)" text. The asterisk itself is hidden from screen
  readers.

## API reference

```ts
const propsData = [
  {
    name: 'type',
    description: 'HTML input type (text, email, number, password, etc.).',
    required: false,
    type: 'TextInputTypes',
    default: '"text"'
  },
  {
    name: 'size',
    description: 'Visual size of the input.',
    required: false,
    type: 'InputSize',
    default: '"sm"'
  },
  {
    name: 'variant',
    description: 'Style variant of the input.',
    required: false,
    type: 'InputVariant',
    default: '"subtle"'
  },
  {
    name: 'placeholder',
    description: 'Placeholder text shown when the input is empty.',
    required: false,
    type: 'string'
  },
  {
    name: 'disabled',
    description: 'Disables the input when true.',
    required: false,
    type: 'boolean'
  },
  {
    name: 'modelValue',
    description: 'Bound value of the input.',
    required: false,
    type: 'string | number'
  },
  {
    name: 'debounce',
    description: 'Debounce delay (in ms) before emitting value updates.',
    required: false,
    type: 'number'
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
    name: 'prefix',
    description: 'Content rendered before the input (left side)',
    type: 'any'
  },
  {
    name: 'suffix',
    description: 'Content rendered after the input (right side)',
    type: 'any'
  },
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
    description: 'Fired when the input value changes.',
    type: '[value: string]'
  }
]

  const exposedData = [
  {
    name: 'inputElement',
    description: 'The native element the user types into. `null` before mount.',
    type: 'HTMLInputElement | null'
  },
  {
    name: 'focus',
    description: 'Moves focus to the input.',
    type: '(options?: FocusOptions) => void'
  }
]
```
