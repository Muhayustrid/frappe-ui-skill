<!-- Upstream: https://github.com/frappe/frappe-ui/blob/b435c66b51c0b19425a2a049b43e3ef2e308a8ab/src/components/Tooltip/Tooltip.md -->

# Tooltip

A small label that describes its trigger, shown on hover or keyboard focus.
For content people interact with, such as links or buttons, use
[`HoverCard`](hovercard.md) or [`Popover`](popover.md).



## Examples

### Toolbar buttons

`TooltipProvider` groups the tooltips of a toolbar. After the first tooltip
opens, moving to the next button opens its tooltip at once.

```vue
<script setup lang="ts">
import { Button, Tooltip, TooltipProvider } from 'frappe-ui'
</script>

<template>
  <!--
    Hover any button to open its tooltip after `hover-delay`. Sweeping the
    pointer to a neighbour within `skip-delay` opens the next tooltip
    instantly — no per-button flicker — because the whole row shares one
    tooltip context.
  -->
  <TooltipProvider :hover-delay="500" :skip-delay="300">
    <div class="flex items-center gap-1">
      <Button icon="lucide-bold" tooltip="Bold" />
      <Button icon="lucide-italic" tooltip="Italic" />
      <Button icon="lucide-underline" tooltip="Underline" />
      <Tooltip text="Strikethrough">
        <Button icon="lucide-strikethrough" />
      </Tooltip>
    </div>
  </TooltipProvider>
</template>
```

### Keyboard shortcut

The `#content` slot shows the button's name and its shortcut.

```vue
<script setup lang="ts">
import { Button, KeyboardShortcut, Tooltip } from 'frappe-ui'
</script>

<template>
  <Tooltip>
    <template #content>
      <span class="flex items-center gap-2">
        Search
        <KeyboardShortcut combo="Mod+K" />
      </span>
    </template>
    <Button icon="lucide-search" aria-label="Search" />
  </Tooltip>
</template>
```

### File preview

`bare` drops the dark bubble, so an image preview shows on its own surface.

```vue
<script setup lang="ts">
import { Tooltip } from 'frappe-ui'

const file = {
  name: 'profile-photo-jane-cooper-2026.jpg',
  url: 'https://i.pravatar.cc/80?u=jane@example.com',
}
</script>

<template>
  <div
    class="flex w-48 items-center gap-2 rounded-4 border border-outline-gray-1 px-2 py-1.5"
  >
    <span
      class="lucide-image size-4 shrink-0 text-ink-gray-5"
      aria-hidden="true"
    />
    <Tooltip bare>
      <template #content>
        <img
          :src="file.url"
          :alt="file.name"
          class="size-20 rounded-4 bg-surface-base p-1 shadow-xl"
        />
      </template>
      <span class="truncate text-base text-ink-gray-8" tabindex="0">
        {{ file.name }}
      </span>
    </Tooltip>
  </div>
</template>
```

### Warning on a delete button

`:hover-delay="0"` shows the warning as soon as the pointer reaches the
button.

```vue
<script setup lang="ts">
import { Button, Tooltip } from 'frappe-ui'
</script>

<template>
  <Tooltip text="This action cannot be undone" :hover-delay="0" side="top">
    <Button theme="red">Delete</Button>
  </Tooltip>
</template>
```

## Behavior

### Trigger and content

The default slot is the **trigger**. Tooltip is the only overlay that works
this way, because `<Tooltip text="Delete"><Button /></Tooltip>` is what almost
every use needs. The trigger must be an element that can take focus, or the
tooltip does not open from the keyboard.

`text` is the label. The `#content` slot replaces it with richer content, which
shows inside the same bubble. Add `bare` when the content has its own surface,
such as an image. The arrow shows in both cases.

### Position

`side` (`top`, `right`, `bottom`, `left`) picks the edge of the trigger the
tooltip opens on, and `offset` sets the gap in pixels. Both work the same as on
`Popover` and `HoverCard`. The tooltip flips to stay inside the screen.

### Hover delay

`hoverDelay` is how long the pointer must rest on the trigger before the
tooltip opens, in milliseconds. It defaults to `500`.

### Groups

Wrap a row of triggers in a `TooltipProvider`. Once one tooltip in the group is
open, moving the pointer to another trigger within `skipDelay` opens its
tooltip with no delay. `Tooltip` and a `Button` with a `tooltip` prop use the
surrounding provider instead of creating their own. Inside a provider, the
provider's `hoverDelay` applies, not the tooltip's.

### Disabled

`disabled` turns off the tooltip and still renders the trigger. Use it when the
label applies only in some states.

## Accessibility

The tooltip opens when its trigger gets keyboard focus and closes on
<kbd>Escape</kbd>. It has the `tooltip` role, and screen readers read it as the
trigger's description.

## API reference

```ts
const tooltipProps = [
  {
    name: 'text',
    description: 'Text content shown inside the tooltip.\nIgnored if a default slot is provided.',
    required: false,
    type: 'string',
    default: '""'
  },
  {
    name: 'hoverDelay',
    description: 'Delay in milliseconds before showing the tooltip on hover. Default: `500`.',
    required: false,
    type: 'number',
    default: '500'
  },
  {
    name: 'side',
    description: 'Side of the trigger the tooltip is placed on.',
    required: false,
    type: 'TooltipSide',
    default: '"top"'
  },
  {
    name: 'offset',
    description: 'Distance in px between the trigger and the tooltip.',
    required: false,
    type: 'number',
    default: '4'
  },
  {
    name: 'bare',
    description: 'Render `#content` without the bubble shell. The arrow still renders.\nUse it for content that brings its own surface, like an image preview.',
    required: false,
    type: 'boolean',
    default: 'false'
  },
  {
    name: 'disabled',
    description: 'Disables the tooltip entirely. The trigger still renders.',
    required: false,
    type: 'boolean',
    default: 'false'
  }
]

  const tooltipSlots = [
  {
    name: 'default',
    description: 'The trigger. Tooltip is the one overlay whose `#default` is the trigger\nrather than the content — the shorthand `<Tooltip text="…"><Button /></Tooltip>`\nis the overwhelmingly common shape, and `#content` names the other half.',
    type: 'any'
  },
  {
    name: 'content',
    description: 'The tooltip\'s content. Takes precedence over `text`.',
    type: 'any'
  }
]

  const tooltipProviderProps = [
  {
    name: 'hoverDelay',
    description: 'Delay in milliseconds before the first tooltip in the group opens.',
    required: false,
    type: 'number',
    default: '500'
  },
  {
    name: 'skipDelay',
    description: 'Window in milliseconds during which moving to another trigger in this\ngroup opens its tooltip with no delay. Mirrors reka\'s\n`skipDelayDuration`.',
    required: false,
    type: 'number',
    default: '300'
  },
  {
    name: 'disableHoverableContent',
    description: 'When `true`, hovering the tooltip body closes it as the pointer\nleaves the trigger.',
    required: false,
    type: 'boolean',
    default: 'false'
  }
]

  const tooltipProviderSlots = [
  {
    name: 'default',
    description: '',
    type: '{}'
  }
]
```
