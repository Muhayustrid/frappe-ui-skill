<!-- Upstream: https://github.com/frappe/frappe-ui/blob/b435c66b51c0b19425a2a049b43e3ef2e308a8ab/experimental/Stepper/Stepper.md -->

# Stepper

Shows where a person or a process is in an ordered sequence of steps.

> **Experimental** — `Stepper` ships from
> [`frappe-ui/experimental`](../experimental.md) while its API is reviewed and
> refined, so it is exempt from the usual deprecation policy and can change
> shape or disappear in any release.

```ts
import { Stepper } from 'frappe-ui/experimental'
import type { StepItem, StepperProps } from 'frappe-ui/experimental'
```



## Examples

### Setup wizard

`clickable` turns finished steps into buttons, so a person can go back. A step
marked `skipped` shows as passed, without a check.

```vue
<script setup lang="ts">
// A setup wizard: finished steps are clickable, and a step can be skipped.
import { computed, ref } from 'vue'
import { Button } from 'frappe-ui'
import { Stepper } from '..'
import type { StepItem } from '..'

const steps = ref<StepItem[]>([
  {
    value: 'account',
    label: 'Account',
    description: 'Name, email and password',
  },
  {
    value: 'workspace',
    label: 'Workspace',
    description: 'Company name and URL',
  },
  { value: 'invite', label: 'Invite team', description: 'Add people by email' },
  {
    value: 'import',
    label: 'Import data',
    description: 'Bring in leads from a CSV',
  },
  {
    value: 'review',
    label: 'Review',
    description: 'Check everything and finish',
  },
])

const current = ref<string | number>('invite')
const index = computed(() =>
  steps.value.findIndex((s) => s.value === current.value),
)
const isLast = computed(() => index.value === steps.value.length - 1)

function next(skip = false) {
  steps.value[index.value].skipped = skip
  if (!isLast.value) current.value = steps.value[index.value + 1].value
}
</script>

<template>
  <div class="flex w-full max-w-lg gap-8">
    <div class="w-56 shrink-0">
      <Stepper v-model="current" :steps="steps" vertical clickable />
    </div>
    <div class="flex flex-1 flex-col justify-between gap-4">
      <p class="text-p-base text-ink-gray-7">{{ steps[index].description }}.</p>
      <div class="flex justify-end gap-2">
        <Button v-if="!isLast" variant="ghost" @click="next(true)">Skip</Button>
        <Button variant="solid" :disabled="isLast" @click="next()"
          >Continue</Button
        >
      </div>
    </div>
  </div>
</template>
```

### Plain

`vertical` with nothing else: the step number sits inside every step that is not
finished yet.

```vue
<script setup lang="ts">
// The default vertical list with nothing extra: numbers and labels.
import { Stepper } from '..'

const steps = [
  { value: 'account', label: 'Account' },
  { value: 'workspace', label: 'Workspace' },
  { value: 'invite', label: 'Invite team' },
  { value: 'import', label: 'Import data' },
  { value: 'review', label: 'Review' },
]
</script>

<template>
  <div class="w-56">
    <Stepper :steps="steps" model-value="invite" vertical />
  </div>
</template>
```

### Run progress with sub-steps

A step with `children` gets its own line of sub-steps. `loading` spins the
outline of the current step's circle; `failed` marks it failed. `edge="end"`
puts the indicators after the labels.

```vue
<script setup lang="ts">
// A long-running job whose third stage has parts: the one nested example.
import { onBeforeUnmount, ref } from 'vue'
import { Button } from 'frappe-ui'
import { Stepper } from '..'
import type { StepItem } from '..'

const steps: StepItem[] = [
  { value: 'plan', label: 'Resolution planning' },
  { value: 'scope', label: 'Scope alignment' },
  {
    value: 'implement',
    label: 'Implementation',
    children: [
      { value: 'configure', label: 'Configure solution' },
      { value: 'connect', label: 'Connect systems' },
      { value: 'apply', label: 'Apply changes' },
      { value: 'run', label: 'Run workflow' },
      { value: 'output', label: 'Prepare output' },
    ],
  },
  { value: 'verify', label: 'Outcome verification' },
  { value: 'summary', label: 'Final summary' },
]

const order = steps.flatMap((s) => s.children ?? [s]).map((s) => s.value)
const current = ref<string | number>('apply')
const completed = ref(false)
const failed = ref(false)
let timer: ReturnType<typeof setInterval> | undefined

function advance() {
  const i = order.indexOf(current.value)
  if (i < order.length - 1) current.value = order[i + 1]
  else completed.value = true
}

function run() {
  clearInterval(timer)
  current.value = order[0]
  completed.value = false
  failed.value = false
  timer = setInterval(
    () => (completed.value ? clearInterval(timer) : advance()),
    900,
  )
}

onBeforeUnmount(() => clearInterval(timer))
</script>

<template>
  <div class="flex flex-col items-center gap-6">
    <div class="w-72">
      <Stepper
        v-model="current"
        :steps="steps"
        :completed="completed"
        :failed="failed"
        :loading="!completed && !failed"
        vertical
        edge="end"
      />
    </div>
    <div class="flex gap-2">
      <Button @click="run">Run again</Button>
      <Button :disabled="completed" @click="failed = !failed">
        {{ failed ? 'Retry' : 'Fail this step' }}
      </Button>
    </div>
  </div>
</template>
```

### Compact

`size="sm"` for a sidebar or a run log. `meta` shows trailing text such as a
duration.

```vue
<script setup lang="ts">
// The small size for a sidebar or run log, with each step's duration.
import { Stepper } from '..'

const steps = [
  { value: 'fetch', label: 'Fetch sources', meta: '0:42' },
  { value: 'install', label: 'Install dependencies', meta: '1:08' },
  { value: 'build', label: 'Build assets', meta: '0:31' },
  { value: 'migrate', label: 'Migrate database' },
  { value: 'deploy', label: 'Deploy' },
]
</script>

<template>
  <div class="w-64">
    <Stepper :steps="steps" model-value="build" size="sm" vertical loading />
  </div>
</template>
```

### Segmented

Without `vertical`, the stepper is the stock `Progress` with one interval per
step, and the labels underneath. Sub-steps are not shown.

```vue
<script setup lang="ts">
// The horizontal default: the stock Progress with one interval per step.
import { Stepper } from '..'

const steps = [
  { value: 'account', label: 'Account' },
  { value: 'workspace', label: 'Workspace' },
  { value: 'invite', label: 'Invite team', skipped: true },
  { value: 'import', label: 'Import data' },
  { value: 'review', label: 'Review' },
]
</script>

<template>
  <div class="w-full max-w-2xl">
    <Stepper :steps="steps" model-value="import" />
  </div>
</template>
```

## Behavior

### Current step

`v-model` holds the current step's value, which can be a sub-step's value. Every
step before it shows as done and every step after it as upcoming. An empty
value, or one that matches no step, shows every step as upcoming. `completed`
shows every step as done.

A step with sub-steps follows them: done once every sub-step is done or skipped,
current while it holds the current sub-step. Naming the parent itself makes its
first sub-step current.

### Skipped steps

Set `skipped` on a step the flow passed without doing. It shows as skipped once
the current step is past it, and as upcoming before that. The line through a
skipped step fills like a finished one.

### Running and failed

`loading` and `failed` describe the current step. `loading` keeps the circle and
its number still and moves a short arc around the outline. It stops under
reduced motion.

### Going back

With `clickable`, finished and skipped steps are buttons. Clicking one emits
`update:modelValue` with its value. While `completed` is set, no step is a
button, because `completed` overrides the value a click would set. The stepper
never moves on its own: the caller decides what the next step is.

## Accessibility

The steps are an ordered list, and sub-steps a nested list. The current step has
`aria-current="step"`. Each indicator carries a hidden word for its state
("completed", "running", "skipped", …), so the list reads without the icons.
With `clickable`, finished steps are buttons you can reach with Tab and press
with Enter or Space.

## API reference

```ts
const propsData = [
  {
    name: 'steps',
    description: 'Steps in order.',
    required: true,
    type: 'StepItem[]'
  },
  {
    name: 'modelValue',
    description: 'Value of the current step; may be a sub-step\'s value.',
    required: false,
    type: 'StepValue | null',
    default: 'null'
  },
  {
    name: 'completed',
    description: 'Every step is done. Wins over `modelValue`.',
    required: false,
    type: 'boolean',
    default: 'false'
  },
  {
    name: 'loading',
    description: 'The current step is running: its indicator\'s outline spins.',
    required: false,
    type: 'boolean',
    default: 'false'
  },
  {
    name: 'failed',
    description: 'The current step failed.',
    required: false,
    type: 'boolean',
    default: 'false'
  },
  {
    name: 'vertical',
    description: 'Lays the steps out as a vertical list instead of a segmented bar.',
    required: false,
    type: 'boolean',
    default: 'false'
  },
  {
    name: 'edge',
    description: 'Vertical only: the side the indicators sit on.',
    required: false,
    type: 'StepperEdge',
    default: '"start"'
  },
  {
    name: 'size',
    description: 'Size of the indicators and text.',
    required: false,
    type: 'StepperSize',
    default: '"md"'
  },
  {
    name: 'substeps',
    description: 'Vertical only: show sub-steps for every step, or only the current one.',
    required: false,
    type: '"all" | "current"',
    default: '"all"'
  },
  {
    name: 'clickable',
    description: 'Finished steps become buttons that set `v-model`. Off while `completed`\nis set, since `completed` would override the value a click sets.',
    required: false,
    type: 'boolean',
    default: 'false'
  }
]

  const slotsData = [
  {
    name: 'step-prefix',
    description: 'Replaces a step\'s indicator.',
    type: 'StepSlotProps'
  },
  {
    name: 'step-label',
    description: 'Replaces a step\'s label.',
    type: 'StepSlotProps'
  },
  {
    name: 'step-description',
    description: 'Replaces a step\'s description (vertical only).',
    type: 'StepSlotProps'
  },
  {
    name: 'step-suffix',
    description: 'Replaces a step\'s trailing meta text (vertical only).',
    type: 'StepSlotProps'
  }
]

  const emitsData = [
  {
    name: 'update:modelValue',
    description: 'Fired when a finished step is clicked (`clickable` only).',
    type: '[value: StepValue]'
  }
]
```
