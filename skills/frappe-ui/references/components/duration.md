<!-- Upstream: https://github.com/frappe/frappe-ui/blob/b435c66b51c0b19425a2a049b43e3ef2e308a8ab/src/components/Duration/Duration.md -->

# Duration

A text input for a length of time. The value is a number of seconds, and people
can type it as `1h 30m`, `1:30:00` or `5400`.



## Examples

### Logging time

Each entry is typed in whatever notation is quickest, and the saved seconds add
up into a total. `formatDuration` renders a value outside the input.

```vue
<script setup lang="ts">
import { computed, ref } from 'vue'
import { Button, Duration, formatDuration } from 'frappe-ui'

// Logging effort against a CRM task: the user types a duration in any
// notation, "Log time" appends it, and the entries roll up into a total.
const entries = ref<{ label: string; seconds: number }[]>([
  { label: 'Discovery call', seconds: 2700 },
  { label: 'Proposal draft', seconds: 5445 },
])

const draft = ref<number | null>(null)

const total = computed(() =>
  entries.value.reduce((sum, entry) => sum + entry.seconds, 0),
)

function logTime() {
  if (!draft.value) return
  entries.value.push({ label: 'Follow-up', seconds: draft.value })
  draft.value = null
}
</script>

<template>
  <div class="flex w-80 flex-col gap-4">
    <div class="flex flex-col gap-2">
      <div
        v-for="(entry, index) in entries"
        :key="index"
        class="flex items-center justify-between text-base text-ink-gray-7"
      >
        <span>{{ entry.label }}</span>
        <span class="text-ink-gray-9">{{ formatDuration(entry.seconds) }}</span>
      </div>
    </div>

    <div class="flex items-end gap-2">
      <Duration v-model="draft" label="Log time" placeholder="e.g. 45m" />
      <Button label="Log time" @click="logTime" />
    </div>

    <div
      class="flex items-center justify-between border-t border-outline-gray-1 pt-3 text-base font-medium text-ink-gray-9"
    >
      <span>Total logged</span>
      <span>{{ formatDuration(total, 'long') }}</span>
    </div>
  </div>
</template>
```

### Service targets

`format="long"` writes the saved value out in words, which reads better in a
settings form.

```vue
<script setup lang="ts">
import { ref } from 'vue'
import { Duration } from 'frappe-ui'

// Service targets for a support team. `format="long"` spells the saved value
// out in words, which reads better in a settings form than `4h`.
const firstResponse = ref<number | null>(4 * 3600)
const resolution = ref<number | null>(24 * 3600)
</script>

<template>
  <div class="flex w-full max-w-md flex-col divide-y divide-outline-gray-1">
    <div class="flex items-center justify-between gap-6 py-3">
      <div class="flex flex-col gap-0.5">
        <span class="text-base font-medium text-ink-gray-8"
          >First response</span
        >
        <span class="text-p-sm text-ink-gray-5"
          >Reply to a new ticket within</span
        >
      </div>
      <Duration v-model="firstResponse" format="long" class="w-44" />
    </div>
    <div class="flex items-center justify-between gap-6 py-3">
      <div class="flex flex-col gap-0.5">
        <span class="text-base font-medium text-ink-gray-8">Resolution</span>
        <span class="text-p-sm text-ink-gray-5">Close the ticket within</span>
      </div>
      <Duration v-model="resolution" format="long" class="w-44" />
    </div>
  </div>
</template>
```

## Behavior

### What people can type

The input accepts several notations. Case does not matter, and units can come
in any order.

| Notation     | Example                                | Seconds            |
| ------------ | -------------------------------------- | ------------------ |
| Short units  | `1h 30m 45s`, `1h30m45s`, `45s 1h 30m` | `5445`             |
| Long units   | `1 hour 30 minutes`, `2hrs 15min`      | `5400`, `8100`     |
| Days         | `1d 2h`, `2 days`                      | `93600`, `172800`  |
| Colon        | `1:30:45`, `1:30`, `:45`               | `5445`, `90`, `45` |
| Bare integer | `90`                                   | `90`               |

The typed text is read when the input loses focus or on `Enter`. Text that
cannot be read keeps the input open with an error, and the saved value stays as
it was. `Escape` drops the edit. An empty input saves `null`.

### Display format

When the input is not focused, it shows the saved value in the `format` you
pass. `format` is a preset name or a template.

The presets leave out parts that are zero, and `long` adds plurals:

| `format`            | `5445`                         | `90`                  |
| ------------------- | ------------------------------ | --------------------- |
| `short` _(default)_ | `1h 30m 45s`                   | `1m 30s`              |
| `long`              | `1 hour 30 minutes 45 seconds` | `1 minute 30 seconds` |
| `colon`             | `1:30:45`                      | `1:30`                |

Any other value is a template, and every part in it is shown, zero or not.
`h`, `m` and `s` stand for hours, minutes and seconds. Double a letter (`hh`)
to pad it to two digits. Put text in single quotes so its letters are not read
as units.

| `format`         | `7323`     |
| ---------------- | ---------- |
| `h'h' m'm' s's'` | `2h 2m 3s` |
| `hh:mm:ss`       | `02:02:03` |
| `h':'mm`         | `2:02`     |

`m` is the minutes left over after the hours (`2`), not the total minutes
(`122`). A template without `h` drops the hours from the output.

While the input has focus, it always shows the short notation (`2h 2m 3s`), so
the text can be edited and read back the same way whatever the format.

### Units

`largestUnit` and `smallestUnit` set which units the `short` and `long` presets
show. The edit text uses the same units. `colon` and templates name their own
units, so these props do not change them.

| Props                    | `93784`       |
| ------------------------ | ------------- |
| _(default)_              | `26h 3m 4s`   |
| `largest-unit="day"`     | `1d 2h 3m 4s` |
| `smallest-unit="minute"` | `26h 3m`      |

With `smallest-unit="minute"`, the value shows rounded to the nearest minute, so
`3599` shows `1h`. A typed value is saved rounded. A saved value that is not
edited keeps its seconds.

A minute field also reads a bare number as minutes and `a:b` as hours and
minutes, so `15` saves `900` and `1:30` saves `5400`. `1:30:45` and text with
units, such as `45s`, read the same as in a seconds field.

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

```vue
<Duration v-model="seconds">
  <template #label="{ required }">
    Time spent <Badge v-if="required" label="Required" />
  </template>
  <template #description>Type 1h 30m, or 01:30:00.</template>
</Duration>
```

## Accessibility

| Keys         | Action                                     |
| ------------ | ------------------------------------------ |
| `Enter`      | Save the typed value                       |
| `Tab` / blur | Save the typed value                       |
| `Escape`     | Drop the edit and show the saved value     |

## API reference

```ts
const propsData = [
  {
    name: 'modelValue',
    description: 'The duration value in seconds (two-way via `v-model`).',
    required: false,
    type: 'number | null',
    default: 'null'
  },
  {
    name: 'placeholder',
    description: 'Placeholder shown when the input is empty. Defaults to `1h 30m` when\n`smallestUnit` is `minute`, and `1h 30m 45s` otherwise.',
    required: false,
    type: 'string'
  },
  {
    name: 'format',
    description: 'How the saved value is rendered when not focused: a named preset\n(`short` | `long` | `colon`) or a token template (e.g. `h\'h\' m\'m\' s\'s\'`,\n`hh:mm:ss`). Defaults to `short`.',
    required: false,
    type: 'DurationFormat',
    default: '"short"'
  },
  {
    name: 'largestUnit',
    description: 'The biggest unit of the `short` and `long` presets and of the edit text.\n`day` shows 93784 as `1d 2h 3m 4s`. Defaults to `hour` (`26h 3m 4s`).',
    required: false,
    type: 'DurationLargestUnit',
    default: '"hour"'
  },
  {
    name: 'smallestUnit',
    description: 'The smallest unit of the `short` and `long` presets and of the edit text.\n`minute` rounds to the nearest minute, and a typed value is saved rounded.\nIn a minute field, a bare number is minutes and `a:b` is hours and\nminutes: `15` is 15 minutes and `1:30` is 1h 30m. Defaults to `second`.',
    required: false,
    type: 'DurationSmallestUnit',
    default: '"second"'
  },
  {
    name: 'size',
    description: 'Visual size of the input. Forwarded to the underlying `TextInput`.',
    required: false,
    type: 'InputSize'
  },
  {
    name: 'variant',
    description: 'Style variant of the input. Forwarded to the underlying `TextInput`.',
    required: false,
    type: 'InputVariant'
  },
  {
    name: 'disabled',
    description: 'Disables the input when true.',
    required: false,
    type: 'boolean'
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
  }
]

  const emitsData = [
  {
    name: 'update:modelValue',
    description: 'Fired when the model value changes.',
    type: '[value: number | null]'
  }
]

  const exposedData = [
  {
    name: 'focus',
    description: 'Moves focus to the input.',
    type: '(options?: FocusOptions) => void'
  }
]
```
