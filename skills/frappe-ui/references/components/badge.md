<!-- Upstream: https://github.com/frappe/frappe-ui/blob/b435c66b51c0b19425a2a049b43e3ef2e308a8ab/src/components/Badge/Badge.md -->

# Badge

A small pill-shaped label for a status, a count or a short piece of metadata.



## Examples

### Event status

A red badge with an icon in `#prefix` flags an event that needs attention.

```vue
<script setup>
import { Badge } from 'frappe-ui'
</script>

<template>
  <div class="w-96 rounded-r-5 border-l-2 border-outline-gray-3 px-3 py-2 transition-colors hover:bg-surface-gray-1">
    <Badge theme="red" size="md">
      <template #prefix>
        <span class="lucide-mail" aria-hidden="true" />
      </template>
      Awaiting response
    </Badge>
    <p class="mt-2 text-base-medium leading-relaxed text-ink-gray-8">Supply Chain Sync</p>
    <p class="text-sm leading-relaxed text-ink-gray-6">2:30 PM - 3:00 PM</p>
  </div>
</template>
```

### Call metadata

Gray badges with an icon in `#prefix` show the date and length of a call, and
links to its recording and note.

```vue
<script setup>
import { Badge } from 'frappe-ui'
</script>

<template>
  <div class="w-[28rem]">
    <p class="mb-2 text-base-medium text-ink-gray-8">Outbound Call</p>
    <div class="flex flex-wrap items-center gap-1.5">
      <Badge>
        <template #prefix>
          <span class="lucide-calendar" aria-hidden="true" />
        </template>
        May 16, Friday
      </Badge>
      <Badge>
        <template #prefix>
          <span class="lucide-timer" aria-hidden="true" />
        </template>
        32:48
      </Badge>
      <Badge>
        <template #prefix>
          <span class="lucide-play" aria-hidden="true" />
        </template>
        Listen
      </Badge>
      <Badge>
        <template #prefix>
          <span class="lucide-sticky-note" aria-hidden="true" />
        </template>
        Note
      </Badge>
    </div>
  </div>
</template>
```

### List status

The status column of a table maps each status to a `theme`. A colored dot in
`#prefix` repeats the color. An `outline` badge in the header shows the count.

```vue
<script setup>
import { Badge } from 'frappe-ui'

const leads = [
  {
    name: 'Acme Corp',
    owner: 'Jane Cooper',
    status: 'Qualified',
    theme: 'green',
    dotClass: 'bg-surface-green-7',
    value: '$24.5k',
  },
  {
    name: 'Globex',
    owner: 'Sammy Well',
    status: 'In Progress',
    theme: 'blue',
    dotClass: 'bg-surface-blue-7',
    value: '$18.2k',
  },
  {
    name: 'Initech',
    owner: 'Brian Robinson',
    status: 'On Hold',
    theme: 'amber',
    dotClass: 'bg-surface-amber-7',
    value: '$9.8k',
  },
  {
    name: 'Soylent',
    owner: 'Matt Clinton',
    status: 'Lost',
    theme: 'red',
    dotClass: 'bg-surface-red-7',
    value: '$4.1k',
  },
  {
    name: 'Umbrella',
    owner: 'Jane Cooper',
    status: 'New',
    theme: 'gray',
    dotClass: 'bg-surface-gray-8',
    value: '$12.0k',
  },
]
</script>

<template>
  <div
    class="w-full max-w-[640px] overflow-hidden rounded-6 border border-outline-gray-1 bg-surface-base shadow-sm"
  >
    <div class="flex items-center justify-between border-b border-outline-gray-1 px-3 py-2.5">
      <h3 class="m-0 text-base-medium text-ink-gray-8">Lead pipeline</h3>
      <Badge theme="gray" variant="outline">5 leads</Badge>
    </div>

    <div
      class="grid grid-cols-[minmax(0,1fr)_7.5rem_6.5rem_5rem] gap-3 border-b border-outline-gray-1 bg-surface-gray-1 px-3 py-1.5 text-sm-medium text-ink-gray-5"
    >
      <span>Company</span>
      <span>Owner</span>
      <span>Status</span>
      <span>Value</span>
    </div>

    <div class="divide-y divide-outline-gray-1">
      <div
        v-for="lead in leads"
        :key="lead.name"
        class="grid grid-cols-[minmax(0,1fr)_7.5rem_6.5rem_5rem] items-center gap-3 px-3 py-2 transition-colors hover:bg-surface-gray-1"
      >
        <div class="flex min-w-0 items-center gap-2">
          <div
            class="flex size-6 shrink-0 items-center justify-center rounded-4 bg-surface-gray-2 text-xs-medium text-ink-gray-6"
          >
            {{ lead.name.slice(0, 1) }}
          </div>
          <span class="truncate text-sm text-ink-gray-8">{{ lead.name }}</span>
        </div>
        <span class="truncate text-sm text-ink-gray-6">{{ lead.owner }}</span>
        <Badge :theme="lead.theme" class="justify-self-start">
          <template #prefix>
            <span class="size-1 rounded-full" :class="lead.dotClass" />
          </template>
          {{ lead.status }}
        </Badge>
        <span class="text-sm-medium text-ink-gray-7">{{ lead.value }}</span>
      </div>
    </div>
  </div>
</template>
```

### Row tags

Colored tags on a task row, a gray "+2" badge for the tags that do not fit,
and a due date.

```vue
<script setup>
import { Badge } from 'frappe-ui'
</script>

<template>
  <div class="flex w-full max-w-[640px] flex-col divide-y divide-outline-gray-1">
    <div class="flex items-center gap-3 py-2.5">
      <span class="lucide-circle size-4 text-ink-gray-4" aria-hidden="true" />
      <span class="flex-1 text-base text-ink-gray-8">
        Hyper-verge landing page
      </span>
      <div class="flex items-center gap-1.5">
        <Badge theme="green">App</Badge>
        <Badge theme="amber">Frappe</Badge>
        <Badge>+2</Badge>
      </div>
      <Badge theme="red">
        <template #prefix>
          <span class="lucide-calendar" aria-hidden="true" />
        </template>
        23 Feb
      </Badge>
    </div>
    <div class="flex items-center gap-3 py-2.5">
      <span class="lucide-circle size-4 text-ink-gray-4" aria-hidden="true" />
      <span class="flex-1 text-base text-ink-gray-8">Client feedback</span>
      <div class="flex items-center gap-1.5">
        <Badge theme="red">Opensource</Badge>
        <Badge>+3</Badge>
      </div>
      <Badge theme="red">
        <template #prefix>
          <span class="lucide-calendar" aria-hidden="true" />
        </template>
        28 Feb
      </Badge>
    </div>
  </div>
</template>
```

### Reactions

An emoji in `#prefix` and a count as the label.

```vue
<script setup>
import { Badge } from 'frappe-ui'

const reactions = [
  { emoji: '😄', count: 12 },
  { emoji: '🍩', count: 8 },
  { emoji: '🔥', count: 7 },
]
</script>

<template>
  <div class="w-[28rem]">
    <p class="text-base text-ink-gray-8">
      The words breathing and ventilation are hyponyms, not synonyms, of
      respiration.
    </p>
    <div class="mt-2 flex items-center gap-1.5">
      <Badge v-for="r in reactions" :key="r.emoji" size="md">
        <template #prefix>
          <span class="text-sm leading-none">{{ r.emoji }}</span>
        </template>
        {{ r.count }}
      </Badge>
      <button
        type="button"
        class="inline-flex h-5 items-center justify-center rounded-full px-1.5 text-ink-gray-5 hover:bg-surface-gray-2"
        aria-label="Add reaction"
      >
        <span class="lucide-smile-plus size-3.5" aria-hidden="true" />
      </button>
    </div>
  </div>
</template>
```

## Behavior

### Label and slots

`label` takes a string or a number. The default slot replaces it. `#prefix`
and `#suffix` place content before and after the label, such as an icon or a
dot. Their size follows `size`.

### Unsupported values

A `theme`, `variant` or `size` outside the supported values falls back to the
default (`gray`, `subtle`, `md`) and logs a warning in development.

## API reference

```ts
const propsData = [
  {
    name: 'theme',
    description: 'Visual color theme of the badge',
    required: false,
    type: '"gray" | "blue" | "green" | "amber" | "red" | "violet"',
    default: '"gray"'
  },
  {
    name: 'size',
    description: 'Controls the size of the badge',
    required: false,
    type: '"sm" | "md" | "lg"',
    default: '"md"'
  },
  {
    name: 'variant',
    description: 'Visual style of the badge',
    required: false,
    type: '"solid" | "subtle" | "outline" | "ghost"',
    default: '"subtle"'
  },
  {
    name: 'label',
    description: 'Content displayed inside the badge',
    required: false,
    type: 'string | number'
  }
]

  const slotsData = [
  {
    name: 'prefix',
    description: 'Content shown before the badge label',
    type: 'any'
  },
  {
    name: 'default',
    description: 'Main badge content (overrides `label` prop)',
    type: 'any'
  },
  {
    name: 'suffix',
    description: 'Content shown after the badge label',
    type: 'any'
  }
]
```
