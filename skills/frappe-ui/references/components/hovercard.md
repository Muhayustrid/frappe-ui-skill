<!-- Upstream: https://github.com/frappe/frappe-ui/blob/b435c66b51c0b19425a2a049b43e3ef2e308a8ab/src/components/HoverCard/HoverCard.md -->

# HoverCard

A floating panel that opens when the pointer rests on a trigger or the
keyboard focuses it, for previews of a person or a link. For a panel that
opens on click, use [`Popover`](popover.md), and for a short text hint, use
[`Tooltip`](tooltip.md).

```vue
<script setup lang="ts">
import { Avatar, HoverCard } from 'frappe-ui'
</script>

<template>
  <HoverCard>
    <template #trigger>
      <a href="#" class="text-ink-gray-9 underline underline-offset-2">
        @jane
      </a>
    </template>
    <template #default>
      <div class="flex w-64 gap-3 p-3">
        <Avatar
          label="Jane Doe"
          image="https://i.pravatar.cc/80?u=jane@example.com"
          size="2xl"
        />
        <div>
          <p class="text-p-base font-medium text-ink-gray-9">Jane Doe</p>
          <p class="mt-1 text-p-sm text-ink-gray-6">
            Product designer on the Mobile team.
          </p>
        </div>
      </div>
    </template>
  </HoverCard>
</template>
```

## Examples

### Link preview

`side="top"` opens the card above the link, and `arrow` points it back at the
link.

```vue
<script setup lang="ts">
import { HoverCard } from 'frappe-ui'
</script>

<template>
  <p class="max-w-sm text-p-base text-ink-gray-8">
    Before you start, read the
    <HoverCard side="top" arrow>
      <template #trigger>
        <a
          href="#"
          class="text-ink-blue-link underline underline-offset-2"
          @click.prevent
          >release checklist</a
        >
      </template>
      <template #default>
        <div class="w-72 p-3">
          <p class="text-sm text-ink-gray-5">wiki.example.com</p>
          <p class="mt-1 text-base-medium text-ink-gray-8">Release checklist</p>
          <p class="mt-1 text-p-sm text-ink-gray-6">
            The steps to follow before every release: tests, changelog, version
            bump and the announcement.
          </p>
        </div>
      </template>
    </HoverCard>
    and assign yourself a step.
  </p>
</template>
```

### Assignees

Shorter `hover-delay` and `leave-delay` values let the pointer move across a
row of avatars and open each card quickly.

```vue
<script setup lang="ts">
import { Avatar, HoverCard } from 'frappe-ui'

const people = [
  { name: 'Jane Cooper', email: 'jane@example.com', role: 'Designer' },
  { name: 'Wade Warren', email: 'wade@example.com', role: 'Engineer' },
  {
    name: 'Esther Howard',
    email: 'esther@example.com',
    role: 'Product manager',
  },
]
</script>

<template>
  <div class="flex items-center gap-1">
    <HoverCard
      v-for="person in people"
      :key="person.email"
      :hover-delay="100"
      :leave-delay="100"
    >
      <template #trigger>
        <button type="button" class="rounded-full" :aria-label="person.name">
          <Avatar
            :label="person.name"
            :image="`https://i.pravatar.cc/80?u=${person.email}`"
            size="md"
          />
        </button>
      </template>
      <template #default>
        <div class="flex w-60 items-center gap-3 p-3">
          <Avatar
            :label="person.name"
            :image="`https://i.pravatar.cc/80?u=${person.email}`"
            size="xl"
          />
          <div class="min-w-0">
            <p class="text-base-medium text-ink-gray-8">{{ person.name }}</p>
            <p class="mt-1 text-sm text-ink-gray-5">{{ person.role }}</p>
            <p class="mt-1 truncate text-sm text-ink-gray-5">
              {{ person.email }}
            </p>
          </div>
        </div>
      </template>
    </HoverCard>
  </div>
</template>
```

## Behavior

### Trigger and content

Put the trigger in `#trigger` and the card content in `#default`. Both slots
receive `{ open, setOpen, close }`, where `close()` is the same as
`setOpen(false)`.

### Delays

`hoverDelay` is how long the pointer must rest on the trigger before the card
opens. `leaveDelay` is how long the card waits after the pointer leaves before
it closes. Both are in milliseconds, like `Tooltip`'s, and both default to
`300`.

### Moving into the card

The card stays open while the pointer is over the trigger or over the card, so
people can move into the card and click a link in it.

### Position

`side`, `align`, `offset` and `collisionPadding` work the same as on
[`Popover`](popover.md#position).

## Accessibility

The card also opens when the trigger gets keyboard focus. Touch screens and
screen readers may never show it, so do not put an action or a fact in the card
that people cannot reach another way.

## API reference

```ts
const propsData = [
  {
    name: 'side',
    description: 'Side of the trigger the card is placed on.',
    required: false,
    type: 'PopoverSide',
    default: '"bottom"'
  },
  {
    name: 'align',
    description: 'Alignment of the card relative to the trigger.',
    required: false,
    type: 'PopoverAlign',
    default: '"start"'
  },
  {
    name: 'offset',
    description: 'Distance in pixels between the card and the trigger.',
    required: false,
    type: 'number',
    default: '4'
  },
  {
    name: 'portalTo',
    description: 'Where the card is teleported to in the DOM. Unset, an embedding host\'s target is used, else `body`.',
    required: false,
    type: 'PortalTarget'
  },
  {
    name: 'collisionPadding',
    description: 'Padding (in pixels) kept between the card and the viewport edges when\nrepositioning to avoid collisions.',
    required: false,
    type: 'number',
    default: '10'
  },
  {
    name: 'hoverDelay',
    description: 'Delay in milliseconds before the card opens. Default: `300`.',
    required: false,
    type: 'number',
    default: '300'
  },
  {
    name: 'leaveDelay',
    description: 'Delay in milliseconds before the card closes. Default: `300`.',
    required: false,
    type: 'number',
    default: '300'
  },
  {
    name: 'arrow',
    description: 'Render a small arrow pointing at the trigger. Styled to match the panel\nsurface.',
    required: false,
    type: 'boolean',
    default: 'false'
  },
  {
    name: 'open',
    description: 'Controls the visibility of the hover card.',
    required: false,
    type: 'boolean',
    default: 'false'
  }
]

  const slotsData = [
  {
    name: 'trigger',
    description: 'Trigger element. Rendered as-child so hover/focus a11y is auto-wired.',
    type: 'HoverCardSlotProps'
  },
  {
    name: 'default',
    description: 'Card contents, rendered inside the standard PopoverPanel shell.',
    type: 'HoverCardSlotProps'
  }
]

  const emitsData = [
  {
    name: 'update:open',
    description: 'Fired when the open state changes.',
    type: '[value: boolean]'
  }
]

  const exposedData = [
  {
    name: 'open',
    description: 'Opens the card.',
    type: '() => void'
  },
  {
    name: 'close',
    description: 'Closes the card.',
    type: '() => void'
  }
]
```
