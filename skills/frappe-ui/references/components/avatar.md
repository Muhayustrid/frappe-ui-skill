<!-- Upstream: https://github.com/frappe/frappe-ui/blob/b435c66b51c0b19425a2a049b43e3ef2e308a8ab/src/components/Avatar/Avatar.md -->

# Avatar

Shows a person or a workspace as a photo, or as the first letter of their name
when there is no photo.



## Examples

### Comment thread

A photo when the person has one, and their initial in a theme color when they
do not.

```vue
<script setup lang="ts">
import { Avatar } from 'frappe-ui'

const avatarFor = (seed: string) => `https://i.pravatar.cc/80?u=${seed}`

// Sara has no photo, so her avatar falls back to her initial.
const comments = [
  {
    author: 'Jane Cooper',
    image: avatarFor('jane@example.com'),
    time: '2h',
    text: 'Pushed the new pricing table. Can someone check the mobile layout?',
  },
  {
    author: 'Sara Khan',
    image: '',
    theme: 'violet' as const,
    time: '1h',
    text: 'Looks good on my phone. The last column wraps a little early.',
  },
  {
    author: 'Rahul Mehta',
    image: avatarFor('rahul@example.com'),
    time: '12m',
    text: 'Fixed the wrap. Merging after lunch.',
  },
]
</script>

<template>
  <div class="flex w-full max-w-md flex-col gap-4">
    <div v-for="c in comments" :key="c.author" class="flex gap-2.5">
      <Avatar :image="c.image" :label="c.author" :theme="c.theme" size="lg" />
      <div class="flex flex-col gap-1">
        <div class="flex items-baseline gap-1.5">
          <span class="text-base font-medium text-ink-gray-9">{{
            c.author
          }}</span>
          <span class="text-sm text-ink-gray-5">{{ c.time }}</span>
        </div>
        <p class="text-p-base text-ink-gray-7">{{ c.text }}</p>
      </div>
    </div>
  </div>
</template>
```

### Assignees

Overlapping avatars on a task row. A ring in the page color separates each
avatar from the one under it, and a count stands in for the rest.

```vue
<script setup lang="ts">
import { Avatar } from 'frappe-ui'

const avatarFor = (seed: string) => `https://i.pravatar.cc/80?u=${seed}`

const people = {
  jane: { label: 'Jane Cooper', image: avatarFor('jane@example.com') },
  rahul: { label: 'Rahul Mehta', image: avatarFor('rahul@example.com') },
  sara: { label: 'Sara Khan', image: '' },
  leo: { label: 'Leo Park', image: avatarFor('leo@example.com') },
  mia: { label: 'Mia Wong', image: avatarFor('mia@example.com') },
}

const tasks = [
  {
    title: 'Redesign the invoice page',
    assignees: [people.jane, people.rahul],
  },
  {
    title: 'Migrate reports to the new API',
    assignees: [people.sara, people.leo, people.mia, people.jane, people.rahul],
  },
  { title: 'Write release notes', assignees: [people.mia] },
]

const shown = 3

// A ring in the page color cuts each avatar out of the one under it.
const cutout = { boxShadow: '0 0 0 2px var(--surface-base)' }
</script>

<template>
  <div class="flex w-full max-w-md flex-col divide-y divide-outline-gray-1">
    <div
      v-for="task in tasks"
      :key="task.title"
      class="flex items-center gap-3 py-2.5"
    >
      <span class="lucide-circle size-4 text-ink-gray-4" aria-hidden="true" />
      <span class="flex-1 truncate text-base text-ink-gray-8">
        {{ task.title }}
      </span>
      <div class="flex -space-x-1.5">
        <Avatar
          v-for="person in task.assignees.slice(0, shown)"
          :key="person.label"
          :image="person.image"
          :label="person.label"
          size="lg"
          :style="cutout"
        />
        <span
          v-if="task.assignees.length > shown"
          class="grid size-7 place-items-center rounded-full bg-surface-gray-2 text-xs font-medium text-ink-gray-6"
          :style="cutout"
        >
          +{{ task.assignees.length - shown }}
        </span>
      </div>
    </div>
  </div>
</template>
```

### Online status

The `#indicator` slot places a dot on the bottom-right corner. A class on the
dot sets its color.

```vue
<Avatar :image :label>
  <template #indicator>
    <span class="block size-full rounded-full bg-surface-green-5" />
  </template>
</Avatar>
```

```vue
<script setup lang="ts">
import { Avatar } from 'frappe-ui'

const avatarFor = (seed: string) => `https://i.pravatar.cc/80?u=${seed}`

const dot = {
  online: 'bg-surface-green-5',
  away: 'bg-surface-amber-5',
  offline: 'bg-surface-gray-4',
}

const members = [
  {
    name: 'Jane Cooper',
    status: 'online' as const,
    image: avatarFor('jane@example.com'),
  },
  {
    name: 'Rahul Mehta',
    status: 'away' as const,
    image: avatarFor('rahul@example.com'),
  },
  { name: 'Sara Khan', status: 'offline' as const, image: '' },
]
</script>

<template>
  <div class="flex w-60 flex-col gap-1">
    <div
      v-for="m in members"
      :key="m.name"
      class="flex items-center gap-2 rounded-4 px-2 py-1.5 transition-colors hover:bg-surface-gray-1"
    >
      <Avatar :image="m.image" :label="m.name" size="lg">
        <template #indicator>
          <span class="block size-full rounded-full" :class="dot[m.status]" />
        </template>
      </Avatar>
      <span class="flex-1 text-base text-ink-gray-8">{{ m.name }}</span>
      <span class="text-sm capitalize text-ink-gray-5">{{ m.status }}</span>
    </div>
  </div>
</template>
```

### Verified badge

The indicator can hold an icon. Use a size with room for it, and give it text
for screen readers.

```vue
<script setup lang="ts">
import { Avatar } from 'frappe-ui'
</script>

<template>
  <div class="flex items-center gap-3 rounded-6 border border-outline-gray-1 bg-surface-gray-2 p-3">
    <Avatar
      image="https://i.pravatar.cc/80?u=jane@example.com"
      label="Jane Cooper"
      size="3xl"
    >
      <template #indicator>
        <span
          class="flex size-full items-center justify-center rounded-full bg-surface-blue-6 text-white"
        >
          <span class="lucide-check size-2" aria-hidden="true" />
          <span class="sr-only">Verified</span>
        </span>
      </template>
    </Avatar>
    <div class="flex flex-col gap-0.5">
      <span class="text-base font-medium text-ink-gray-9">Jane Cooper</span>
      <span class="text-sm text-ink-gray-5">Verified account</span>
    </div>
  </div>
</template>
```

### Workspace switcher

Square avatars stand for workspaces and teams, round ones for people. The
default slot replaces the initial with any content, here an icon.

```vue
<script setup lang="ts">
import { Avatar } from 'frappe-ui'
import type { AvatarTheme } from 'frappe-ui'

// Square avatars stand for places, round ones for people.
const workspaces: { name: string; members: number; theme: AvatarTheme }[] = [
  { name: 'Acme Design', members: 12, theme: 'blue' },
  { name: 'Growth', members: 5, theme: 'green' },
  { name: 'Support', members: 8, theme: 'amber' },
]
</script>

<template>
  <div
    class="flex w-60 flex-col gap-1 rounded-lg bg-surface-elevation-2 p-1.5 shadow-xl ring-1 ring-black/5"
  >
    <button
      v-for="(w, i) in workspaces"
      :key="w.name"
      type="button"
      class="flex items-center gap-2 rounded px-2 py-1.5 text-left hover:bg-surface-gray-2"
    >
      <Avatar :label="w.name" :theme="w.theme" shape="square" size="xl" />
      <span class="flex flex-1 flex-col">
        <span class="text-base font-medium text-ink-gray-8">{{ w.name }}</span>
        <span class="text-sm text-ink-gray-5">{{ w.members }} members</span>
      </span>
      <span
        v-if="i === 0"
        class="lucide-check size-4 text-ink-gray-7"
        aria-hidden="true"
      />
    </button>
    <div class="my-1 border-t border-outline-gray-1" />
    <button
      type="button"
      class="flex items-center gap-2 rounded px-2 py-1.5 text-left hover:bg-surface-gray-2"
    >
      <!-- The default slot replaces the initial, here with an icon. -->
      <Avatar shape="square" size="xl">
        <span class="lucide-plus size-full" aria-hidden="true" />
      </Avatar>
      <span class="text-base text-ink-gray-8">New workspace</span>
    </button>
  </div>
</template>
```

### Profile header

A `size-*` class sets a size beyond the largest `size` step.

```vue
<script setup lang="ts">
import { Avatar, Button } from 'frappe-ui'
</script>

<template>
  <div class="flex w-full max-w-md items-center gap-4">
    <!-- A size-* class goes past the largest size step. -->
    <Avatar
      image="https://i.pravatar.cc/160?u=jane@example.com"
      label="Jane Cooper"
      class="size-16"
    />
    <div class="flex flex-1 flex-col gap-1">
      <span class="text-xl font-semibold text-ink-gray-9">Jane Cooper</span>
      <span class="text-base text-ink-gray-6"
        >Product designer · Bengaluru</span
      >
    </div>
    <Button label="Message" icon-left="lucide-message-circle" />
  </div>
</template>
```

## Behavior

### Fallback

The avatar shows the initial when `image` is empty or the image fails to load.
The initial is the first character of `label`, uppercased, so pass the full
name rather than initials: `label="Jane Cooper"` shows `J`. `theme` colors the
initial and its background. It has no effect on a photo.

### Custom sizes

A sizing class on the avatar (`size-*`, `w-*`, `h-*`) replaces the `size`
prop's width and height. The `size` prop still sets the text size of the
initial and the size of the indicator. A responsive class like
`sm:size-16` works alongside `size`: `size` sets the width on small screens,
and the class takes over from its breakpoint up.

### Indicator

The avatar cuts a circle out of its bottom-right corner for the indicator. The
gap around the indicator is see-through, so it matches the surface behind the
avatar, such as a hovered row, a popover or a dialog.

## Accessibility

The image uses `label` as its alt text.

A status dot shows status with color only. Show the status as text next to the
avatar, or put text with the `sr-only` class in the indicator.

## API reference

```ts
const propsData = [
  {
    name: 'image',
    description: 'Image URL used for the avatar',
    required: false,
    type: 'string'
  },
  {
    name: 'label',
    description: 'Fallback text shown when the image is missing',
    required: false,
    type: 'string'
  },
  {
    name: 'size',
    description: 'Controls the overall size of the avatar',
    required: false,
    type: '"xs" | "sm" | "md" | "lg" | "xl" | "2xl" | "3xl"',
    default: '"md"'
  },
  {
    name: 'shape',
    description: 'Defines the avatar shape',
    required: false,
    type: '"circle" | "square"',
    default: '"circle"'
  },
  {
    name: 'theme',
    description: 'Visual color theme used for the fallback avatar',
    required: false,
    type: 'AvatarTheme',
    default: '"gray"'
  }
]

  const slotsData = [
  {
    name: 'default',
    description: 'Custom content shown inside the avatar (icon / initials replacement)',
    type: 'any'
  },
  {
    name: 'indicator',
    description: 'Small indicator shown at the bottom-right of the avatar',
    type: 'any'
  }
]
```
