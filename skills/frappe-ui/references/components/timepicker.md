<!-- Upstream: https://github.com/frappe/frappe-ui/blob/b435c66b51c0b19425a2a049b43e3ef2e308a8ab/src/components/TimePicker/TimePicker.md -->

# TimePicker

An input that picks a time from a list, or takes a typed time. To pick a date
and a time together, use [DateTimePicker](datepicker.md#datetime-picker).

```vue
<script setup lang="ts">
import { ref } from 'vue'
import { TimePicker } from 'frappe-ui'

// "When should we sync?" — typical meeting scheduler time slot
const meetingTime = ref('14:30')
</script>

<template>
  <div class="flex w-72 flex-col gap-1.5">
    <span class="text-sm text-ink-gray-7">Meeting time</span>
    <TimePicker
      v-model="meetingTime"
      placeholder="Pick a time"
      side="bottom"
      align="start"
    >
      <template #prefix>
        <span class="lucide-clock size-4 text-ink-gray-5" />
      </template>
    </TimePicker>
    <span class="text-xs text-ink-gray-5">
      Selected: {{ meetingTime || '—' }}
    </span>
  </div>
</template>
```

## Examples

### Nightly backup time

`:interval="30"` lists a time every 30 minutes.

```vue
<script setup lang="ts">
import { ref } from 'vue'
import { TimePicker } from 'frappe-ui'

// Schedule a nightly cron — 24h format keeps the ops team unambiguous
const cronTime = ref('02:00')
</script>

<template>
  <div class="flex w-72 flex-col gap-1.5">
    <span class="text-sm text-ink-gray-7">Nightly backup runs at</span>
    <TimePicker
      v-model="cronTime"
      format="HH:mm"
      :interval="30"
    >
      <template #prefix>
        <span class="lucide-server-cog size-4 text-ink-gray-5" />
      </template>
    </TimePicker>
    <span class="text-xs text-ink-gray-5">UTC, every day at {{ cronTime }}</span>
  </div>
</template>
```

### Class schedule

`options` replaces the generated list with a fixed set of times, each with its
own label.

```vue
<script setup lang="ts">
import { ref } from 'vue'
import { TimePicker } from 'frappe-ui'

// A yoga studio offers only its scheduled class times — not free-form intervals
const classTime = ref('07:00')

const classOptions = [
  { value: '06:00', label: '6:00 AM — Sunrise Flow' },
  { value: '07:00', label: '7:00 AM — Vinyasa' },
  { value: '09:30', label: '9:30 AM — Gentle Hatha' },
  { value: '12:15', label: '12:15 PM — Lunch Power' },
  { value: '17:30', label: '5:30 PM — Restorative' },
  { value: '19:00', label: '7:00 PM — Candlelight Yin' },
]
</script>

<template>
  <div class="flex w-80 flex-col gap-1.5">
    <span class="text-sm text-ink-gray-7">Book a class</span>
    <TimePicker v-model="classTime" :options="classOptions">
      <template #prefix>
        <span class="lucide-dumbbell size-4 text-ink-gray-5" />
      </template>
    </TimePicker>
  </div>
</template>
```

### Shift hours

`min` and `max` limit each picker. The end time's `min` is the start time, so
a shift cannot end before it starts.

```vue
<script setup lang="ts">
import { ref } from 'vue'
import { TimePicker } from 'frappe-ui'

// Shift scheduling — limit each picker to business hours.
const shiftStart = ref('09:00')
const shiftEnd = ref('17:00')
</script>

<template>
  <div class="flex w-full max-w-md flex-col gap-3">
    <span class="text-sm-medium text-ink-gray-7">Shift hours</span>
    <div class="grid grid-cols-2 gap-3">
      <div class="flex flex-col gap-1.5">
        <span class="text-xs text-ink-gray-6">From</span>
        <TimePicker
          v-model="shiftStart"
          min="06:00"
          :max="shiftEnd"
          :interval="15"
        >
          <template #prefix>
            <span class="lucide-sunrise size-4 text-ink-gray-5" />
          </template>
        </TimePicker>
      </div>
      <div class="flex flex-col gap-1.5">
        <span class="text-xs text-ink-gray-6">To</span>
        <TimePicker
          v-model="shiftEnd"
          :min="shiftStart"
          max="22:00"
          :interval="15"
        >
          <template #prefix>
            <span class="lucide-sunset size-4 text-ink-gray-5" />
          </template>
        </TimePicker>
      </div>
    </div>
    <span class="text-xs text-ink-gray-5">
      Working {{ shiftStart }} – {{ shiftEnd }}
    </span>
  </div>
</template>
```

## Behavior

### Value

The value is a 24-hour `'HH:mm'` string, or `'HH:mm:ss'` when seconds were
typed. `min` and `max` use the same form.

### Display format

`format` is a Dayjs format string for the text in the input. The default is
`HH:mm`. Use `format="h:mm A"` for a 12-hour clock. The value stays in 24-hour
form either way.

### Options

The list has a time every `interval` minutes, 15 by default. `options` takes
`{ value, label? }` items and replaces the generated list.

### Typing a time

People can type a time into the input by default. Set `:typeable="false"` to
allow picking from the list only. Typed text that cannot be read as a time
goes back to the last valid value.

### Suffix slot

`#suffix` replaces the chevron. It receives
`{ open, disabled, setOpen, close }`. Use `setOpen(!open)` for a custom
chevron. `close()` is the same as `setOpen(false)`.

### Label, description and error

`label` renders above the field and `description` below it. `error` renders
below the field and hides `description`. It takes a string, an array of strings
(one line each), or an `Error`, the same values as
[ErrorMessage](errormessage.md). An empty string or an empty array means no
error. `required` adds a red asterisk to the label and sets `required` on the
`<input>`.

The `#label` slot replaces the label text and the required marker, and receives
`{ required }`. A `#description` slot is not hidden by `error`. It renders
above the error.

## Accessibility

The input has `role="combobox"`, `aria-haspopup="listbox"` and
`aria-expanded`, so a screen reader announces that the field opens a list and
whether the list is open.

## API reference

```ts
const propsData = [
  {
    name: 'modelValue',
    description: 'Controlled value, canonical `HH:mm` (or `HH:mm:ss`).',
    required: false,
    type: 'string',
    default: '""'
  },
  {
    name: 'interval',
    description: 'Minute interval between generated options.',
    required: false,
    type: 'number',
    default: '15'
  },
  {
    name: 'options',
    description: 'Caller-provided option values; bypasses the generated grid.',
    required: false,
    type: '{ value: string; label?: string; }[]',
    default: '[]'
  },
  {
    name: 'side',
    description: 'Preferred popover side.',
    required: false,
    type: 'PopoverSide'
  },
  {
    name: 'align',
    description: 'Alignment of the popover along the trigger edge.',
    required: false,
    type: 'PopoverAlign'
  },
  {
    name: 'offset',
    description: 'Gap between trigger and popover in pixels.',
    required: false,
    type: 'number'
  },
  {
    name: 'placeholder',
    description: 'Placeholder text when no value is selected.',
    required: false,
    type: 'string',
    default: '"Select time"'
  },
  {
    name: 'variant',
    description: 'Visual style variant.',
    required: false,
    type: 'InputVariant',
    default: '"subtle" as Variant'
  },
  {
    name: 'size',
    description: 'Visual size of the trigger input. Forwarded to the underlying `TextInput`.',
    required: false,
    type: 'InputSize'
  },
  {
    name: 'typeable',
    description: 'Whether the trigger input accepts typed input. When `false` the user can\nstill open the popover and pick a time, but cannot type a time manually.\nDefault: `true`.',
    required: false,
    type: 'boolean',
    default: 'true'
  },
  {
    name: 'keepOpen',
    description: 'Keeps the popover open after a time is selected.',
    required: false,
    type: 'boolean'
  },
  {
    name: 'format',
    description: 'Dayjs format string used for display. Default: `HH:mm`.',
    required: false,
    type: 'string'
  },
  {
    name: 'disabled',
    description: 'Disable the time picker.',
    required: false,
    type: 'boolean',
    default: 'false'
  },
  {
    name: 'open',
    description: 'Controlled popover open state. Use with `v-model:open` for two-way binding.',
    required: false,
    type: 'boolean'
  },
  {
    name: 'openOnFocus',
    description: 'Opens the popover when the input receives focus. Default: false.',
    required: false,
    type: 'boolean',
    default: 'false'
  },
  {
    name: 'openOnClick',
    description: 'Opens the popover when the input is clicked. Default: true.',
    required: false,
    type: 'boolean',
    default: 'true'
  },
  {
    name: 'min',
    description: 'Minimum selectable time as `HH:mm[:ss]`.',
    required: false,
    type: 'string'
  },
  {
    name: 'max',
    description: 'Maximum selectable time as `HH:mm[:ss]`.',
    required: false,
    type: 'string'
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
  },
  {
    name: 'prefix',
    description: 'Rendered inside the trigger input, before the typed value.',
    type: 'any'
  },
  {
    name: 'suffix',
    description: 'Rendered inside the trigger input, after the typed value. Defaults to a\nchevron-down that toggles the popover.',
    type: '{ open: boolean; disabled: boolean; setOpen: (value: boolean) => void; close: () => void; }'
  }
]

  const emitsData = [
  {
    name: 'update:open',
    description: 'Fired when the open state changes.',
    type: '[value: boolean]'
  },
  {
    name: 'change',
    description: 'Fired after the value is committed.',
    type: '[value: string]'
  },
  {
    name: 'update:modelValue',
    description: 'Fired when the model value changes.',
    type: '[value: string]'
  }
]

  const exposedData = [
  {
    name: 'open',
    description: 'Opens the panel. A disabled picker stays closed.',
    type: '() => void'
  },
  {
    name: 'close',
    description: 'Closes the panel.',
    type: '() => void'
  },
  {
    name: 'focus',
    description: 'Moves focus to the input.',
    type: '(options?: FocusOptions) => void'
  }
]
```
