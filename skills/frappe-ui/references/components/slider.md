<!-- Upstream: https://github.com/frappe/frappe-ui/blob/b435c66b51c0b19425a2a049b43e3ef2e308a8ab/src/components/Slider/Slider.md -->

# Slider

A track with one or more handles for picking a number, or a range of numbers,
between a minimum and a maximum.



## Examples

### Price range filter

A `v-model` with two numbers draws two handles, one for each end of the range.

```vue
<script setup lang="ts">
import { ref } from 'vue'
import { Badge, Slider } from 'frappe-ui'

const value = ref([20, 80])
</script>

<template>
  <div class="flex flex-col gap-3 w-full max-w-md">
    <Slider v-model="value" label="Price range" />
    <Badge class="w-fit !rounded-1">{{ value.join(' – ') }}</Badge>
  </div>
</template>
```

### Exposure adjustment

With a negative `min`, the filled part of the track starts at zero, so values
below and above zero look different.

```vue
<script setup lang="ts">
import { ref } from 'vue'
import { Slider } from 'frappe-ui'

const exposure = ref([-20])
</script>

<template>
  <div class="flex w-full max-w-md flex-col gap-2">
    <Slider v-model="exposure" label="Exposure" :min="-100" :max="100" />
    <span class="text-sm text-ink-gray-5 tabular-nums">
      {{ exposure[0] > 0 ? '+' : '' }}{{ exposure[0] }}
    </span>
  </div>
</template>
```

### Save when the handle is released

`value-commit` fires once when a drag ends. Save there instead of on every
change of `v-model`.

```vue
<script setup lang="ts">
import { ref } from 'vue'
import { Slider } from 'frappe-ui'

const brightness = ref([60])
const saved = ref<number | null>(null)
const saves = ref(0)

// Runs once when the drag ends, not on every step.
function save([value]: number[]) {
  saved.value = value
  saves.value++
}
</script>

<template>
  <div class="flex w-full max-w-md flex-col gap-2">
    <Slider
      v-model="brightness"
      label="Screen brightness"
      @value-commit="save"
    />
    <p class="text-p-sm text-ink-gray-5">
      Showing {{ brightness[0] }}.
      <template v-if="saved !== null">
        Saved {{ saved }} ({{ saves }} {{ saves === 1 ? 'save' : 'saves' }}).
      </template>
      <template v-else>Not saved yet.</template>
    </p>
  </div>
</template>
```

## Behavior

### Value

The value is always an array of numbers: `[25]` for one handle, `[20, 80]` for
a range. Each number draws a handle. An empty or missing value shows one handle
at `min`, without updating the model.

`min` defaults to `0`, `max` to `100` and `step` to `1`.

### Negative values

When `min` is below zero and `max` is above it, the filled part of the track
runs from zero to the handle, in either direction. With a range, it runs
between the two handles as usual.

### Committed values

`update:modelValue` fires on every step of a drag. `value-commit` fires with
the final value once the drag ends. Each key press also changes the value in
one step, so it fires both events.

### Width

The slider fills the width of its container. A width class such as `w-64`
replaces that width.

### Label, description and error

`label` renders above the track and `description` below it. `error` renders
below the track and hides `description`. It takes a string, an array of
strings (one line each), or an `Error`, the same values as
[ErrorMessage](errormessage.md). An empty string or an empty array means no
error. `required` adds a red asterisk to the label.

The `#label` slot replaces the label text and the required marker, and receives
`{ required }`. A `#description` slot is not hidden by `error`. It renders
above the error.

### Attributes

`class` and `style` go on the outer element: the wrapper when a label,
description or error shows, and the slider otherwise. `aria-*` attributes go
on every handle. Every other attribute and listener goes on the slider's root
element.

## Accessibility

Each handle has `role="slider"` and is in the tab order.

| Keys                                 | Action                          |
| ------------------------------------ | ------------------------------- |
| `ArrowLeft` `ArrowDown` / `ArrowRight` `ArrowUp`                    | Decrease / increase by `step`   |
| `Shift` + arrow, `PageDown` / `PageUp` | Decrease / increase by 10 steps |
| `Home` / `End`                       | Set the first handle to `min` / the last handle to `max` |

### Names

The name from `label` lands on each handle. A caller's `aria-labelledby` or
`aria-label` replaces it, so the visible label and the announced name can
differ. Use one or the other.

In a range, each handle adds its part to the name: "Price minimum" and "Price
maximum", or "Stops value 2 of 3" with more than two handles. A range with no
name uses "Minimum" and "Maximum".

### Required

The slider role does not allow `aria-required`, so `required` is announced
from the label instead, as hidden "(required)" text next to the asterisk. The
asterisk itself is hidden from screen readers.

This works only while the name comes from the rendered label. In these cases
you have to show or word the required state yourself:

- A caller `aria-label` or `aria-labelledby` replaces the label as the name,
  so the handle is announced without "(required)".
- A `#label` slot replaces the whole label, asterisk included. The slot
  receives `{ required }` for this.

### Other ARIA attributes

Any other `aria-*` you set is copied to every handle, so a range gets one
value for all of them. `aria-valuetext` on a range is announced the same at
both ends.

`aria-hidden` stays on the root instead, because it hides a whole subtree. It
also makes the slider `inert`. Without that, the handles would stay in the tab
order while screen readers could not see them.

## API reference

```ts
const propsData = [
  {
    name: 'step',
    description: 'Step interval between slider values.',
    required: false,
    type: 'number',
    default: '1'
  },
  {
    name: 'max',
    description: 'Maximum allowed slider value.',
    required: false,
    type: 'number',
    default: '100'
  },
  {
    name: 'min',
    description: 'Minimum allowed slider value. Negative values enable bidirectional fill from zero.',
    required: false,
    type: 'number',
    default: '0'
  },
  {
    name: 'size',
    description: 'Visual size of the slider.',
    required: false,
    type: 'RangeSize',
    default: '"sm"'
  },
  {
    name: 'disabled',
    description: 'Disables the slider.',
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
    description: 'The current slider value (controlled).',
    required: false,
    type: 'SliderValue'
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
    type: '[value: SliderValue | undefined]'
  },
  {
    name: 'value-commit',
    description: 'Fired once when the user finishes dragging the slider.',
    type: '[value: SliderValue]'
  }
]

  const exposedData = [
  {
    name: 'focus',
    description: 'Moves focus to the thumb. A range slider focuses its first thumb.',
    type: '(options?: FocusOptions) => void'
  }
]
```
