<!-- Upstream: https://github.com/frappe/frappe-ui/blob/b435c66b51c0b19425a2a049b43e3ef2e308a8ab/src/components/FormControl/FormControl.md -->

# FormControl

One component that renders any form field from its `type` prop, with the same
`label`, `description`, `error` and `required` props for every type. When you
need a field's full set of props and slots, or its exact value type, use that
component directly.



## Examples

### Create-account form

Most `type` values in one form. Submit it empty to see each field's `error`.

```vue
<script setup lang="ts">
import { computed, reactive, ref } from 'vue'
import { Button, FormControl } from 'frappe-ui'

const form = reactive({
  fullName: '',
  email: '',
  password: '',
  role: '',
  team: '',
  skills: [] as string[],
  startDate: '',
  availability: [] as string[],
  bio: '',
  acceptTerms: false,
})

const submitted = ref(false)

const errors = computed(() => {
  if (!submitted.value) return {} as Record<string, string>
  const e: Record<string, string> = {}
  if (!form.fullName.trim()) e.fullName = 'Full name is required.'
  if (!form.email.trim()) e.email = 'Email is required.'
  else if (!/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(form.email))
    e.email = 'Enter a valid email.'
  if (!form.password) e.password = 'Pick a password.'
  else if (form.password.length < 8) e.password = 'Use at least 8 characters.'
  if (!form.role) e.role = 'Pick a role.'
  if (!form.team) e.team = 'Pick a team.'
  if (form.skills.length === 0) e.skills = 'Pick at least one skill.'
  if (!form.startDate) e.startDate = 'Pick a start date.'
  if (!form.acceptTerms) e.acceptTerms = 'You must accept the terms.'
  return e
})

const isValid = computed(() => Object.keys(errors.value).length === 0)

const roleOptions = [
  { label: 'Engineer', value: 'engineer' },
  { label: 'Designer', value: 'designer' },
  { label: 'Product Manager', value: 'pm' },
  { label: 'Other', value: 'other' },
]

const teamOptions = [
  { label: 'Platform', value: 'platform' },
  { label: 'Growth', value: 'growth' },
  { label: 'Frontend', value: 'frontend' },
  { label: 'Backend', value: 'backend' },
  { label: 'Mobile', value: 'mobile' },
]

const skillOptions = [
  { label: 'Vue', value: 'vue' },
  { label: 'TypeScript', value: 'typescript' },
  { label: 'Python', value: 'python' },
  { label: 'Frappe Framework', value: 'frappe' },
  { label: 'Tailwind CSS', value: 'tailwind' },
  { label: 'PostgreSQL', value: 'postgres' },
]

function submit() {
  submitted.value = true
}

function reset() {
  Object.assign(form, {
    fullName: '',
    email: '',
    password: '',
    role: '',
    team: '',
    skills: [],
    startDate: '',
    availability: [],
    bio: '',
    acceptTerms: false,
  })
  submitted.value = false
}
</script>

<template>
  <form class="w-full max-w-lg space-y-4 py-4" @submit.prevent="submit">
    <div class="space-y-1">
      <h2 class="text-lg-semibold text-ink-gray-9">Create account</h2>
      <p class="text-p-sm text-ink-gray-6">
        Every <code>FormControl</code> type rendered together. Submit to see
        validation light up.
      </p>
    </div>

    <FormControl
      v-model="form.fullName"
      type="text"
      label="Full name"
      placeholder="Jane Doe"
      :error="errors.fullName"
      required
    />

    <FormControl
      v-model="form.email"
      type="email"
      label="Email"
      description="We'll never share your email."
      placeholder="you@example.com"
      :error="errors.email"
      required
    />

    <FormControl
      v-model="form.password"
      type="password"
      label="Password"
      description="At least 8 characters."
      :error="errors.password"
      required
    />

    <FormControl
      v-model="form.role"
      type="select"
      label="Role"
      placeholder="Pick a role"
      :options="roleOptions"
      :error="errors.role"
      required
    />

    <FormControl
      v-model="form.team"
      type="combobox"
      label="Team"
      placeholder="Type to filter"
      :options="teamOptions"
      :error="errors.team"
      required
    />

    <FormControl
      v-model="form.skills"
      type="multiselect"
      label="Skills"
      description="Pick all that apply."
      placeholder="Pick skills"
      :options="skillOptions"
      :error="errors.skills"
      required
    />

    <FormControl
      v-model="form.startDate"
      type="date"
      label="Start date"
      placeholder="Pick a start date"
      :error="errors.startDate"
      required
    />

    <FormControl
      v-model="form.availability"
      type="daterange"
      label="Availability window"
      description="When are you free for onboarding?"
      placeholder="Pick a range"
    />

    <FormControl
      v-model="form.bio"
      type="textarea"
      label="Short bio"
      description="A sentence or two — optional."
      placeholder="Tell us a little about yourself..."
    />

    <FormControl
      v-model="form.acceptTerms"
      type="checkbox"
      label="I accept the terms and privacy policy"
    />
    <p v-if="errors.acceptTerms" class="text-p-sm text-ink-red-5">
      {{ errors.acceptTerms }}
    </p>

    <div class="flex items-center gap-2 pt-2">
      <Button variant="solid" type="submit">Create account</Button>
      <Button variant="ghost" type="button" @click="reset">Reset</Button>
      <span
        v-if="submitted && isValid"
        class="ml-auto text-p-sm text-ink-green-5"
      >
        Looks good!
      </span>
    </div>
  </form>
</template>
```

### Settings form from a field list

One `FormControl` in a `v-for` renders a list of field definitions, whatever
their types. This is the main reason to use it over the individual components.

```vue
<script setup lang="ts">
import { reactive } from 'vue'
import { FormControl } from 'frappe-ui'
import type { FormControlProps } from 'frappe-ui'

interface Field {
  name: string
  type: FormControlProps['type']
  label: string
  options?: string[]
}

// The fields come from data, as they would from a doctype's meta.
const fields: Field[] = [
  { name: 'company', type: 'text', label: 'Company name' },
  {
    name: 'currency',
    type: 'select',
    label: 'Default currency',
    options: ['USD', 'EUR', 'INR'],
  },
  { name: 'fiscalYearStart', type: 'date', label: 'Fiscal year starts' },
  { name: 'address', type: 'textarea', label: 'Address' },
  { name: 'sendReports', type: 'checkbox', label: 'Email monthly reports' },
]

const settings = reactive<Record<string, unknown>>({
  company: 'Acme Inc.',
  currency: 'USD',
  fiscalYearStart: '2026-04-01',
  address: '',
  sendReports: true,
})
</script>

<template>
  <div class="flex w-full max-w-sm flex-col gap-4">
    <FormControl
      v-for="field in fields"
      :key="field.name"
      v-model="settings[field.name]"
      :type="field.type"
      :label="field.label"
      :options="field.options"
    />
  </div>
</template>
```

## Behavior

### Supported types

| `type`            | Renders                                                       |
| ----------------- | ------------------------------------------------------------- |
| `text` _(default)_, `email`, `password`, `number`, `search`, `tel`, `url`, `file`, `range`, `month`, `week`, `datetime-local` | [`TextInput`](textinput.md) with the matching HTML `type`      |
| `textarea`        | [`Textarea`](textarea.md)                                      |
| `select`          | [`Select`](select.md)                                          |
| `combobox`        | [`Combobox`](combobox.md)                                      |
| `multiselect`     | [`MultiSelect`](multiselect.md)                                |
| `checkbox`        | [`Checkbox`](checkbox.md)                                      |
| `date`            | [`DatePicker`](datepicker.md#date-picker)                      |
| `daterange`       | [`DateRangePicker`](datepicker.md#date-range-picker)           |
| `datetime`        | [`DateTimePicker`](datepicker.md#datetime-picker)              |
| `time`            | [`TimePicker`](timepicker.md)                                  |

For the browser's native date or time input, use `<TextInput type="date" />`,
or `type="datetime-local"` here.

### Label, description and error

`FormControl` passes `label`, `description`, `error` and `required` to the
component it renders, and that component draws them. `error` replaces the
description and marks the field invalid.

```vue
<FormControl
  v-model="email"
  type="email"
  label="Email"
  description="We'll never share your email."
  :error="errors.email"
  required
/>
```

### Props, slots and attributes

- **Props and listeners.** `placeholder`, `disabled`, `modelValue`, `options`,
  `min`/`max`, `formatter` and the rest go to the rendered component.
  `FormControl` does not declare them itself.
- **Slots.** Every slot you pass goes through by name. The slots you can use
  depend on `type`: `#prefix` and `#suffix` for `TextInput` and the pickers,
  `#item-prefix` and `#item-label` for `Select` and `Combobox`, and so on. See
  each component's page.
- **`class` and `style`.** They go to the rendered component, which places
  them on its outer element.

`size` and `variant` go to every type, with two exceptions for
`type="checkbox"`. A checkbox draws no box, so it does not get `variant`. Its
size scale ends at `md`, so `size="lg"` renders as `md`.

The `select`, `combobox`, `multiselect` and picker types fill the width of
their container.

### Value types

`FormControl` does not check type-specific props or the `v-model` type for
each `type`. The value follows the rendered component: `multiselect` emits an
array, `checkbox` a boolean, `daterange` the value `DateRangePicker` emits, and
so on.

## API reference

```ts
const propsData = [
  {
    name: 'label',
    description: 'Label text displayed above the input',
    required: false,
    type: 'string'
  },
  {
    name: 'description',
    description: 'Optional description or helper text shown below the input',
    required: false,
    type: 'string'
  },
  {
    name: 'error',
    description: 'Error message shown below the input. Sets aria-invalid on the control.',
    required: false,
    type: 'ErrorMessageValue'
  },
  {
    name: 'type',
    description: 'Type of input to render. FormControl is a thin dispatcher — it forwards\n`label`/`description`/`error`/`required`/`size`/`variant` plus all\nremaining attrs/listeners to the resolved child component. Type-specific\nprops (e.g. `options` for select/combobox, `min`/`max`/`formatter` for\ndate pickers, `:options` for multiselect) and the `v-model` value shape\nfollow the underlying component — see that component\'s docs/types for\nthe full surface. Slots are forwarded by name; only slot names declared\non FormControl get IDE typing, others pass through at runtime.',
    required: false,
    type: 'TextInputTypes | "textarea" | "select" | "checkbox" | "combobox" | "multiselect" | "daterange" | "datetime"',
    default: '"text"'
  },
  {
    name: 'size',
    description: 'Size of the input. The full input scale — `xs` 24px, `sm` 28px, `md` 32px,\n`lg` 40px. `type="checkbox"` renders on the narrower toggle scale, so `lg`\nis clamped to that scale\'s largest value there.',
    required: false,
    type: 'InputSize',
    default: '"sm"'
  },
  {
    name: 'variant',
    description: 'Visual variant of the input',
    required: false,
    type: '"subtle" | "outline"',
    default: '"subtle"'
  },
  {
    name: 'required',
    description: 'Whether the input is required',
    required: false,
    type: 'boolean'
  }
]

  const slotsData = [
  {
    name: 'prefix',
    description: 'Custom content rendered before the input (prefix icon/content)',
    type: 'any'
  },
  {
    name: 'suffix',
    description: 'Custom content rendered after the input (suffix icon/content)',
    type: 'any'
  },
  {
    name: 'description',
    description: 'Custom description slot (replaces description prop)',
    type: 'any'
  },
  {
    name: 'label',
    description: 'Custom label slot (replaces label prop). Receives `{ required }`.',
    type: '{ required: boolean; }'
  },
  {
    name: 'item-prefix',
    description: 'Per-item prefix content, forwarded to the combobox/multiselect rows',
    type: '{ item: any; }'
  },
  {
    name: 'default',
    description: 'Default slot override for full input rendering',
    type: 'any'
  }
]

  const exposedData = [
  {
    name: 'focus',
    description: 'Moves focus to the control that `type` renders.',
    type: '(options?: FocusOptions) => void'
  }
]
```
