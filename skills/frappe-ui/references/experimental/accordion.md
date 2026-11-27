<!-- Upstream: https://github.com/frappe/frappe-ui/blob/b435c66b51c0b19425a2a049b43e3ef2e308a8ab/experimental/Accordion/Accordion.md -->

# Accordion

Stacks sections of content behind labelled headers that expand and collapse.
Useful for FAQs, settings groups, and anywhere vertical space is at a premium.

> **Experimental** — `Accordion` ships from
> [`frappe-ui/experimental`](../experimental.md) while its API settles, so it is
> exempt from the usual deprecation policy and can change shape or disappear in
> any release.

```ts
import { Accordion } from 'frappe-ui/experimental'
import type { AccordionItem, AccordionProps } from 'frappe-ui/experimental'
```

Each item needs a `value` — it is the item's identity and the key `modelValue` /
`defaultValue` refer to, so it must stay stable as `items` is reordered or
filtered.

## Default

```vue
<script setup lang="ts">
import { Accordion } from '..'

const items = [
  {
    value: 'shipping',
    title: 'How long does shipping take?',
    content:
      'Orders are processed within 1–2 business days and typically arrive within 5–7 business days.',
  },
  {
    value: 'returns',
    title: 'What is your return policy?',
    content:
      'You can return any unused item within 30 days of delivery for a full refund.',
  },
  {
    value: 'support',
    title: 'How do I contact support?',
    content:
      'Reach out to our team any time at support@example.com and we will get back to you within a day.',
  },
]
</script>

<template>
  <div class="w-full max-w-lg">
    <Accordion :items="items" default-value="shipping" />
  </div>
</template>
```

## Multiple

```vue
<script setup lang="ts">
import { ref } from 'vue'
import { Accordion } from '..'

const open = ref<string[]>(['general', 'billing'])

const items = [
  {
    value: 'general',
    title: 'General',
    content: 'Account-wide preferences such as language, timezone, and theme.',
  },
  {
    value: 'billing',
    title: 'Billing',
    content: 'Manage your plan, payment methods, and download past invoices.',
  },
  {
    value: 'notifications',
    title: 'Notifications',
    content: 'Choose which email and in-app notifications you want to receive.',
  },
]
</script>

<template>
  <div class="w-full max-w-lg">
    <Accordion type="multiple" v-model="open" :items="items" />
  </div>
</template>
```

## Controlled

Bind `v-model` to own the open state in the parent — external controls can drive
it, and you can react to every change.

```vue
<script setup lang="ts">
import { ref } from 'vue'
import { Button } from 'frappe-ui'
import { Accordion } from '..'

// "Controlled" means the parent owns the open state via v-model — the
// Accordion reflects `open`, and external controls can drive it too.
const items = [
  {
    value: 'overview',
    title: 'Overview',
    content: 'A short summary of the project.',
  },
  {
    value: 'specs',
    title: 'Specs',
    content: 'Detailed technical specifications.',
  },
  {
    value: 'pricing',
    title: 'Pricing',
    content: 'Plans, limits, and billing details.',
  },
]

const open = ref<string[]>(['overview'])

function expandAll() {
  open.value = items.map((item) => item.value)
}

function collapseAll() {
  open.value = []
}
</script>

<template>
  <div class="w-full max-w-lg space-y-3">
    <div class="flex items-center gap-2">
      <Button label="Expand all" @click="expandAll" />
      <Button label="Collapse all" @click="collapseAll" />
      <span class="ml-auto text-p-sm text-ink-gray-5">
        Open: {{ open.length ? open.join(', ') : 'none' }}
      </span>
    </div>
    <Accordion type="multiple" v-model="open" :items="items" />
  </div>
</template>
```

## Icons

```vue
<script setup lang="ts">
import { Accordion } from '..'

const items = [
  {
    value: 'profile',
    title: 'Profile',
    icon: 'lucide-user',
    content: 'Update your name, avatar, and public profile details.',
  },
  {
    value: 'security',
    title: 'Security',
    icon: 'lucide-shield',
    content: 'Change your password and manage two-factor authentication.',
  },
  {
    value: 'archived',
    title: 'Archived (disabled)',
    icon: 'lucide-archive',
    content: 'This section is read-only.',
    disabled: true,
  },
]
</script>

<template>
  <div class="w-full max-w-lg">
    <Accordion :items="items">
      <template #item-content="{ item }">
        <p class="text-p-base text-ink-gray-6">{{ item.content }}</p>
      </template>
    </Accordion>
  </div>
</template>
```

## Header suffix

Use the `#item-suffix` slot for trailing, non-interactive header content such as
a count or `Badge`.

```vue
<script setup lang="ts">
import { Badge } from 'frappe-ui'
import { Accordion } from '..'

const items = [
  { value: 'inbox', title: 'Inbox', content: 'Messages waiting for a reply.' },
  {
    value: 'drafts',
    title: 'Drafts',
    content: 'Unsent messages saved for later.',
  },
  {
    value: 'spam',
    title: 'Spam',
    content: 'Filtered messages we think are junk.',
  },
]

// Aligned to `items` by index — the slot exposes `{ item, index }`.
const badges = [
  { label: '12', theme: 'blue' },
  { label: '3', theme: 'gray' },
  { label: 'New', theme: 'red' },
] as const
</script>

<template>
  <div class="w-full max-w-lg">
    <Accordion :items="items" default-value="inbox">
      <template #item-suffix="{ index }">
        <Badge
          :label="badges[index].label"
          :theme="badges[index].theme"
          variant="subtle"
        />
      </template>
    </Accordion>
  </div>
</template>
```

## API reference

```ts
const propsData = [
  {
    name: 'as',
    description: 'Element/component used to render the accordion container.',
    required: false,
    type: 'string'
  },
  {
    name: 'headingTag',
    description: 'Heading tag each trigger is wrapped in. The WAI-ARIA accordion pattern\nrequires triggers to sit inside a heading so assistive tech can navigate\nby heading; pick the level that fits the surrounding outline.',
    required: false,
    type: '"h1" | "h2" | "h3" | "h4" | "h5" | "h6"',
    default: '"h3"'
  },
  {
    name: 'items',
    description: 'List of items to render.',
    required: true,
    type: 'AccordionItem[]'
  },
  {
    name: 'type',
    description: 'Whether one (`single`) or several (`multiple`) items can be open at the\nsame time. Determines the shape of `modelValue` / `defaultValue`:\na `string` for `single`, a `string[]` for `multiple`.',
    required: false,
    type: '"single" | "multiple"',
    default: '"single"'
  },
  {
    name: 'collapsible',
    description: 'When `type` is `single`, allows the open item to be collapsed again by\nclicking its trigger. Ignored for `multiple`.',
    required: false,
    type: 'boolean',
    default: 'true'
  },
  {
    name: 'modelValue',
    description: 'Currently open item value(s). Use with `v-model`.',
    required: false,
    type: 'string | string[]'
  },
  {
    name: 'defaultValue',
    description: 'Open item value(s) for uncontrolled usage.',
    required: false,
    type: 'string | string[]'
  },
  {
    name: 'disabled',
    description: 'Disables the whole accordion.',
    required: false,
    type: 'boolean',
    default: 'false'
  }
]

  const slotsData = [
  {
    name: 'item-label',
    description: 'Custom renderer for an item\'s trigger label. Receives `{ item, index }`.',
    type: '{ item: AccordionItem; index: number; }'
  },
  {
    name: 'item-suffix',
    description: 'Trailing, non-interactive header content (e.g. a `Badge` or count) shown\nbefore the chevron. Receives `{ item, index }`.',
    type: '{ item: AccordionItem; index: number; }'
  },
  {
    name: 'item-content',
    description: 'Custom renderer for an item\'s panel. Receives `{ item, index }`.',
    type: '{ item: AccordionItem; index: number; }'
  }
]

  const emitsData = [
  {
    name: 'update:modelValue',
    description: 'Fired when the model value changes.',
    type: '[value: string | string[] | undefined]'
  }
]
```
