<!-- Upstream: https://github.com/frappe/frappe-ui/blob/b435c66b51c0b19425a2a049b43e3ef2e308a8ab/src/components/Button/Button.md -->

# Button

A clickable element that runs an action, submits a form or opens a link.



## Examples

### List view controls

Filter and view controls above a list. `icon-left` and `icon-right` put an icon
beside the label, `icon` alone makes an icon-only button, and the `#suffix` slot
holds a count.

```vue
<script setup>
import { Button } from 'frappe-ui'
</script>

<template>
  <div class="flex w-full max-w-[1100px] items-center gap-8">
    <div class="flex flex-1 items-center gap-1.5">
      <Button>Lead owner</Button>
      <Button icon-right="lucide-chevron-down">Status</Button>
      <Button icon-right="lucide-chevron-down">Organisation</Button>
    </div>
    <div class="flex items-center gap-2">
      <Button icon-left="lucide-eye-off">Columns</Button>
      <Button icon-left="lucide-list-filter">
        Filter
        <template #suffix>
          <span
            class="inline-flex h-4.5 items-center justify-center rounded-1 bg-surface-base px-1.5 text-xs text-ink-gray-7 shadow-sm ring-1 ring-black/5"
          >
            1
          </span>
        </template>
      </Button>
      <Button icon-left="lucide-arrow-up-down">Sort</Button>
      <Button icon="lucide-ellipsis" label="More" />
    </div>
  </div>
</template>
```

### Section header

A `ghost` button with the section name, and an add action at the other end of
the row.

```vue
<script setup>
import { Button } from 'frappe-ui'
</script>

<template>
  <div class="flex w-full max-w-[352px] items-center gap-2 p-3">
    <Button
      variant="ghost"
      icon-left="lucide-chevron-down"
      class="-ml-2 font-medium"
    >
      Contacts
    </Button>
    <div class="flex-1" />
    <Button icon-left="lucide-plus">Add contact</Button>
  </div>
</template>
```

### Selection toolbar

A row of `ghost` buttons that act on the selected items. `theme="red"` marks the
destructive action.

```vue
<script setup>
import { Button } from 'frappe-ui'
</script>

<template>
  <div
    class="inline-flex items-center gap-3.5 rounded-6 bg-surface-base p-1.5 shadow-[0px_0px_1.5px_rgba(0,0,0,0.16),0px_2px_5px_rgba(0,0,0,0.14)]"
  >
    <Button variant="ghost" icon-left="lucide-x">2 selected</Button>
    <div class="h-5 border-l border-outline-gray-1" />
    <div class="flex items-center gap-1">
      <Button variant="ghost" icon-left="lucide-download">Download</Button>
      <Button variant="ghost" icon-left="lucide-corner-up-right">Move</Button>
      <Button variant="ghost" icon-left="lucide-share-2">Share</Button>
      <Button variant="ghost" theme="red" icon-left="lucide-trash-2">
        Delete
      </Button>
    </div>
  </div>
</template>
```

### Primary and secondary action

A `solid` button for the main action beside a default `subtle` one.

```vue
<script setup>
import { Button } from 'frappe-ui'
</script>

<template>
  <div class="flex items-center gap-2">
    <Button>Join</Button>
    <Button variant="solid">Reschedule</Button>
  </div>
</template>
```

### Full-width actions

Add `class="w-full"` to stack buttons in a narrow column.

```vue
<script setup>
import { Button } from 'frappe-ui'
</script>

<template>
  <div class="flex w-58 flex-col items-stretch gap-2">
    <Button class="w-full">Join</Button>
    <Button variant="solid" class="w-full">Reschedule</Button>
  </div>
</template>
```

### Card actions

Two actions that share the width of a card, each with `class="flex-1"`.

```vue
<script setup>
import { Button } from 'frappe-ui'
</script>

<template>
  <div
    class="flex w-96 flex-col gap-6 rounded-6 bg-surface-base p-4 shadow-[0px_0px_0.75px_rgba(0,0,0,0.16),0px_2px_2.5px_rgba(0,0,0,0.14)]"
  >
    <div class="flex flex-col gap-5">
      <div class="flex flex-col gap-3">
        <p class="text-md-semibold text-ink-gray-7">Frappe january - 2023</p>
        <div class="flex flex-col gap-3">
          <div class="flex items-center gap-2 text-base text-ink-gray-7">
            <span class="lucide-calendar size-4" aria-hidden="true" />
            <span>Wed, Dec 27, 2023</span>
          </div>
          <div class="flex items-center gap-2 text-base text-ink-gray-7">
            <span class="lucide-clock size-4" aria-hidden="true" />
            <span>03:45 PM - 04:15 PM (30 min)</span>
          </div>
        </div>
      </div>
      <p class="text-base leading-normal text-ink-gray-7">
        Digital products are more abstract and complex than any product we’v…
      </p>
    </div>
    <div class="flex items-center gap-2">
      <Button class="flex-1">Start</Button>
      <Button variant="solid" class="flex-1" icon-left="lucide-video">
        Join
      </Button>
    </div>
  </div>
</template>
```

## Behavior

### Pressed state

`Button` shows its pressed look while its `data-state` attribute is `open` or
`active`. There is no `active` prop.

As a menu trigger, this needs no wiring. The menu sets `data-state="open"` on
the trigger, so the button stays pressed while the menu is open.

```vue
<Dropdown :options="options" :button="{ label: 'Options' }" />
```

For a standalone toggle, such as a toolbar control or a filter chip, set the
attribute yourself. It changes only the look, so pair it with `aria-pressed`:

```vue
<Button
  label="Bold"
  :data-state="isBold ? 'active' : 'inactive'"
  :aria-pressed="isBold"
/>
```

### Loading

`loading` shows a spinner in place of the left icon and makes the button
non-interactive, without the dimmed disabled look. `loading-text` replaces the
label while it loads. Only `disabled` dims the button.

### Links

With `route`, the button renders as a router link. With `href`, it renders as
an `<a>` that opens the URL in a new tab. A disabled or loading button always
renders as a native `<button>`, so it cannot be followed. `type` sets the native
button type and defaults to `button`.

## Accessibility

- An icon-only button needs a `label`. The label is not shown. It becomes the
  button's `aria-label`.
- A loading button sets `aria-busy="true"`.

## API reference

```ts
const propsData = [
  {
    name: 'theme',
    description: 'Visual color theme of the button',
    required: false,
    type: 'Theme',
    default: '\'gray\''
  },
  {
    name: 'size',
    description: 'Controls the button size',
    required: false,
    type: 'Size',
    default: '\'sm\''
  },
  {
    name: 'variant',
    description: 'Visual style of the button',
    required: false,
    type: 'Variant',
    default: '\'subtle\''
  },
  {
    name: 'label',
    description: 'Text label displayed inside the button',
    required: false,
    type: 'string'
  },
  {
    name: 'icon',
    description: 'Icon shown when no left or right icon is specified',
    required: false,
    type: 'string | Component'
  },
  {
    name: 'iconLeft',
    description: 'Icon shown before the label',
    required: false,
    type: 'string | Component'
  },
  {
    name: 'iconRight',
    description: 'Icon shown after the label',
    required: false,
    type: 'string | Component'
  },
  {
    name: 'tooltip',
    description: 'Tooltip text shown on hover',
    required: false,
    type: 'string'
  },
  {
    name: 'loading',
    description: 'Shows a loading state and disables interaction',
    required: false,
    type: 'boolean',
    default: 'false'
  },
  {
    name: 'loadingText',
    description: 'Text shown while the button is loading',
    required: false,
    type: 'string'
  },
  {
    name: 'disabled',
    description: 'Disables the button',
    required: false,
    type: 'boolean',
    default: 'false'
  },
  {
    name: 'route',
    description: 'Router destination when used as a link',
    required: false,
    type: 'RouteDestination'
  },
  {
    name: 'href',
    description: 'External URL, opened in a new tab.',
    required: false,
    type: 'string'
  },
  {
    name: 'type',
    description: 'Native button type',
    required: false,
    type: '"button" | "submit" | "reset"',
    default: '\'button\''
  }
]

  const slotsData = [
  {
    name: 'prefix',
    description: 'Content shown before the button label (left icon / custom content)',
    type: '[void]'
  },
  {
    name: 'icon',
    description: 'Icon-only content for icon buttons',
    type: '[void]'
  },
  {
    name: 'default',
    description: 'Main button content (overrides `label`)',
    type: '[void]'
  },
  {
    name: 'suffix',
    description: 'Content shown after the button label (right icon / custom content)',
    type: '[void]'
  }
]
```
