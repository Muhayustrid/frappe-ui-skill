<!-- Upstream: https://github.com/frappe/frappe-ui/blob/b435c66b51c0b19425a2a049b43e3ef2e308a8ab/src/components/Checkbox/Checkbox.md -->

# Checkbox

A box that turns one option on or off. For a dropdown that picks several
options, use [MultiSelect](multiselect.md), which draws the checkbox rows for
you.



## Examples

### Select all

A parent checkbox with `indeterminate` shows that only some of its children
are checked. Clicking it checks all of them, and clicking again clears them.
`padded` gives each row a clickable surface.

```vue
<script setup lang="ts">
import { ref } from 'vue'
import { Checkbox } from 'frappe-ui'

const items = ref([
  { label: 'Notifications', checked: true },
  { label: 'Weekly digest', checked: false },
  { label: 'Marketing emails', checked: true },
])

const allChecked = ref(false)

const indeterminate = ref(
  items.value.some((i) => i.checked) && !items.value.every((i) => i.checked),
)

function onSelectAll(val: boolean | 0 | 1 | undefined) {
  const checked = Boolean(val)
  items.value.forEach((i) => (i.checked = checked))
  allChecked.value = checked
  indeterminate.value = false
}

function onItemChange() {
  const checkedCount = items.value.filter((i) => i.checked).length
  allChecked.value = checkedCount === items.value.length
  indeterminate.value = checkedCount > 0 && checkedCount < items.value.length
}
</script>

<template>
  <div class="flex flex-col gap-1 w-48">
    <Checkbox
      :indeterminate="indeterminate"
      :model-value="allChecked"
      label="Select all"
      padded
      @update:model-value="onSelectAll"
    />
    <div class="pl-4 flex flex-col gap-1">
      <Checkbox
        v-for="item in items"
        :key="item.label"
        v-model="item.checked"
        :label="item.label"
        padded
        @update:model-value="onItemChange"
      />
    </div>
  </div>
</template>
```

### Notification settings

A `description` shows below the label, lined up with the label text.

```vue
<script setup lang="ts">
import { ref } from 'vue'
import { Checkbox } from 'frappe-ui'

const settings = ref({
  email: true,
  mentions: true,
  threads: true,
  events: false,
})
</script>

<template>
  <div class="flex w-96 flex-col gap-4">
    <Checkbox v-model="settings.email" label="Enable email notifications" />
    <Checkbox v-model="settings.mentions" label="Mentions" />
    <Checkbox
      v-model="settings.threads"
      label="Email threads on assigned document"
      description="Get notified when an email is received on any of the documents assigned to you."
    />
    <Checkbox v-model="settings.events" label="Event reminders" />
  </div>
</template>
```

### Print settings

Checkboxes in a row that wraps, under a section title.

```vue
<script setup lang="ts">
import { reactive } from 'vue'
import { Checkbox } from 'frappe-ui'

const settings = reactive({
  inclusiveText: false,
  paymentSchedule: false,
  taxesAsTable: true,
})
</script>

<template>
  <div class="flex flex-col gap-4 items-start w-[32rem]">
    <p class="text-lg-medium text-ink-gray-9">Print settings</p>
    <div class="flex flex-wrap gap-x-8 gap-y-2">
      <Checkbox v-model="settings.inclusiveText" label="Show inclusive text in print" />
      <Checkbox
        v-model="settings.paymentSchedule"
        label="Show payment schedule in print"
      />
      <Checkbox v-model="settings.taxesAsTable" label="Show taxes as table in print" />
    </div>
  </div>
</template>
```

### Setting with several values

A column of checkboxes as the value side of a label and value row. One option
is `disabled`, so it always stays checked.

```vue
<script setup lang="ts">
import { reactive } from 'vue'
import { Checkbox } from 'frappe-ui'

const ticketTypes = reactive({
  primary: true,
  promotions: true,
  social: true,
  updates: true,
})
</script>

<template>
  <div class="flex gap-3 items-start border-b border-outline-gray-1 py-3 w-[28rem]">
    <p class="text-base text-ink-gray-7 pt-1.5 w-40 shrink-0">Default ticket type</p>
    <div class="flex flex-col gap-1 items-start">
      <Checkbox v-model="ticketTypes.primary" label="Primary" disabled />
      <Checkbox v-model="ticketTypes.promotions" label="Promotions" />
      <Checkbox v-model="ticketTypes.social" label="Social" />
      <Checkbox v-model="ticketTypes.updates" label="Updates" />
    </div>
  </div>
</template>
```

## Behavior

### Value

The value is a `boolean`. `1` and `0` also work, and stay supported in v1.

### Indeterminate

`indeterminate` draws the mixed state. It only changes how the box looks, and
you must set it with the prop: the browser does not read an `indeterminate`
attribute from markup.

### Padded

`padded` wraps the box and the label in a clickable surface with hover, active
and focus states, for selection lists and menu items. The box stays on the
leading side.

### Label, description and error

`label` renders beside the box and `description` below it, lined up with the
label text. `error` renders below the label and hides `description`. It takes a
string, an array of strings (one line each), or an `Error`, the same values as
[ErrorMessage](errormessage.md). An empty string or an empty array means no
error. `required` adds a red asterisk to the label and sets `required` on the
`<input>`.

The `#label` slot replaces the label text and the required marker, and receives
`{ required }`. A `#description` slot is not hidden by `error`. It renders
above the error.

### Attributes

`class` and `style` go on the outer element, which holds the box, the label,
the description and the error. Every other attribute and listener goes on the
`<input type="checkbox">`, including `name`, `aria-*` and `@change`.

## Accessibility

`Checkbox` renders a native `<input type="checkbox">`, so `Space` toggles it.
`required` sets `aria-required`. While `error` is set, the input gets
`aria-invalid` and an `aria-errormessage` that points to the error text.

## API reference

```ts
const propsData = [
  {
    name: 'size',
    description: 'Controls the size of the checkbox',
    required: false,
    type: 'ToggleSize',
    default: '"sm"'
  },
  {
    name: 'padded',
    description: 'Wraps the control and label in a clickable surface with hover, active and focus states — useful for selection lists and menu items. The control always stays on the leading side.',
    required: false,
    type: 'boolean',
    default: 'false'
  },
  {
    name: 'disabled',
    description: 'Disables the checkbox interaction',
    required: false,
    type: 'boolean'
  },
  {
    name: 'indeterminate',
    description: 'Renders the mixed "—" state (e.g. a select-all that\'s partially selected).\nPurely visual — the native `indeterminate` DOM property is not reflected as\nan attribute, so it must be set via this prop, not markup.',
    required: false,
    type: 'boolean',
    default: 'false'
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
    type: 'boolean | 0 | 1'
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
    type: '[value: boolean | 0 | 1 | undefined]'
  }
]

  const exposedData = [
  {
    name: 'focus',
    description: 'Moves focus to the checkbox.',
    type: '(options?: FocusOptions) => void'
  }
]
```
