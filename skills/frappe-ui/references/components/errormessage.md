<!-- Upstream: https://github.com/frappe/frappe-ui/blob/b435c66b51c0b19425a2a049b43e3ef2e308a8ab/src/components/ErrorMessage/ErrorMessage.md -->

# ErrorMessage

Shows one or more error messages in red text. It renders nothing when there is
no error.



## Examples

### Error from a server call

Pass the `Error` a failed call returns. Frappe's whitelisted methods put
several messages on it, and each one renders on its own line.

```vue
<script setup lang="ts">
import { ref } from 'vue'
import { Button, ErrorMessage } from 'frappe-ui'

const error = ref<Error | null>(null)

// Stands in for a failed call to a whitelisted method, which returns
// several messages on the error.
function save() {
  error.value = Object.assign(new Error('Validation failed'), {
    messages: [
      'Email <b>jane@example.com</b> is already in use.',
      'Phone number must include a country code.',
    ],
  })
}
</script>

<template>
  <div class="flex w-full max-w-sm flex-col gap-3">
    <ErrorMessage :message="error ?? undefined" />
    <div class="flex gap-2">
      <Button variant="solid" @click="save">Save contact</Button>
      <Button v-if="error" @click="error = null">Dismiss</Button>
    </div>
  </div>
</template>
```

### Form errors in one place

`message` takes an array of strings. Submit the form empty to see both lines.

```vue
<script setup lang="ts">
import { computed, ref } from 'vue'
import { Button, ErrorMessage, TextInput } from 'frappe-ui'

const email = ref('')
const password = ref('')
const submitted = ref(false)

const errors = computed(() => {
  if (!submitted.value) return []
  const list: string[] = []
  if (!email.value) list.push('Email is required.')
  if (password.value.length < 8)
    list.push('Password must be at least 8 characters.')
  return list
})
</script>

<template>
  <form
    class="flex w-full max-w-xs flex-col gap-3"
    @submit.prevent="submitted = true"
  >
    <TextInput
      v-model="email"
      type="email"
      placeholder="Email"
      aria-label="Email"
    />
    <TextInput
      v-model="password"
      type="password"
      placeholder="Password"
      aria-label="Password"
    />
    <ErrorMessage :message="errors" />
    <Button variant="solid" type="submit">Create account</Button>
  </form>
</template>
```

## Behavior

### Message values

`message` takes a string, an array of strings, or an `Error`. An `Error` with a
`messages` array renders those messages. Its own `message` is used only when
`messages` is empty. An empty string, an empty array, or an `Error` with
neither renders nothing.

The `error` prop on every input takes the same values.

### HTML in messages

Messages render as HTML, so markup such as `<b>` in a server message shows as
bold. The HTML is sanitized with DOMPurify first, which removes scripts and
event handlers.

## Accessibility

The message has `role="alert"`, so screen readers announce it when it appears.

## API reference

```ts
const propsData = [
  {
    name: 'message',
    description: 'The error message to display.',
    required: false,
    type: 'ErrorMessageValue'
  }
]
```
