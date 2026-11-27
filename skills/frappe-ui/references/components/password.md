<!-- Upstream: https://github.com/frappe/frappe-ui/blob/b435c66b51c0b19425a2a049b43e3ef2e308a8ab/src/components/Password/Password.md -->

# Password

A text field for passwords. An eye icon inside the field shows or hides what
was typed.



## Examples

### Sign-in form

`Password` next to an email [TextInput](textinput.md). The `#prefix` slot puts
a lock icon before the text, and `autocomplete="current-password"` lets the
browser fill in a saved password.

```vue
<script setup lang="ts">
import { ref } from 'vue'
import { Button, Password, TextInput } from 'frappe-ui'

const email = ref('')
const password = ref('')
</script>

<template>
  <form class="flex w-full max-w-xs flex-col gap-4" @submit.prevent>
    <TextInput
      v-model="email"
      type="email"
      label="Email"
      placeholder="jane@example.com"
      autocomplete="email"
    />
    <Password
      v-model="password"
      label="Password"
      autocomplete="current-password"
    >
      <template #prefix>
        <span class="lucide-lock size-4 text-ink-gray-5" aria-hidden="true" />
      </template>
    </Password>
    <Button variant="solid" type="submit">Sign in</Button>
  </form>
</template>
```

### New password

`required`, a `description` with the rule, and an `error` set from a `@blur`
listener. `autocomplete="new-password"` lets the browser suggest a strong
password.

```vue
<script setup lang="ts">
import { ref } from 'vue'
import { Password } from 'frappe-ui'

const password = ref('')
const error = ref('')

function validate() {
  error.value = password.value.length < 8 ? 'Use at least 8 characters.' : ''
}
</script>

<template>
  <Password
    v-model="password"
    label="New password"
    description="At least 8 characters."
    autocomplete="new-password"
    required
    :error="error"
    class="w-full max-w-xs"
    @blur="validate"
  />
</template>
```

## Behavior

### Show and hide

The eye icon toggles the field between hidden and plain text. `Cmd+I` on macOS
and `Ctrl+I` elsewhere do the same while the field has focus. A tooltip on the
icon names the action and the shortcut.

The icon is hidden while the value contains `*`, such as a masked
`********` value from the server.

### Slots

The eye icon uses the space of the `#suffix` slot, so `Password` has no
`#suffix`. It has `#prefix`, `#label` and `#description`, which work as they do
on [TextInput](textinput.md).

### Label, description and error

`label` renders above the field and `description` below it. `error` renders
below the field and hides `description`. It takes a string, an array of strings
(one line each), or an `Error`, the same values as
[ErrorMessage](errormessage.md). An empty string or an empty array means no
error. `required` adds a red asterisk to the label and sets `required` on the
`<input>`.

The `#label` slot replaces the label text and the required marker, and receives
`{ required }`. A `#description` slot is not hidden by `error`. It renders
above the error.

### Attributes

`class` and `style` go on the outer element: the wrapper when a label,
description or error shows, and the field box otherwise. Every other attribute
and listener goes on the `<input>`, including `name`, `autocomplete` and
`@blur`.

## Accessibility

| Keys               | Action                    |
| ------------------ | ------------------------- |
| `Cmd+I` / `Ctrl+I` | Show or hide the password |

- The eye icon is not a button and cannot be reached with `Tab`. Keyboard
  users toggle with the shortcut above.
- The `<label>` is linked to the `<input>`, so screen readers announce it as
  the field's name.
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
  },
  {
    name: 'modelValue',
    description: 'The current password value (controlled).',
    required: false,
    type: 'string'
  }
]

  const slotsData = [
  {
    name: 'prefix',
    description: 'Content shown before the input field (left icon / custom content)',
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
    description: 'Fired when the model value changes.',
    type: '[value: string | undefined]'
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
    description: 'Moves focus to the password input.',
    type: '(options?: FocusOptions) => void'
  }
]
```
