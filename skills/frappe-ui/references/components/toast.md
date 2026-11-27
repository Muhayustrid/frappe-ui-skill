<!-- Upstream: https://github.com/frappe/frappe-ui/blob/b435c66b51c0b19425a2a049b43e3ef2e308a8ab/src/components/Toast/Toast.md -->

# Toast

A short message in the bottom-right corner that reports the result of an
action and closes on its own. For a message that stays in the page, use
[`Alert`](alert.md).

```vue
<script setup lang="ts">
import { Button, toast } from 'frappe-ui'
</script>

<template>
  <Button label="Show toast" @click="toast.success('Workspace created')" />
</template>
```

## Examples

### Results of an action

`toast.success`, `toast.error`, `toast.warning` and `toast.info` each show
their own icon, and `toast.message` shows none. `description` adds a second
line.

```vue
<script setup lang="ts">
import { Button, toast } from 'frappe-ui'
</script>

<template>
  <div class="grid grid-cols-2 gap-2 sm:grid-cols-3">
    <Button
      label="Workspace created"
      @click="toast.success('Workspace created')"
    />
    <Button
      label="Link copied"
      @click="toast.info('Link copied to clipboard')"
    />
    <Button
      label="Comment posted"
      @click="
        toast.message('Comment posted', {
          description: '“Looks good — shipping it!”',
        })
      "
    />
    <Button
      label="Profile updated"
      @click="
        toast.success('Profile updated', {
          description: 'Your changes have been saved.',
        })
      "
    />
    <Button
      label="Payment failed"
      @click="
        toast.error('Payment failed', {
          description: 'Your card was declined. Try a different one.',
        })
      "
    />
    <Button
      label="Storage almost full"
      @click="
        toast.warning('Storage almost full', {
          description:
            'You have used 9.2 GB of 10 GB. Upgrade to keep syncing.',
        })
      "
    />
  </div>
</template>
```

### Undo and refresh

`action` adds a button to the toast. With `duration: Infinity`, the toast waits
until the person clicks it or closes the toast.

```vue
<script setup lang="ts">
import { Button, toast } from 'frappe-ui'

function postArchived() {
  toast.success('Post archived', {
    action: {
      label: 'Undo',
      onClick: () => toast.info('Archive undone'),
    },
  })
}

function newVersion() {
  toast.info('A new version is available', {
    description: 'Refresh to get the latest features.',
    duration: Infinity,
    action: {
      label: 'Refresh',
      onClick: () => toast.loading('Refreshing…'),
    },
  })
}

function backgroundSync() {
  toast.info('Background sync in progress', {
    duration: Infinity,
    action: {
      label: 'Cancel',
      onClick: () => toast.dismiss(),
    },
  })
}
</script>

<template>
  <Button label="Post archived" @click="postArchived" />
  <Button label="New version" @click="newVersion" />
  <Button label="Background sync" @click="backgroundSync" />
</template>
```

### Reminders and favorites

`icon` replaces the type's icon with your own.

```vue
<script setup lang="ts">
import { h } from 'vue'
import { Button, toast } from 'frappe-ui'

function celebrate() {
  toast.success('You unlocked a new badge', {
    icon: () => h('span', { class: 'lucide-sparkles size-4' }),
  })
}

function favorited() {
  toast('Added to favorites', {
    description: 'Find it later in your saved items.',
    icon: () => h('span', { class: 'lucide-heart size-4 text-ink-red-4' }),
  })
}

function reminder() {
  toast.message('Standup in 5 minutes', {
    icon: () => h('span', { class: 'lucide-bell size-4' }),
  })
}
</script>

<template>
  <Button label="Celebrate" @click="celebrate" />
  <Button label="Favorited" @click="favorited" />
  <Button label="Reminder" @click="reminder" />
</template>
```

### Sending, deleting and deploying

`toast.promise` shows one toast that changes from loading to success or error
when the promise settles. The deploy button updates a single toast through each
step by passing its `id` back to `toast.loading` and `toast.success`.

```vue
<script setup lang="ts">
import { Button, toast } from 'frappe-ui'

function sendInvite() {
  toast.promise(new Promise<void>((resolve) => setTimeout(resolve, 1500)), {
    loading: 'Sending invite to alex@example.com…',
    success: 'Invite sent to alex@example.com',
    error: 'Could not send invite',
  })
}

function deleteFile() {
  const file = { id: 'f_42', name: 'report.pdf' }
  const willFail = Math.random() < 0.5

  toast.promise(
    new Promise<{ id: string; name: string }>((resolve, reject) =>
      setTimeout(
        () => (willFail ? reject(new Error('Network error')) : resolve(file)),
        1500,
      ),
    ),
    {
      loading: `Deleting ${file.name}…`,
      success: (deleted: { id: string; name: string }) => ({
        message: `Deleted ${deleted.name}`,
        action: {
          label: 'Undo',
          onClick: () => toast.success(`Restored ${deleted.name}`),
        },
      }),
      error: (err: Error) => ({
        message: `Couldn't delete ${file.name} — ${err.message}`,
        action: {
          label: 'Retry',
          onClick: () => deleteFile(),
        },
      }),
    },
  )
}

function deployPipeline() {
  const steps = [
    { label: 'Linting…', delay: 0 },
    { label: 'Type-checking…', delay: 600 },
    { label: 'Running tests…', delay: 1200 },
    { label: 'Building…', delay: 1900 },
    { label: 'Deploying to production…', delay: 2700 },
  ]
  const id = toast.loading(steps[0].label)
  steps.slice(1).forEach((step) => {
    setTimeout(() => toast.loading(step.label, { id }), step.delay)
  })
  setTimeout(
    () =>
      toast.success('Deployed to production', {
        id,
        description: 'v2.4.1 is live • took 3.6s',
      }),
    3500,
  )
}
</script>

<template>
  <Button label="Send invite" @click="sendInvite" />
  <Button label="Delete file" @click="deleteFile" />
  <Button label="Deploy pipeline" @click="deployPipeline" />
</template>
```

## Behavior

### Setup

The `toast.*` functions need `<ToastProvider />` mounted in the app.
[`FrappeUIProvider`](frappeuiprovider.md) already mounts it. `ToastProvider`
takes no props: the position, the look and the default duration are the same
in every app. Toasts sit at the bottom right, or at the bottom left on a
`dir="rtl"` page. Up to three toasts show at a time, and each one has a close
button.

### Functions

Every function takes a message and an optional options object, and returns the
toast's id.

| Function        | Shows                                          |
| --------------- | ---------------------------------------------- |
| `toast()`       | a message with no icon                         |
| `toast.message` | the same as `toast()`                          |
| `toast.success` | a success icon                                 |
| `toast.error`   | a red error icon                               |
| `toast.warning` | an amber warning icon                          |
| `toast.info`    | an info icon                                   |
| `toast.loading` | a spinner; the toast stays until you update it |

`toast.dismiss(id)` closes one toast. `toast.dismiss()` with no id closes all
of them.

### Duration

A toast closes after 4000ms. Pass `duration` to change it for one toast, or
`duration: Infinity` to keep it until the person closes it.

```js
toast.success('Saved')
toast.error('Could not save', { duration: 10000 })
toast.info('Uploading…', { duration: Infinity })
```

### Updating a toast

Pass the id a call returned as `id` in a later call. The toast with that id
changes in place instead of a new toast showing.

```js
const id = toast.loading('Uploading…')
toast.success('Uploaded', { id })
```

### Promises

`toast.promise(promise, { loading, success, error })` shows the `loading`
message, then `success` or `error` when the promise settles. `success` and
`error` take a string, or a function that receives the resolved value or the
error. The function returns a string, or an object with `message` and any other
option such as `action`, `description` or `duration`. Use this for an **Undo**
after a success or a **Retry** after a failure.

### Actions

`action` takes `{ label, onClick }` and shows a button in the toast. `cancel`
takes the same shape.

### Custom icon

`icon` takes a render function. To use a Lucide icon, render a `lucide-*`
class and set its size and color with utility classes:

```js
import { h } from 'vue'

toast('Added to favorites', {
  icon: () => h('span', { class: 'lucide-heart size-4 text-ink-red-4' }),
})
```

### HTML in messages

A string message and a string `description` can hold inline HTML. Only `a`,
`em`, `strong`, `i`, `b` and `u` tags are kept. Other tags are removed, so
write `&lt;` for a literal `<`. A component, VNode or render function renders
as it is.

### TypeScript types

`ToastOptions` is the options object every `toast.*` call takes. `ToastAction`
is its `action` or `cancel` button. `ToastId` is what a call returns.

```ts
import { toast, type ToastId, type ToastOptions } from 'frappe-ui'

function notifySaved(options?: ToastOptions): ToastId {
  return toast.success('Saved', options)
}
```
