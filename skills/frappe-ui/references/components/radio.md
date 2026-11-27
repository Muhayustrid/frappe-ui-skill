<!-- Upstream: https://github.com/frappe/frappe-ui/blob/b435c66b51c0b19425a2a049b43e3ef2e308a8ab/src/components/Radio/Radio.md -->

# Radio

A set of options where people pick exactly one. For a few options shown as
buttons, use [TabButtons](tabbuttons.md).



## Anatomy

`RadioGroup` holds the selected value and the group's label, description and
error. Each `Radio` inside it sets the `value` it stands for.

```vue
<RadioGroup v-model="plan" label="Choose a plan">
  <Radio value="free" label="Free" />
  <Radio value="pro" label="Pro" description="For growing teams." />
</RadioGroup>
```

## Examples

### Choose a plan

`required` on `RadioGroup` puts the asterisk on the group label.

```vue
<script setup lang="ts">
import { ref } from 'vue'
import { Radio, RadioGroup } from 'frappe-ui'

const plan = ref()
</script>

<template>
  <RadioGroup v-model="plan" label="Choose a plan" required class="w-96">
    <Radio value="free" label="Free" />
    <Radio value="pro" label="Pro" />
    <Radio value="enterprise" label="Enterprise" />
  </RadioGroup>
</template>
```

### Notification settings

`padded` makes each option a row with a clickable surface. A `description`
shows below the label, and one option is `disabled`.

```vue
<script setup lang="ts">
import { ref } from 'vue'
import { Radio, RadioGroup } from 'frappe-ui'

const notify = ref('all')
</script>

<template>
  <RadioGroup v-model="notify" padded class="w-96">
    <Radio value="all" label="All new emails" />
    <Radio
      value="important"
      label="Important emails only"
      description="Only emails where you're mentioned or assigned."
    />
    <Radio value="off" label="Mail notifications off" disabled />
  </RadioGroup>
</template>
```

## Behavior

### Inside a group

`Radio` must be inside a `RadioGroup`, and throws an error otherwise. The
group handles arrow keys, focus and form submission, so each option needs only
a `value`.

### Value

The value can be a `string`, `number` or `boolean`. When nothing is selected,
the value is `undefined`, which is where an unbound group starts.

### Group props

`required` and `error` are set on `RadioGroup` only, because they describe the
whole choice, not one option. `size` and `padded` are also set on the group
and apply to every option. `disabled` on the group disables every option, and
`disabled` on a `Radio` disables that option only.

### Label, description and error

On `RadioGroup`, `label` renders above the options and `description` below
them. `error` renders below the options and hides `description`. It takes a
string, an array of strings (one line each), or an `Error`, the same values as
[ErrorMessage](errormessage.md). An empty string or an empty array means no
error. `required` adds a red asterisk to the group label.

The `#label` slot replaces the label text and the required marker, and receives
`{ required }`. A `#description` slot is not hidden by `error`. It renders
above the error.

### Orientation

`orientation` is `vertical` by default. `horizontal` lays the options in a row
and switches the arrow keys to left and right. `loop` is `true` by default, so
the arrow keys go from the last option back to the first.

### Form submission

The group submits its value with a form. `name` sets the field name. Without
it, a name is generated.

### Attributes

`class` and `style` go to the layout wrapper. Everything else (`name`,
`aria-*`, `data-*` and listeners) goes once to the interactive element, which
for `RadioGroup` is the radio group itself.

## Accessibility

`RadioGroup` renders an element with `role="radiogroup"`, labelled by the
group label. Each `Radio` renders a `role="radio"` button that covers the
label and description.

| Keys                    | Action                                           |
| ----------------------- | ------------------------------------------------ |
| `Tab`                   | Move focus into the group, to the selected option |
| `ArrowDown` / `ArrowUp` | Select the next or previous option (vertical)    |
| `ArrowRight` / `ArrowLeft` | Select the next or previous option (horizontal) |
| `Space`                 | Select the focused option                        |

While `error` is set, the group gets `aria-invalid` and an
`aria-errormessage` that points to the error text.

## API reference

```ts
const radioProps = [
  {
    name: 'value',
    description: 'The value this option represents within its group.',
    required: true,
    type: 'RadioValue'
  },
  {
    name: 'disabled',
    description: 'Disables this option only. The group\'s `disabled` still wins when set.',
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
    name: 'id',
    description: 'HTML id of the underlying control. Auto-generated via `useId()` if omitted.',
    required: false,
    type: 'string'
  }
]

  const radioSlots = [
  {
    name: 'label',
    description: 'Overrides the rendered label content.',
    type: 'any'
  },
  {
    name: 'description',
    description: 'Overrides the rendered description content.',
    type: 'any'
  }
]

  const radioGroupProps = [
  {
    name: 'modelValue',
    description: 'The selected value of the group.',
    required: false,
    type: 'RadioValue'
  },
  {
    name: 'size',
    description: 'Size of every radio in the group.',
    required: false,
    type: 'ToggleSize',
    default: '"sm"'
  },
  {
    name: 'padded',
    description: 'Wraps each option in a clickable surface with hover, active and focus states — useful for selection lists and menu items.',
    required: false,
    type: 'boolean',
    default: 'false'
  },
  {
    name: 'disabled',
    description: 'Disables every radio in the group.',
    required: false,
    type: 'boolean',
    default: 'false'
  },
  {
    name: 'orientation',
    description: 'Layout of the options. Also decides which arrow keys move the selection: up/down when vertical, left/right when horizontal.',
    required: false,
    type: '"vertical" | "horizontal"',
    default: '"vertical"'
  },
  {
    name: 'loop',
    description: 'When `true`, arrow-key navigation wraps from the last option back to the first.',
    required: false,
    type: 'boolean',
    default: 'true'
  },
  {
    name: 'name',
    description: 'Native `name` for the hidden form input, so the group submits with a form. Auto-generated when omitted.',
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

  const radioGroupSlots = [
  {
    name: 'default',
    description: 'The `<Radio>` options.',
    type: 'any'
  },
  {
    name: 'label',
    description: 'Overrides the rendered group heading. Receives `{ required }`.',
    type: '{ required: boolean; }'
  },
  {
    name: 'description',
    description: 'Overrides the rendered group description.',
    type: 'any'
  }
]

  const radioGroupEmits = [
  {
    name: 'update:modelValue',
    description: 'Fired when the model value changes.',
    type: '[value: RadioValue | undefined]'
  }
]

  const radioGroupExposed = [
  {
    name: 'focus',
    description: 'Moves focus to the checked radio, or to the first enabled radio when\nnothing is checked.',
    type: '(options?: FocusOptions) => void'
  }
]
```
