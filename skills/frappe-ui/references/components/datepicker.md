<!-- Upstream: https://github.com/frappe/frappe-ui/blob/b435c66b51c0b19425a2a049b43e3ef2e308a8ab/src/components/DatePicker/DatePicker.md -->

# DatePicker

Three input fields that open a calendar: `DatePicker` picks a date,
`DateTimePicker` picks a date and a time, and `DateRangePicker` picks a start
and an end date. To pick only a time, use [TimePicker](timepicker.md).



## Examples

### Booking forms {#date-picker}

`DatePicker` with `min` and `max` for flights and birthdays,
`isDateUnavailable` to block weekends and full days, `#actions` for shortcuts
such as "Tomorrow", and `#trigger` for an "Add due date" button on a task.

```vue
<script setup lang="ts">
import { ref } from 'vue'
import { DatePicker, Button } from 'frappe-ui'
import { dayjs } from '../../../utils/dayjs'
import type { Dayjs } from 'dayjs/esm'

// 1. Flight booking — can't fly into the past
const departure = ref('')
const today = dayjs().format('YYYY-MM-DD')
const oneYearOut = dayjs().add(1, 'year').format('YYYY-MM-DD')

// 2. Date of birth — can't be born in the future
const dob = ref('1995-08-14')
const longAgo = dayjs().subtract(120, 'year').format('YYYY-MM-DD')

// 3. Appointment — weekdays only, next 30 days, with quick actions and
//    a few slots already taken.
const appointment = ref('')
const inThirtyDays = dayjs().add(30, 'day').format('YYYY-MM-DD')
const fullyBooked = new Set([
  dayjs().add(2, 'day').format('YYYY-MM-DD'),
  dayjs().add(5, 'day').format('YYYY-MM-DD'),
  dayjs().add(11, 'day').format('YYYY-MM-DD'),
])
function isUnavailable(date: Dayjs) {
  const day = date.day()
  if (day === 0 || day === 6) return true
  return fullyBooked.has(date.format('YYYY-MM-DD'))
}
function nextMonday(from: Dayjs = dayjs()) {
  const diff = (8 - from.day()) % 7 || 7
  return from.add(diff, 'day')
}

// 4. Fun — pick a Stardate for a time-travel destination
const stardate = ref('')
const stardateFloor = dayjs('1800-01-01').format('YYYY-MM-DD')
const stardateCeil = dayjs('2300-12-31').format('YYYY-MM-DD')

// 5. Custom trigger — "Add due date" button on a task card
const dueDate = ref('')

const rowCls =
  'w-full rounded-4 px-2 py-1.5 text-left text-base hover:bg-surface-gray-2'
</script>

<template>
  <div class="grid w-full max-w-sm gap-6 py-10">
    <!-- 1. Flight booking -->
    <DatePicker
      v-model="departure"
      label="Departure"
      placeholder="When are you flying?"
      format="ddd, MMM D"
      :min="today"
      :max="oneYearOut"
    >
      <template #prefix>
        <span class="lucide-plane size-4 text-ink-gray-5" />
      </template>
    </DatePicker>

    <!-- 2. Date of birth -->
    <DatePicker
      v-model="dob"
      label="Date of birth"
      placeholder="MM/DD/YYYY"
      format="MMM D, YYYY"
      :min="longAgo"
      :max="today"
      :clearable="false"
    >
      <template #prefix>
        <span class="lucide-cake size-4 text-ink-gray-5" />
      </template>
    </DatePicker>

    <!-- 3. Appointment booking -->
    <DatePicker
      v-model="appointment"
      label="Book appointment"
      description="Weekdays only, next 30 days. Greyed-out slots are full."
      placeholder="Pick a slot"
      :min="today"
      :max="inThirtyDays"
      :is-date-unavailable="isUnavailable"
    >
      <template #prefix>
        <span class="lucide-stethoscope size-4 text-ink-gray-5" />
      </template>
      <template #actions="{ setDate, close }">
        <button
          type="button"
          :class="rowCls"
          @click="
            () => {
              setDate(dayjs().add(1, 'day'))
              close()
            }
          "
        >
          Tomorrow
        </button>
        <button
          type="button"
          :class="rowCls"
          @click="
            () => {
              setDate(nextMonday())
              close()
            }
          "
        >
          Next Monday
        </button>
      </template>
    </DatePicker>

    <!-- 4. Fun: Time-travel destination -->
    <DatePicker
      v-model="stardate"
      label="Time-travel destination"
      placeholder="When to?"
      format="[Stardate] YYYY.MM.DD"
      variant="outline"
      :min="stardateFloor"
      :max="stardateCeil"
    >
      <template #prefix>
        <span class="lucide-rocket size-4 text-ink-gray-5" />
      </template>
    </DatePicker>

    <!-- 5. Custom trigger — feels like a task-card affordance -->
    <div class="flex flex-col gap-1.5">
      <span class="text-sm text-ink-gray-7">Task card affordance</span>
      <DatePicker v-model="dueDate" format="MMM D">
        <template #trigger="{ setOpen, displayLabel, open }">
          <Button
            :variant="dueDate ? 'subtle' : 'ghost'"
            @click="setOpen(!open)"
          >
            <template #prefix>
              <span class="lucide-calendar-plus size-4" />
            </template>
            {{ dueDate ? `Due ${displayLabel}` : 'Add due date' }}
          </Button>
        </template>
      </DatePicker>
    </div>
  </div>
</template>
```

### Scheduling a meeting {#datetime-picker}

`DateTimePicker` for a meeting that cannot be in the past, a maintenance window
with a `min` and a `max`, and `#actions` shortcuts that set a date and time.

```vue
<script setup lang="ts">
import { ref } from 'vue'
import { DateTimePicker } from 'frappe-ui'
import { dayjs } from '../../../utils/dayjs'

// 1. Meeting — can't schedule in the past, default to the next 30-min slot
const now = dayjs()
const nextSlot = now
  .minute(now.minute() < 30 ? 30 : 60)
  .second(0)
  .format('YYYY-MM-DD HH:mm:ss')
const meeting = ref(nextSlot)
const minMeetingTime = now.format('YYYY-MM-DD HH:mm:ss')

// 2. Server maintenance window — must be at least 24h out, max 60 days
const maintenance = ref('')
const tomorrow = dayjs()
  .add(1, 'day')
  .startOf('day')
  .format('YYYY-MM-DD HH:mm:ss')
const sixtyDaysOut = dayjs()
  .add(60, 'day')
  .endOf('day')
  .format('YYYY-MM-DD HH:mm:ss')

// 3. Fun — schedule the great launch (with a "Pick a dramatic moment" preset)
const launch = ref('')

function nextFridayMidnight() {
  const today = dayjs()
  const diff = (5 - today.day() + 7) % 7 || 7
  return today.add(diff, 'day').hour(0).minute(0).second(0)
}

const rowCls =
  'w-full rounded-4 px-2 py-1.5 text-left text-base hover:bg-surface-gray-2'
</script>

<template>
  <div class="grid w-full max-w-sm gap-6 py-10">
    <!-- 1. Meeting scheduler -->
    <DateTimePicker
      v-model="meeting"
      label="Meeting time"
      placeholder="When should we meet?"
      :min="minMeetingTime"
      format="ddd, MMM D · h:mm A"
    >
      <template #prefix>
        <span class="lucide-calendar-clock size-4 text-ink-gray-5" />
      </template>
    </DateTimePicker>

    <!-- 2. Maintenance window -->
    <DateTimePicker
      v-model="maintenance"
      label="Maintenance window"
      description="Must be scheduled at least 24 hours in advance."
      placeholder="Pick a low-traffic time"
      :min="tomorrow"
      :max="sixtyDaysOut"
    >
      <template #prefix>
        <span class="lucide-server size-4 text-ink-gray-5" />
      </template>
    </DateTimePicker>

    <!-- 3. Fun: dramatic launch moment -->
    <DateTimePicker
      v-model="launch"
      label="Launch moment"
      placeholder="When do we light the candle?"
      format="MMMM D, YYYY [at] h:mm A"
      variant="outline"
    >
      <template #prefix>
        <span class="lucide-rocket size-4 text-ink-gray-5" />
      </template>
      <template #actions="{ setDate, close }">
        <button
          type="button"
          :class="rowCls"
          @click="
            () => {
              setDate(dayjs().add(1, 'day').hour(12).minute(0))
              close()
            }
          "
        >
          High noon
        </button>
        <button
          type="button"
          :class="rowCls"
          @click="
            () => {
              setDate(nextFridayMidnight())
              close()
            }
          "
        >
          Midnight Friday
        </button>
        <button
          type="button"
          :class="rowCls"
          @click="
            () => {
              setDate(dayjs().add(1, 'day').hour(6).minute(15))
              close()
            }
          "
        >
          Sunrise
        </button>
      </template>
    </DateTimePicker>
  </div>
</template>
```

### Time off, stays and reports {#date-range-picker}

`DateRangePicker` with shortcuts in `#actions`, `dual-pane` for hotel stays,
`isDateUnavailable` for a sprint on weekdays only, and a `#trigger` that splits
the range into "Depart" and "Return" fields over one calendar.

```vue
<script setup lang="ts">
import { computed, ref } from 'vue'
import { DateRangePicker, type DateRangeValue } from 'frappe-ui'
import { dayjs } from '../../../utils/dayjs'
import type { Dayjs } from 'dayjs/esm'

const today = dayjs().format('YYYY-MM-DD')
const oneYearOut = dayjs().add(1, 'year').format('YYYY-MM-DD')

// 1. PTO request — quick presets feel native to HR tools
const timeOff = ref<DateRangeValue>([])

// 2. Hotel booking — dual pane, check-in must be today or later
const stay = ref<DateRangeValue>([])

// 3. Analytics filter — preset-driven, displays compact format
const analyticsRange = ref<DateRangeValue>([
  dayjs().subtract(29, 'day').format('YYYY-MM-DD'),
  dayjs().format('YYYY-MM-DD'),
])

function longWeekend(): [Dayjs, Dayjs] {
  const fri = upcomingDay(5)
  return [fri, fri.add(2, 'day')]
}

function workWeek(): [Dayjs, Dayjs] {
  const mon = upcomingDay(1)
  return [mon, mon.add(4, 'day')]
}

function upcomingDay(weekday: number) {
  const t = dayjs()
  const diff = (weekday - t.day() + 7) % 7 || 7
  return t.add(diff, 'day')
}

function lastNDays(days: number): [Dayjs, Dayjs] {
  return [dayjs().subtract(days - 1, 'day'), dayjs()]
}

// Canonical sidebar row styling.
const rowCls =
  'w-full rounded-4 px-2 py-1.5 text-left text-base hover:bg-surface-gray-2'

// 4. Project sprint — weekdays only, span limited to ~2 weeks via `max`
const sprint = ref<DateRangeValue>([])
const sprintEnd = dayjs().add(60, 'day').format('YYYY-MM-DD')
function isWeekend(date: Dayjs) {
  const d = date.day()
  return d === 0 || d === 6
}

// 5. Flight booking — custom #trigger renders two side-by-side inputs
// that share one popover, so the calendar still visualizes the range.
const flight = ref<DateRangeValue>([])
const depart = computed(() =>
  flight.value[0] ? dayjs(flight.value[0]).format('ddd, MMM D') : '',
)
const ret = computed(() =>
  flight.value[1] ? dayjs(flight.value[1]).format('ddd, MMM D') : '',
)
</script>

<template>
  <div class="grid w-full max-w-sm gap-6 py-10">
    <!-- 1. PTO request -->
    <DateRangePicker
      v-model="timeOff"
      label="Time off"
      placeholder="Pick your dates"
      :min="today"
    >
      <template #prefix>
        <span
          class="lucide-palmtree size-4 text-ink-gray-5"
          aria-hidden="true"
        />
      </template>
      <template #actions="{ setRange, close }">
        <button
          type="button"
          :class="rowCls"
          @click="
            () => {
              setRange(longWeekend())
              close()
            }
          "
        >
          Long weekend
        </button>
        <button
          type="button"
          :class="rowCls"
          @click="
            () => {
              setRange(workWeek())
              close()
            }
          "
        >
          Next week
        </button>
      </template>
    </DateRangePicker>

    <!-- 2. Hotel booking — dual pane shines for >1 month stays -->
    <DateRangePicker
      v-model="stay"
      label="Check-in / Check-out"
      placeholder="Select your stay"
      dual-pane
      :min="today"
      :max="oneYearOut"
    >
      <template #prefix>
        <span class="lucide-hotel size-4 text-ink-gray-5" aria-hidden="true" />
      </template>
    </DateRangePicker>

    <!-- 3. Analytics date range filter -->
    <DateRangePicker
      v-model="analyticsRange"
      label="Report range"
      description="Defaults to the last 30 days."
      format="MMM D"
      :max="today"
    >
      <template #prefix>
        <span
          class="lucide-chart-line size-4 text-ink-gray-5"
          aria-hidden="true"
        />
      </template>
      <template #actions="{ fromDate, toDate, setRange, clear, close }">
        <button
          type="button"
          :class="rowCls"
          @click="
            () => {
              setRange([dayjs(), dayjs()])
              close()
            }
          "
        >
          Today
        </button>
        <button
          type="button"
          :class="rowCls"
          @click="
            () => {
              setRange(lastNDays(7))
              close()
            }
          "
        >
          Last 7 days
        </button>
        <button
          type="button"
          :class="rowCls"
          @click="
            () => {
              setRange(lastNDays(28))
              close()
            }
          "
        >
          Last 4 weeks
        </button>
        <button
          type="button"
          :class="rowCls"
          @click="
            () => {
              setRange(lastNDays(90))
              close()
            }
          "
        >
          Last 3 months
        </button>
        <button
          type="button"
          :class="rowCls"
          @click="
            () => {
              setRange(lastNDays(365))
              close()
            }
          "
        >
          Last 12 months
        </button>
        <hr class="my-1 border-outline-gray-2" />
        <button
          v-if="fromDate || toDate"
          type="button"
          :class="rowCls"
          @click="
            () => {
              clear()
              close()
            }
          "
        >
          Clear
        </button>
      </template>
    </DateRangePicker>

    <!-- 4. Sprint window — weekdays only -->
    <DateRangePicker
      v-model="sprint"
      label="Sprint window"
      description="Pick start and end. Weekends are skipped."
      :min="today"
      :max="sprintEnd"
      :is-date-unavailable="isWeekend"
    >
      <template #prefix>
        <span
          class="lucide-timer-reset size-4 text-ink-gray-5"
          aria-hidden="true"
        />
      </template>
    </DateRangePicker>

    <!-- 5. Flight booking — split trigger over one shared popover -->
    <DateRangePicker v-model="flight" dual-pane :min="today">
      <template #trigger="{ setOpen, open }">
        <div
          class="grid grid-cols-2 divide-x divide-outline-gray-2 rounded-4 border bg-surface-base text-sm transition-colors"
          :class="
            open
              ? 'border-outline-gray-4 ring-2 ring-outline-gray-2'
              : 'border-outline-gray-2 hover:border-outline-gray-3'
          "
        >
          <button
            type="button"
            class="flex items-center gap-2 rounded-l-4 px-3 py-2 text-left hover:bg-surface-gray-1"
            @click="setOpen(!open)"
          >
            <span
              class="lucide-plane-takeoff size-4 text-ink-gray-5"
              aria-hidden="true"
            />
            <div class="flex flex-col leading-tight">
              <span class="text-xs text-ink-gray-5">Depart</span>
              <span class="text-ink-gray-9">
                {{ depart || 'Add date' }}
              </span>
            </div>
          </button>
          <button
            type="button"
            class="flex items-center gap-2 rounded-r-4 px-3 py-2 text-left hover:bg-surface-gray-1"
            @click="setOpen(!open)"
          >
            <span
              class="lucide-plane-landing size-4 text-ink-gray-5"
              aria-hidden="true"
            />
            <div class="flex flex-col leading-tight">
              <span class="text-xs text-ink-gray-5">Return</span>
              <span class="text-ink-gray-9">
                {{ ret || 'Add date' }}
              </span>
            </div>
          </button>
        </div>
      </template>
    </DateRangePicker>
  </div>
</template>
```

## Behavior

### Values

| Picker            | `v-model`                                 |
| ----------------- | ----------------------------------------- |
| `DatePicker`      | `'YYYY-MM-DD'`                            |
| `DateTimePicker`  | `'YYYY-MM-DD HH:mm:ss'`                   |
| `DateRangePicker` | `[from, to]` as `'YYYY-MM-DD'`, or `[]`   |

`format` changes only the text shown in the input, not the value. Type a range
with `DateRangeValue`, exported from `frappe-ui`.

### Limits

`min` and `max` take `'YYYY-MM-DD'`. `DateTimePicker` also takes
`'YYYY-MM-DD HH:mm:ss'`. `isDateUnavailable` receives each date as a Dayjs
object and returns `true` to block it. It works together with `min` and
`max`.

### Typing a date

People can type a date into the input by default. Set `:typeable="false"` to
allow picking from the calendar only. On `DateTimePicker`, `typeable` covers
both the date and the time inputs.

With a `format`, typed numbers read in that format's day-month-year order.
Under `DD-MM-YYYY`, `3-11-2026`, `3/11/26` and `03.11.2026` all mean
3 November 2026. A `DateTimePicker` also takes the date without its seconds or
without a time, and a 12-hour time in either AM/PM case. ISO text (`2026-11-03`) and text with a month name
(`Nov 3, 2026`) read as they do with no `format`. A focus and blur with no edit
leaves the value as it is.

### Clearing

`clearable` is `true` by default, and an empty input clears the value. On
`DatePicker` and `DateTimePicker`, `:clearable="false"` fills an empty input
with today's date (and the current time) instead. `DateRangePicker` does not
read `clearable`: emptying its input always clears the range.

### Closing

The popover closes after a date is picked. Set `keep-open` to keep it open.
`DateTimePicker` stays open after a date click, because the time is still
missing, and moves focus to the time input.

### Label, description and error

`label` renders above the field and `description` below it. `error` renders
below the field and hides `description`. It takes a string, an array of strings
(one line each), or an `Error`, the same values as
[ErrorMessage](errormessage.md). An empty string or an empty array means no
error. `required` adds a red asterisk to the label and sets `required` on the
`<input>`.

The pickers have no `#label` or `#description` slot. A `#trigger` slot
replaces the input, and the label, description and error go with it.

### Slots

`#trigger`, `#prefix` and `#suffix` receive
`{ open, disabled, setOpen, close, displayLabel, inputValue }`. `close()` is
the same as `setOpen(false)`.

`#actions` renders a column of shortcuts beside the calendar. It receives
`open`, `disabled`, `setOpen`, `close` and `clear`, plus:

| Picker            | Extra `#actions` props                          |
| ----------------- | ----------------------------------------------- |
| `DatePicker`      | `selected`, `setDate`                           |
| `DateTimePicker`  | `selected`, `time`, `setDate`                   |
| `DateRangePicker` | `fromDate`, `toDate`, `setDate`, `setRange`     |

## Accessibility

The input has `role="combobox"`, `aria-haspopup="dialog"` and
`aria-expanded`, so a screen reader announces that the field opens a panel and
whether the panel is open.

## API reference

```ts
const datePickerProps = [
  {
    name: 'side',
    description: 'Preferred popover side relative to the trigger.',
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
    description: 'Gap between the trigger and popover content in pixels.',
    required: false,
    type: 'number'
  },
  {
    name: 'format',
    description: 'Display format used for the input text.',
    required: false,
    type: 'string'
  },
  {
    name: 'size',
    description: 'Size of the trigger input.',
    required: false,
    type: 'InputSize'
  },
  {
    name: 'variant',
    description: 'Visual style variant passed through to the input.',
    required: false,
    type: 'InputVariant',
    default: '"subtle"'
  },
  {
    name: 'placeholder',
    description: 'Placeholder text shown when no value is selected.',
    required: false,
    type: 'string',
    default: '"Select date"'
  },
  {
    name: 'open',
    description: 'Controls popover open state (for controlled usage).',
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
    name: 'typeable',
    description: 'Whether the trigger input accepts typed input. When `false` the user can\nstill open the popover and pick a date, but cannot type a date manually.\nDefault: `true`.',
    required: false,
    type: 'boolean',
    default: 'true'
  },
  {
    name: 'disabled',
    description: 'Disables the trigger input and calendar interactions.',
    required: false,
    type: 'boolean',
    default: 'false'
  },
  {
    name: 'clearable',
    description: 'Shows clear and quick-action controls when enabled.',
    required: false,
    type: 'boolean',
    default: 'true'
  },
  {
    name: 'keepOpen',
    description: 'Keeps the popover open after a date is selected. Default: false.',
    required: false,
    type: 'boolean'
  },
  {
    name: 'min',
    description: 'Earliest selectable date. Accepts `YYYY-MM-DD` (or, for `DateTimePicker`,\n`YYYY-MM-DD HH:mm:ss` for second-level granularity).',
    required: false,
    type: 'string'
  },
  {
    name: 'max',
    description: 'Latest selectable date. Accepts `YYYY-MM-DD` (or, for `DateTimePicker`,\n`YYYY-MM-DD HH:mm:ss` for second-level granularity).',
    required: false,
    type: 'string'
  },
  {
    name: 'isDateUnavailable',
    description: 'Return true to prevent a date from being selected. Combined with `min`/`max`.',
    required: false,
    type: '((date: Dayjs) => boolean)'
  },
  {
    name: 'modelValue',
    description: 'Controlled value for the picker.',
    required: false,
    type: 'string',
    default: '""'
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

  const datePickerSlots = [
  {
    name: 'trigger',
    description: 'Custom trigger renderer for the picker.',
    type: 'DatePickerTriggerSlotProps'
  },
  {
    name: 'prefix',
    description: 'Content rendered before the trigger input value.',
    type: 'DatePickerTriggerSlotProps'
  },
  {
    name: 'suffix',
    description: 'Content rendered after the trigger input value.',
    type: 'DatePickerTriggerSlotProps'
  },
  {
    name: 'actions',
    description: 'Sidebar rendered to the left of the calendar. Use for date shortcuts\n("Today", "Tomorrow", "Last 7 days") and other preset actions. When\nomitted, the popover shows the calendar only.',
    type: 'DatePickerActionsSlotProps'
  }
]

  const datePickerEmits = [
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

  const datePickerExposed = [
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
    description: 'Moves focus to the input. With a custom `#trigger`, it focuses the first\nfocusable element inside it.',
    type: '(options?: FocusOptions) => void'
  }
]

  const dateRangePickerProps = [
  {
    name: 'side',
    description: 'Preferred popover side relative to the trigger.',
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
    description: 'Gap between the trigger and popover content in pixels.',
    required: false,
    type: 'number'
  },
  {
    name: 'format',
    description: 'Display format used for the input text.',
    required: false,
    type: 'string'
  },
  {
    name: 'size',
    description: 'Size of the trigger input.',
    required: false,
    type: 'InputSize'
  },
  {
    name: 'variant',
    description: 'Visual style variant passed through to the input.',
    required: false,
    type: 'InputVariant',
    default: '"subtle"'
  },
  {
    name: 'placeholder',
    description: 'Placeholder text shown when no value is selected.',
    required: false,
    type: 'string',
    default: '"Select range"'
  },
  {
    name: 'open',
    description: 'Controls popover open state (for controlled usage).',
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
    name: 'typeable',
    description: 'Whether the trigger input accepts typed input. When `false` the user can\nstill open the popover and pick a date, but cannot type a date manually.\nDefault: `true`.',
    required: false,
    type: 'boolean',
    default: 'true'
  },
  {
    name: 'disabled',
    description: 'Disables the trigger input and calendar interactions.',
    required: false,
    type: 'boolean',
    default: 'false'
  },
  {
    name: 'clearable',
    description: 'Shows clear and quick-action controls when enabled.',
    required: false,
    type: 'boolean',
    default: 'true'
  },
  {
    name: 'keepOpen',
    description: 'Keeps the popover open after a date is selected. Default: false.',
    required: false,
    type: 'boolean'
  },
  {
    name: 'min',
    description: 'Earliest selectable date. Accepts `YYYY-MM-DD` (or, for `DateTimePicker`,\n`YYYY-MM-DD HH:mm:ss` for second-level granularity).',
    required: false,
    type: 'string'
  },
  {
    name: 'max',
    description: 'Latest selectable date. Accepts `YYYY-MM-DD` (or, for `DateTimePicker`,\n`YYYY-MM-DD HH:mm:ss` for second-level granularity).',
    required: false,
    type: 'string'
  },
  {
    name: 'isDateUnavailable',
    description: 'Return true to prevent a date from being selected. Combined with `min`/`max`.',
    required: false,
    type: '((date: Dayjs) => boolean)'
  },
  {
    name: 'modelValue',
    description: 'Controlled range value as `[from, to]` in `YYYY-MM-DD` format, or `[]` for\nno selection. `DateRangeValue` types both sides of `v-model` (INP-Q12): the\nprop was `string[]`, which let a one-element array in and made a round-trip\nthrough the model fail to type-check.',
    required: false,
    type: 'DateRangeValue',
    default: '[]'
  },
  {
    name: 'dualPane',
    description: 'Render two calendar panels side by side (current month + next month).',
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
  }
]

  const dateRangePickerSlots = [
  {
    name: 'trigger',
    description: '',
    type: 'DatePickerTriggerSlotProps'
  },
  {
    name: 'prefix',
    description: '',
    type: 'DatePickerTriggerSlotProps'
  },
  {
    name: 'suffix',
    description: '',
    type: 'DatePickerTriggerSlotProps'
  },
  {
    name: 'actions',
    description: 'Sidebar rendered to the left of the calendar. Use for range shortcuts\n("Last 7 days", "Last 12 months") and other preset actions. When\nomitted, the popover shows the calendar only.',
    type: 'DateRangePickerActionsSlotProps'
  }
]

  const dateRangePickerEmits = [
  {
    name: 'update:open',
    description: 'Fired when the open state changes.',
    type: '[value: boolean]'
  },
  {
    name: 'change',
    description: 'Fired after the value is committed.',
    type: '[value: DateRangeValue]'
  },
  {
    name: 'update:modelValue',
    description: 'Fired when the model value changes.',
    type: '[value: DateRangeValue]'
  }
]

  const dateRangePickerExposed = [
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
    description: 'Moves focus to the input. With a custom `#trigger`, it focuses the first\nfocusable element inside it.',
    type: '(options?: FocusOptions) => void'
  }
]

  const dateTimePickerProps = [
  {
    name: 'side',
    description: 'Preferred popover side relative to the trigger.',
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
    description: 'Gap between the trigger and popover content in pixels.',
    required: false,
    type: 'number'
  },
  {
    name: 'format',
    description: 'Display format used for the input text.',
    required: false,
    type: 'string'
  },
  {
    name: 'size',
    description: 'Size of the trigger input.',
    required: false,
    type: 'InputSize'
  },
  {
    name: 'variant',
    description: 'Visual style variant passed through to the input.',
    required: false,
    type: 'InputVariant',
    default: '"subtle"'
  },
  {
    name: 'placeholder',
    description: 'Placeholder text shown when no value is selected.',
    required: false,
    type: 'string',
    default: '"Select date & time"'
  },
  {
    name: 'open',
    description: 'Controls popover open state (for controlled usage).',
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
    name: 'typeable',
    description: 'Whether the trigger input accepts typed input. When `false` the user can\nstill open the popover and pick a date, but cannot type a date manually.\nDefault: `true`.',
    required: false,
    type: 'boolean',
    default: 'true'
  },
  {
    name: 'disabled',
    description: 'Disables the trigger input and calendar interactions.',
    required: false,
    type: 'boolean',
    default: 'false'
  },
  {
    name: 'clearable',
    description: 'Shows clear and quick-action controls when enabled.',
    required: false,
    type: 'boolean',
    default: 'true'
  },
  {
    name: 'keepOpen',
    description: 'Keeps the popover open after a date is selected. Default: false.',
    required: false,
    type: 'boolean'
  },
  {
    name: 'min',
    description: 'Earliest selectable date. Accepts `YYYY-MM-DD` (or, for `DateTimePicker`,\n`YYYY-MM-DD HH:mm:ss` for second-level granularity).',
    required: false,
    type: 'string'
  },
  {
    name: 'max',
    description: 'Latest selectable date. Accepts `YYYY-MM-DD` (or, for `DateTimePicker`,\n`YYYY-MM-DD HH:mm:ss` for second-level granularity).',
    required: false,
    type: 'string'
  },
  {
    name: 'isDateUnavailable',
    description: 'Return true to prevent a date from being selected. Combined with `min`/`max`.',
    required: false,
    type: '((date: Dayjs) => boolean)'
  },
  {
    name: 'modelValue',
    description: 'Controlled value for the picker.',
    required: false,
    type: 'string',
    default: '""'
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

  const dateTimePickerSlots = [
  {
    name: 'trigger',
    description: '',
    type: 'DatePickerTriggerSlotProps'
  },
  {
    name: 'prefix',
    description: '',
    type: 'DatePickerTriggerSlotProps'
  },
  {
    name: 'suffix',
    description: '',
    type: 'DatePickerTriggerSlotProps'
  },
  {
    name: 'actions',
    description: 'Sidebar rendered to the left of the calendar. Use for date-time\nshortcuts ("Now", "Tomorrow 9am") and other preset actions. When\nomitted, the popover shows the calendar and time picker only.',
    type: 'DateTimePickerActionsSlotProps'
  }
]

  const dateTimePickerEmits = [
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

  const dateTimePickerExposed = [
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
    description: 'Moves focus to the input. With a custom `#trigger`, it focuses the first\nfocusable element inside it.',
    type: '(options?: FocusOptions) => void'
  }
]
```
