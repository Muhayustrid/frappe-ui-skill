<!-- Upstream: https://github.com/frappe/frappe-ui/blob/b435c66b51c0b19425a2a049b43e3ef2e308a8ab/src/components/Alert/Alert.md -->

# Alert

A message inside the page that reports a status and offers a next step. For a
short message that goes away on its own, use [`toast`](toast.md) instead.



## Examples

### Confirmations

Plain messages with only a × button. `dismissible` shows the button, and the
parent removes the alert on `@dismiss`.

```vue
<script setup>
import { ref } from 'vue'
import { Alert } from 'frappe-ui'

// Plain confirmations with only a × button — `:icon="false"` hides the
// gray theme's default info icon, matching the design's neutral rows.
// The parent owns hiding — dismiss just flips a flag.
const messages = ref([
  { id: 1, title: 'Contacts added successfully' },
  { id: 2, title: 'Deal moved to Negotiation' },
])

function remove(id) {
  messages.value = messages.value.filter((m) => m.id !== id)
}
</script>

<template>
  <div class="flex w-full max-w-sm flex-col gap-2">
    <Alert
      v-for="message in messages"
      :key="message.id"
      :title="message.title"
      :icon="false"
      dismissible
      @dismiss="remove(message.id)"
    />
    <button
      v-if="!messages.length"
      class="self-start text-sm text-ink-gray-5 underline"
      @click="
        messages = [
          { id: 1, title: 'Contacts added successfully' },
          { id: 2, title: 'Deal moved to Negotiation' },
        ]
      "
    >
      Bring the messages back
    </button>
  </div>
</template>
```

### Status with an action

One-line status messages with one `primaryAction` each. `theme` colors the
icon and the action label.

```vue
<script setup>
import { ref } from 'vue'
import { Alert } from 'frappe-ui'

// One-line status rows. The theme colors the icon and the action label;
// the container stays neutral. Actions report in the status line below.
const status = ref('')

function open(target) {
  status.value = `${target} opened`
}
</script>

<template>
  <div class="flex w-full max-w-sm flex-col gap-2">
    <Alert
      title="SLA timer has started"
      theme="blue"
      :primary-action="{ label: 'View SLA', onClick: () => open('SLA details') }"
    />
    <Alert
      title="Failed to create lead"
      theme="red"
      :primary-action="{ label: 'Try again', onClick: () => open('Lead form') }"
    />
    <Alert
      title="Meeting scheduled"
      theme="green"
      :primary-action="{ label: 'Open calendar', onClick: () => open('Calendar') }"
    />
    <Alert
      title="Storage is almost full"
      theme="amber"
      :primary-action="{ label: 'Manage', onClick: () => open('Storage settings') }"
    />
    <p v-if="status" class="text-sm text-ink-gray-5">{{ status }}</p>
  </div>
</template>
```

### Warnings that need a decision

A `description` or a `secondaryAction` switches the alert to the banner
layout. The "Dismiss" action calls `dismiss()` from its `onClick` context.

```vue
<script setup>
import { ref } from 'vue'
import { Alert } from 'frappe-ui'

// Banners stack a description and two actions. A "Dismiss" secondary action
// calls context.dismiss(); the parent hides the alert with v-if. Primary
// actions report what they did in the status line below the stack.
const showSla = ref(true)
const showSync = ref(true)
const showDuplicate = ref(true)
const status = ref('')

function reset() {
  showSla.value = true
  showSync.value = true
  showDuplicate.value = true
  status.value = ''
}
</script>

<template>
  <div class="flex w-full max-w-sm flex-col gap-3">
    <Alert
      v-if="showSla"
      title="SLA due soon for #58281"
      description="The SLA for #58281 will breach in 1 hour"
      theme="amber"
      :primary-action="{
        label: 'Respond now',
        onClick: () => (status = 'Ticket #58281 opened'),
      }"
      :secondary-action="{ label: 'Dismiss', onClick: ({ dismiss }) => dismiss() }"
      @dismiss="showSla = false"
    />
    <Alert
      v-if="showSync"
      title="Sync completed with issues"
      description="96 of 100 contacts were synced."
      theme="amber"
      :primary-action="{
        label: 'Review',
        onClick: () => (status = 'Sync report opened'),
      }"
      :secondary-action="{ label: 'Dismiss', onClick: ({ dismiss }) => dismiss() }"
      @dismiss="showSync = false"
    />
    <Alert
      v-if="showDuplicate"
      title="Duplicate lead detected"
      description="A similar lead already exists."
      theme="amber"
      :primary-action="{
        label: 'Review duplicate',
        onClick: () => (status = 'Duplicate lead opened'),
      }"
      :secondary-action="{
        label: 'Continue anyway',
        onClick: ({ dismiss }) => ((status = 'Lead created'), dismiss()),
      }"
      @dismiss="showDuplicate = false"
    />
    <p v-if="status" class="text-sm text-ink-gray-5">{{ status }}</p>
    <button
      v-if="!showSla || !showSync || !showDuplicate"
      class="self-start text-sm text-ink-gray-5 underline"
      @click="reset()"
    >
      Bring the banners back
    </button>
  </div>
</template>
```

### Trial ending

A banner with one action and a × button in the corner.

```vue
<script setup>
import { ref } from 'vue'
import { Alert } from 'frappe-ui'

// An info banner with one action and a × button in the corner.
const showTrialBanner = ref(true)
const status = ref('')

function openBilling() {
  status.value = 'Billing page opened'
}
</script>

<template>
  <div class="flex w-full max-w-sm flex-col gap-2">
    <Alert
      v-if="showTrialBanner"
      title="Your trial ends soon!"
      description="Upgrade to keep enjoying features and future technical support."
      theme="blue"
      dismissible
      :primary-action="{ label: 'Update now', onClick: openBilling }"
      @dismiss="showTrialBanner = false"
    />
    <button
      v-else
      class="self-start text-sm text-ink-gray-5 underline"
      @click="showTrialBanner = true"
    >
      Bring the banner back
    </button>
    <p v-if="status" class="text-sm text-ink-gray-5">{{ status }}</p>
  </div>
</template>
```

### Import in progress

`#prefix` replaces the status icon with a spinner, and `#description` holds
formatted text.

```vue
<script setup>
import { ref } from 'vue'
import { Alert, Spinner } from 'frappe-ui'

// Slot overrides: #prefix swaps the status icon for a Spinner, and
// #description carries rich text.
const showImportBanner = ref(true)
const status = ref('')

function viewProgress() {
  status.value = 'Import log opened'
}
</script>

<template>
  <div class="flex w-full max-w-sm flex-col gap-2">
    <Alert
      v-if="showImportBanner"
      title="Contact import is in progress"
      theme="blue"
      :primary-action="{ label: 'View progress', onClick: viewProgress }"
      :secondary-action="{ label: 'Dismiss', onClick: ({ dismiss }) => dismiss() }"
      @dismiss="showImportBanner = false"
    >
      <template #prefix>
        <Spinner size="md" class="text-ink-blue-5" />
      </template>
      <template #description>
        Importing <span class="font-medium text-ink-gray-7">contacts.csv</span> —
        large imports may take a few minutes.
      </template>
    </Alert>
    <button
      v-else
      class="self-start text-sm text-ink-gray-5 underline"
      @click="showImportBanner = true"
    >
      Bring the banner back
    </button>
    <p v-if="status" class="text-sm text-ink-gray-5">{{ status }}</p>
  </div>
</template>
```

## Behavior

### Layout

The alert has no layout prop. With only a title, it is a single row. A
`description`, a `#description` slot or a `secondaryAction` switches it to the
banner layout, with the description and buttons below the title. The
container looks the same for every `theme`. Only the icon and the row action
label take the theme's color.

### Icon

`icon` has three states. Unset or `true` shows the theme's status icon. The
gray theme shows the info icon in black. `false` hides the icon. A `lucide-*`
class name or a Vue component shows that icon instead, in the theme's color.

```vue
<!-- the theme's icon -->
<Alert title="Saved" theme="green" />
<!-- the same icon, written out -->
<Alert title="Saved" theme="green" :icon="true" />
<!-- no icon -->
<Alert title="Saved" :icon="false" />
<!-- your own icon -->
<Alert title="Saved" icon="lucide-rocket" />
```

### Actions

`primaryAction` and `secondaryAction` take `Button` props plus an `onClick`
that receives `{ dismiss }`. While an `onClick` that returns a promise is
running, its button shows a spinner and ignores more clicks. A `loading` value
you pass wins over this. A `secondaryAction` without a `primaryAction` logs a
warning in development.

The `#actions` slot replaces both buttons and receives `{ dismiss }`.

### Dismissing

The alert never hides itself. The × button and `dismiss()` only emit
`dismiss`. The parent hides the alert, usually with `v-if`.

## Accessibility

A `red` or `amber` alert has the `alert` role, so screen readers read it out
at once. Other themes have the `status` role, which screen readers read out
when they are idle. The × button is labelled "Dismiss".

## API reference

```ts
const propsData = [
  {
    name: 'title',
    description: 'Main heading text of the alert. Optional when the `#title` slot is used',
    required: false,
    type: 'string'
  },
  {
    name: 'description',
    description: 'Supporting text below the title; its presence switches the alert to the banner layout',
    required: false,
    type: 'string'
  },
  {
    name: 'theme',
    description: 'Color theme of the status icon and the row action label; the container never changes with theme',
    required: false,
    type: 'StatusTheme',
    default: '\'gray\''
  },
  {
    name: 'icon',
    description: 'Status icon: unset or `true` shows the theme\'s auto icon (gray shows the info glyph in black ink), `false` hides it, a `lucide-*` string or Component renders a custom theme-colored glyph',
    required: false,
    type: 'boolean | string | Component'
  },
  {
    name: 'primaryAction',
    description: 'Primary action button (`ButtonProps` plus `onClick({ dismiss })`)',
    required: false,
    type: 'AlertAction'
  },
  {
    name: 'secondaryAction',
    description: 'Second action button; its presence forces the banner layout',
    required: false,
    type: 'AlertAction'
  },
  {
    name: 'dismissible',
    description: 'Shows the dismiss (×) button, which emits `dismiss`',
    required: false,
    type: 'boolean',
    default: 'false'
  }
]

  const slotsData = [
  {
    name: 'prefix',
    description: 'Overrides the status icon area',
    type: 'any'
  },
  {
    name: 'title',
    description: 'Rich title content (overrides the `title` prop)',
    type: 'any'
  },
  {
    name: 'description',
    description: 'Rich description content; its presence forces the banner layout',
    type: 'any'
  },
  {
    name: 'actions',
    description: 'Replaces the auto-rendered action buttons; receives `{ dismiss }`',
    type: 'AlertActionsSlotProps'
  }
]

  const emitsData = [
  {
    name: 'dismiss',
    description: 'Fired when the user dismisses the alert — the × button or an action\'s `context.dismiss()`. The parent owns hiding.',
    type: '[]'
  }
]
```
