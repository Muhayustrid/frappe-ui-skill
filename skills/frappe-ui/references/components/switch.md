<!-- Upstream: https://github.com/frappe/frappe-ui/blob/b435c66b51c0b19425a2a049b43e3ef2e308a8ab/src/components/Switch/Switch.md -->

# Switch

A toggle that turns a setting on or off.



## Examples

### Notification settings

`icon` shows an icon before the label.

```vue
<script setup lang="ts">
import { Switch } from 'frappe-ui'
</script>

<template>
  <div class="flex flex-col gap-3 w-80">
    <Switch icon="lucide-bell" label="Notifications" class="w-full" />
    <Switch
      icon="lucide-mail"
      label="Email digest"
      description="A weekly summary delivered every Friday."
      class="w-full"
    />
  </div>
</template>
```

### Toolbar toggle

`padded` gives the switch a clickable surface and a hover state that match the
buttons beside it. A click anywhere on the row toggles the switch.

```vue
<script setup lang="ts">
import { ref } from 'vue'
import { Button, Switch } from 'frappe-ui'

const showActivity = ref(true)
</script>

<template>
  <div class="flex items-center gap-2">
    <Switch v-model="showActivity" padded label="Show activity" />
    <Button variant="subtle" icon-left="lucide-settings-2" label="Customize" />
    <Button variant="solid" icon-right="lucide-chevron-down" label="New" />
  </div>
</template>
```

## Behavior

### Icon

`icon` takes a `lucide-` class name, such as `"lucide-bell"`, or a component,
which is rendered with `<component :is>`.

### Control position

`controlPosition` is `end` by default, so the switch sits after the label and
the row fills the available width. `start` puts the switch before the label,
and the row fits its content. Both follow the text direction.

### Padded

`padded` wraps the switch and the label in a clickable surface with hover,
active and focus states, for toolbars and menu items. Without `padded`, only
the switch and its label respond to clicks, and the row has no hover or focus
state.

### Label, description and error

`label` renders beside the switch and `description` below it. `error` renders
below the row and hides `description`. It takes a string, an array of strings
(one line each), or an `Error`, the same values as
[ErrorMessage](errormessage.md). An empty string or an empty array means no
error. `required` adds a red asterisk to the label.

The `#label` slot replaces the label text and the required marker, and receives
`{ required }`. A `#description` slot is not hidden by `error`. It renders
above the error.

### Attributes

`class` and `style` go to the layout wrapper. Everything else (`name`,
`aria-*`, `data-*` and listeners) goes once to the interactive element, which
for `Switch` is the switch button.

## Accessibility

`Switch` renders a button with `role="switch"`, and `Space` or `Enter` toggles
it. An icon given as a class name is hidden from screen readers. `required`
sets `aria-required`. While `error` is set, the switch gets `aria-invalid` and
an `aria-errormessage` that points to the error text.

## API reference

```ts
const propsData = [
  {
    name: 'size',
    description: 'Size of the switch control',
    required: false,
    type: 'ToggleSize',
    default: '"sm"'
  },
  {
    name: 'padded',
    description: 'Wraps the control and label in a clickable surface with hover, active and focus states — useful for settings rows and menu items.',
    required: false,
    type: 'boolean',
    default: 'false'
  },
  {
    name: 'controlPosition',
    description: 'Position of the control relative to the label, along the inline axis (RTL-aware). `start` is the leading side, `end` the trailing side. Defaults to `end`, so the switch trails the label.',
    required: false,
    type: '"start" | "end"'
  },
  {
    name: 'disabled',
    description: 'Disables the switch and prevents interaction',
    required: false,
    type: 'boolean',
    default: 'false'
  },
  {
    name: 'icon',
    description: 'Optional icon rendered alongside the label. Strings starting with `lucide-` are rendered via the shared Lucide Tailwind utility; component values are rendered with `<component :is>`.',
    required: false,
    type: 'string | Component'
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
    description: '',
    required: false,
    type: 'boolean',
    default: 'false'
  }
]

  const slotsData = [
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
    type: '[value: boolean]'
  }
]

  const exposedData = [
  {
    name: 'focus',
    description: 'Moves focus to the switch.',
    type: '(options?: FocusOptions) => void'
  }
]
```
