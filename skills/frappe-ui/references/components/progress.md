<!-- Upstream: https://github.com/frappe/frappe-ui/blob/b435c66b51c0b19425a2a049b43e3ef2e308a8ab/src/components/Progress/Progress.md -->

# Progress

A bar that shows how far a task has come, as a value from 0 to 100.



## Examples

### File upload

`hint` shows the value as a percentage on the right of the label. The bar
updates each time `value` changes.

```vue
<script setup lang="ts">
import { ref } from 'vue'
import { useIntervalFn } from '@vueuse/core'
import { Progress, Button } from 'frappe-ui'

const value = ref(0)

// A poll or upload reports progress in coarse steps. The fill transitions
// between them, so the bar reads as continuous motion instead of jumping.
// The tick matches the fill's own transition duration, so each step is still
// moving when the next arrives — that hand-off is the point of the demo.
const { pause, resume, isActive } = useIntervalFn(
  () => {
    value.value += 10
    if (value.value >= 100) pause()
  },
  660,
  { immediate: false },
)

function start() {
  value.value = 0
  resume()
}
</script>

<template>
  <div class="grid w-full max-w-[400px] gap-4">
    <Progress :value="value" label="Uploading" :hint="true" size="md" />

    <div class="flex gap-2">
      <Button variant="solid" :disabled="isActive" @click="start">
        {{ value === 100 ? 'Restart' : 'Start' }}
      </Button>
      <Button :disabled="!isActive" @click="pause()">Pause</Button>
    </div>
  </div>
</template>
```

### Storage quota

A meter for how much of a quota is used. The numbers sit below the bar instead
of in the `#hint` slot, because "700 GB of 2 TB" says more than a percentage
and shares the row with an action.

```vue
<script setup lang="ts">
// A quota meter can pair calculated progress with richer usage details below.
import { computed } from 'vue'
import { Button, Progress } from 'frappe-ui'

const usedGB = 700
const totalGB = 2048

const percent = computed(() => Math.round((usedGB / totalGB) * 100))
const usageLabel = computed(
  () => `${formatSize(usedGB)} of ${formatSize(totalGB)}`,
)

function formatSize(gigabytes: number) {
  if (gigabytes >= 1024) return `${gigabytes / 1024} TB`
  return `${gigabytes} GB`
}
</script>

<template>
  <div class="w-full max-w-[240px]">
    <Progress :value="percent" label="Storage" size="md" />

    <div class="mt-3.5 flex items-center gap-2">
      <span
        class="lucide-cloud size-4 shrink-0 text-ink-gray-5"
        aria-hidden="true"
      />
      <span class="text-base text-ink-gray-5">{{ usageLabel }}</span>
      <Button class="ml-auto" variant="ghost" size="sm">Manage</Button>
    </div>
  </div>
</template>
```

### Onboarding checklist

The value comes from how many steps are done, so finishing a step moves the
bar.

```vue
<script setup lang="ts">
// Checklist completion drives progress while the next unfinished task stays in focus.
import { computed, ref } from 'vue'
import { Button, Progress } from 'frappe-ui'

const steps = ref([
  {
    title: 'Upload file or folder',
    description: 'Add content to start organizing your workspace.',
    action: 'Upload',
    done: true,
  },
  {
    title: 'Share a file or folder',
    description: "Share content with someone you'd like to collaborate with.",
    action: 'Share',
    done: false,
  },
  {
    title: 'Create a shared folder',
    description: 'Keep files your team uses together in one place.',
    action: 'Create',
    done: false,
  },
])

const expanded = ref<number | null>(1)
const percent = computed(() => {
  const doneCount = steps.value.filter((step) => step.done).length
  return Math.round((doneCount / steps.value.length) * 100)
})

function toggleStep(index: number) {
  expanded.value = expanded.value === index ? null : index
}

function completeStep(index: number) {
  steps.value[index].done = true

  const followingStep = steps.value.findIndex(
    (step, stepIndex) => stepIndex > index && !step.done,
  )
  expanded.value =
    followingStep === -1
      ? steps.value.findIndex((step) => !step.done)
      : followingStep

  if (expanded.value === -1) {
    expanded.value = null
  }
}
</script>

<template>
  <div class="w-full max-w-[320px] rounded-7 bg-surface-base p-2.5 shadow-lg">
    <Progress
      :value="percent"
      label="Get Started with Frappe Drive"
      size="md"
    />

    <div class="mt-6 flex flex-col gap-1">
      <div
        v-for="(step, index) in steps"
        :key="step.title"
        class="rounded-6 p-2"
        :class="{ 'bg-surface-gray-1': expanded === index }"
      >
        <button
          type="button"
          class="flex w-full items-center gap-2 text-left"
          :aria-expanded="expanded === index"
          @click="toggleStep(index)"
        >
          <span
            class="size-4 shrink-0"
            :class="step.done ? 'lucide-circle-check' : 'lucide-circle'"
            aria-hidden="true"
          />
          <span class="flex-1 text-base text-ink-gray-8">
            {{ step.title }}
          </span>
          <span
            class="size-4 shrink-0"
            :class="
              expanded === index ? 'lucide-chevron-up' : 'lucide-chevron-down'
            "
            aria-hidden="true"
          />
        </button>

        <div v-if="expanded === index" class="mt-1 px-6">
          <p class="text-p-sm text-ink-gray-6">
            {{ step.description }}
          </p>
          <Button
            class="mt-2"
            variant="solid"
            size="sm"
            @click="completeStep(index)"
          >
            {{ step.action }}
          </Button>
        </div>
      </div>
    </div>
  </div>
</template>
```

### Multi-step form

`intervals` splits the bar into one segment per step. The `#hint` slot shows
"Step 2 of 3" in place of the percentage.

```vue
<script setup lang="ts">
// This story demonstrates segmented progress as a replayable step indicator.
import { computed, reactive, ref } from 'vue'
import { Button, FormControl, Progress } from 'frappe-ui'

const steps = ['Payment mode', 'Billing address', 'Review'] as const
const step = ref(0)
const form = reactive({
  cardNumber: '',
  cardholderName: '',
  city: '',
  postalCode: '',
})

const percent = computed(() => ((step.value + 1) / steps.length) * 100)

function continueForm() {
  step.value = step.value === steps.length - 1 ? 0 : step.value + 1
}
</script>

<template>
  <div class="w-full max-w-[400px] rounded-7 border border-outline-gray-1 p-4">
    <Progress :value="percent" :label="steps[step]" :intervals="steps.length">
      <template #hint>
        <span class="text-base text-ink-gray-5">
          Step {{ step + 1 }} of {{ steps.length }}
        </span>
      </template>
    </Progress>

    <div v-if="step === 0" class="mt-6 flex flex-col gap-4">
      <FormControl
        v-model="form.cardNumber"
        label="Card number"
        placeholder="1234 5678 9012 3456"
      />
      <FormControl
        v-model="form.cardholderName"
        label="Name on card"
        placeholder="Full name"
      />
    </div>

    <div v-else-if="step === 1" class="mt-6 flex flex-col gap-4">
      <FormControl v-model="form.city" label="City" placeholder="City" />
      <FormControl
        v-model="form.postalCode"
        label="Postal code"
        placeholder="Postal code"
      />
    </div>

    <p v-else class="mt-6 text-p-sm text-ink-gray-7">
      Confirm your details to authorize the subscription charge.
    </p>

    <div class="mt-6 flex justify-end gap-2">
      <Button :disabled="step === 0" @click="step -= 1">Back</Button>
      <Button variant="solid" @click="continueForm">
        {{ step === steps.length - 1 ? 'Confirm' : 'Save & Continue' }}
      </Button>
    </div>
  </div>
</template>
```

## Behavior

### Value

`value` is required and is a number from 0 to 100. A value below 0 shows an
empty bar, and a value above 100 shows a full bar.

### Label and hint

`label` shows on the left above the bar. `hint` shows the value followed by
`%` on the right. The `#hint` slot replaces that text with your own content,
and shows even when `hint` is not set.

### Steps

`intervals` sets the number of segments. The bar fills whole segments only:
the filled count is `value / 100 × intervals`, rounded to the nearest whole
number. Leave `intervals` out for a continuous bar.

## Accessibility

The bar has the `progressbar` role, with `aria-valuenow` set to the value
from 0 to 100. `label` is the bar's accessible name. A bar without a label is
named "Progress".

## API reference

```ts
const propsData = [
  {
    name: 'value',
    description: 'Current progress value',
    required: true,
    type: 'number'
  },
  {
    name: 'size',
    description: 'Size of the progress bar: "sm" | "md" | "lg" | "xl"',
    required: false,
    type: '"sm" | "md" | "lg" | "xl"',
    default: '"sm"'
  },
  {
    name: 'label',
    description: 'Optional text label displayed on the progress bar',
    required: false,
    type: 'string',
    default: '""'
  },
  {
    name: 'hint',
    description: 'Whether to show a hint/tooltip for the progress value',
    required: false,
    type: 'boolean',
    default: 'false'
  },
  {
    name: 'intervals',
    description: 'Number of interval markers to show; omit for a continuous progress bar',
    required: false,
    type: 'number'
  }
]

  const slotsData = [
  {
    name: 'hint',
    description: 'Custom content for the hint area (usually displays the progress value).\nIf not provided, defaults to showing `props.value` followed by `%`.',
    type: 'any'
  }
]
```
