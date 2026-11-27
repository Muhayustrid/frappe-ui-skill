<!-- Upstream: https://github.com/frappe/frappe-ui/blob/b435c66b51c0b19425a2a049b43e3ef2e308a8ab/src/components/Rating/Rating.md -->

# Rating

A row of stars for picking a score, such as 4 out of 5.



## Examples

### Product review

A required rating with a `description` and an `error`. Submit the form without
a rating to see the error.

```vue
<script setup lang="ts">
import { computed, ref } from 'vue'
import { Button, Rating, Textarea } from 'frappe-ui'

const rating = ref(0)
const review = ref('')
const submitted = ref(false)

const error = computed(() =>
  submitted.value && rating.value === 0
    ? 'Pick a rating to post your review.'
    : '',
)
</script>

<template>
  <form
    class="flex w-full max-w-sm flex-col gap-4"
    @submit.prevent="submitted = true"
  >
    <Rating
      v-model="rating"
      label="Overall rating"
      description="Click a selected star again to clear it."
      size="md"
      required
      :error="error"
    />
    <Textarea
      v-model="review"
      label="Your review"
      placeholder="What did you like or dislike?"
    />
    <Button variant="solid" type="submit" class="self-start">
      Post review
    </Button>
  </form>
</template>
```

### Score with half stars

`step="0.5"` lets people pick half a star. Click the left half of a star for
the half value.

```vue
<script setup lang="ts">
import { ref } from 'vue'
import { Rating } from 'frappe-ui'

const score = ref(3.5)
</script>

<template>
  <div class="flex items-end gap-3">
    <Rating v-model="score" :step="0.5" size="lg" label="Your score" />
    <span class="flex h-6 items-center text-base text-ink-gray-7 tabular-nums">
      {{ score.toFixed(1) }} / 5
    </span>
  </div>
</template>
```

### Spice level

`icon` replaces the star with any class icon, here `lucide-flame`. `max`
sets the number of icons.

```vue
<script setup lang="ts">
import { ref } from 'vue'
import { Rating } from 'frappe-ui'

const spice = ref(2)
const levels = ['Not rated', 'Mild', 'Medium', 'Hot']
</script>

<template>
  <div class="flex items-end gap-3">
    <Rating
      v-model="spice"
      icon="lucide-flame"
      :max="3"
      size="md"
      label="Spice level"
    />
    <span class="flex h-5 items-center text-base text-ink-gray-7">{{
      levels[spice]
    }}</span>
  </div>
</template>
```

### Mood check-in

The `#icon` slot draws a different emoji at each position. The slot's `index`,
`value` and `previewValue` pick which one stands out.

```vue
<script setup lang="ts">
import { ref } from 'vue'
import { Rating } from 'frappe-ui'

const mood = ref(3)
const moods = ['😞', '🙁', '😐', '🙂', '😊']

const score = ref(2)
const scores = ['💩', '👎', '👌', '👍', '🔥']
</script>

<template>
  <div class="flex flex-col gap-4 items-center">
    <Rating v-model="mood" label="How was your day?" size="lg">
      <template #icon="{ index, value, previewValue }">
        <span
          class="text-3xl leading-none transition-transform"
          :class="
            index === (previewValue ?? value) ? 'scale-110' : 'opacity-40'
          "
        >
          {{ moods[index - 1] }}
        </span>
      </template>
    </Rating>
    <Rating v-model="score" label="Rate this meal" size="lg">
      <template #icon="{ index, value, previewValue }">
        <span
          class="text-3xl leading-none"
          :class="
            index === (previewValue ?? value) ? '' : 'grayscale opacity-50'
          "
        >
          {{ scores[index - 1] }}
        </span>
      </template>
    </Rating>
  </div>
</template>
```

## Behavior

### Value

The value is a number from `0` to `max`, where `0` means no rating. `max`
defaults to `5`. A value outside that range shows as the nearest end, and a
value between steps shows rounded to the nearest `step`.

### Clearing

Clicking the selected star, or pressing `0`, sets the rating to `0`. To keep
people from clearing it, bind the value yourself and ignore the `0` update:

```vue
<Rating
  :model-value="value"
  @update:model-value="(v) => { if (v !== 0) value = v }"
/>
```

### Hover preview

While the pointer is over the stars, they show the value a click would pick.
Stars that the click would add and stars that it would remove are drawn in
lighter shades.

### Icon

`icon` takes a class name (`icon="lucide-zap"`), which renders as a `<span>`
with that class, or a Vue component. A component gets `fill="currentColor"`,
so closed shapes render filled.

The default icon is a filled star. The `lucide-star` class icon draws only the
outline, so it cannot replace the default.

### Icon slot

The `#icon` slot replaces the icon at each position, for content such as emojis
or icons that change from one position to the next. It renders twice per star,
once for each half, so half stars still work.

The slot receives `{ index, side, state, leftState, rightState, value,
previewValue, max }`. `index` starts at `1`. `state` is `filled`, `preview`,
`removing` or `empty`, and describes the half being drawn. Use it to set the
icon's color. `previewValue` is the value under the pointer, or `null`, so
`previewValue ?? value` is the value to highlight.

### Size

`size` defaults to `sm`, like every other input. A value it does not know
renders as `sm`.

### Label, description and error

`label` renders above the stars and `description` below them. `error` renders
below the stars and hides `description`. It takes a string, an array of
strings (one line each), or an `Error`, the same values as
[ErrorMessage](errormessage.md). An empty string or an empty array means no
error. `required` adds a red asterisk to the label.

The `#label` slot replaces the label text and the required marker, and receives
`{ required }`. A `#description` slot is not hidden by `error`. It renders
above the error.

### Attributes

`class` and `style` go on the outer element: the wrapper when a label,
description or error shows, and the row of stars otherwise. Every other
attribute and listener goes on the row of stars.

## Accessibility

With whole stars, the row is a `radiogroup` and each star is a `radio` named
"3 of 5". Only the selected star, or the first star when there is no rating,
is in the tab order.

| Keys              | Action                                               |
| ----------------- | ---------------------------------------------------- |
| `ArrowLeft` `ArrowUp` / `ArrowRight` `ArrowDown` | Select the previous / next star and move focus to it |
| `Home` / `End`    | Select the first / last star                         |
| `Space` / `Enter` | Select the focused star                              |
| `1`–`9`           | Set the rating to that number                        |
| `0`               | Clear the rating                                     |

With `step="0.5"`, the whole row is one `slider`. Screen readers announce its
value as "3.5 of 5 stars", or "No rating, out of 5 stars" at `0`. The slider
role does not allow `aria-required`, so a required rating in this mode is
announced only through the hidden "(required)" text in the label.

| Keys                  | Action                              |
| --------------------- | ----------------------------------- |
| `ArrowLeft` `ArrowDown` / `ArrowRight` `ArrowUp`     | Decrease / increase by half a star  |
| `PageDown` / `PageUp` | Decrease / increase by one star     |
| `Home` / `End`        | Set to `0` / `max`                  |
| `0`–`9`               | Set the rating to that number       |

In both modes, a number above `max` sets the rating to `max`.

## API reference

```ts
const propsData = [
  {
    name: 'modelValue',
    description: 'The current rating value (controlled). In star units, `0..max`, in increments of `step`.',
    required: false,
    type: 'number',
    default: '0'
  },
  {
    name: 'max',
    description: 'Number of stars to render. Defaults to 5.',
    required: false,
    type: 'number'
  },
  {
    name: 'step',
    description: 'Granularity of the rating value. `1` for whole stars, `0.5` for half stars.\nDefaults to `1`.',
    required: false,
    type: '1 | 0.5',
    default: '1'
  },
  {
    name: 'disabled',
    description: 'If true, disables interaction.',
    required: false,
    type: 'boolean',
    default: 'false'
  },
  {
    name: 'icon',
    description: 'Icon to render for each star. Accepts a class-icon name\n(`icon="lucide-heart"`), which renders as a `<span>` carrying that class,\nor a Vue component. A component receives `fill="currentColor"` so\nclosed-path SVGs render filled.\n\nDefaults to an inline filled star. The `lucide-star` class icon is a mask\nof lucide\'s outline star, so it cannot draw a solid one.',
    required: false,
    type: 'string | Component',
    default: 'RatingStar'
  },
  {
    name: 'size',
    description: 'Size of the rating component.',
    required: false,
    type: 'InputSize',
    default: '"sm"'
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
    name: 'icon',
    description: 'Overrides the per-star icon. Called once per star and stamped into both\nhalf-spans (so half-step clipping still works). Use `state` to color the\nicon, or `index` to render different content per position (e.g. emojis).',
    type: 'RatingIconSlotProps'
  }
]

  const emitsData = [
  {
    name: 'update:modelValue',
    description: 'Fired when the model value changes.',
    type: '[value: number]'
  }
]

  const exposedData = [
  {
    name: 'focus',
    description: 'Moves focus to the selected star, or to the first star when nothing is\nselected. That is the star `Tab` reaches. In half-star mode the whole\ncontrol is one slider, so it focuses the control itself.',
    type: '(options?: FocusOptions) => void'
  }
]
```
