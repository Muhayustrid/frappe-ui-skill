<!-- Upstream: https://github.com/frappe/frappe-ui/blob/b435c66b51c0b19425a2a049b43e3ef2e308a8ab/src/components/Select/Select.md -->

# Select

A button that opens a list of options and picks one. For a searchable list, use
[Combobox](combobox.md), and to pick several values, use
[MultiSelect](multiselect.md).



## Examples

### Dessert menu

`#item-prefix` and `#item-label` fill the standard row with a photo and a
second line of text. `#prefix` shows the selected option's photo on the
trigger.

```vue
<script setup lang="ts">
import { computed, ref } from 'vue'
import { Avatar, Select } from 'frappe-ui'

const value = ref('matcha-tiramisu')

const options = [
  {
    label: 'Matcha Tiramisu',
    value: 'matcha-tiramisu',
    image:
      'https://images.unsplash.com/photo-1563805042-7684c019e1cb?w=150&h=150&fit=crop',
    description: 'Kyoto cream · Soft sponge · Ceremonial matcha',
    price: '$14',
  },
  {
    label: 'Strawberry Cheesecake',
    value: 'strawberry-cheesecake',
    image:
      'https://images.unsplash.com/photo-1533134486753-c833f0ed4866?w=150&h=150&fit=crop',
    description: 'Fresh berries · Baked filling · Biscuit crust',
    price: '$16',
  },
  {
    label: 'Chocolate Lava Cake',
    value: 'chocolate-lava-cake',
    image:
      'https://images.unsplash.com/photo-1624353365286-3f8d62daad51?w=150&h=150&fit=crop',
    description: 'Warm center · Dark chocolate · Sea salt',
    price: '$15',
  },
  {
    label: 'Mango Sticky Rice',
    value: 'mango-sticky-rice',
    image:
      'https://images.unsplash.com/photo-1604085792782-8d92f276d7d8?w=150&h=150&fit=crop',
    description: 'Coconut cream · Sweet mango · Toasted sesame',
    price: '$13',
    disabled: true,
  },
]

const activeOption = computed(() => {
  return options.find((option) => option.value === value.value) ?? null
})
</script>

<template>
  <div>
    <Select v-model="value" :options="options" variant="outline" class="w-full">
      <template #prefix>
        <Avatar
          v-if="activeOption"
          size="sm"
          :image="activeOption.image"
          :label="activeOption.label"
        />
      </template>

      <template #item-prefix="{ item }">
        <Avatar size="sm" :image="item.image" :label="item.label" />
      </template>

      <template #item-label="{ item }">
        <div class="min-w-0">
          <div class="truncate">{{ item.label }}</div>
          <div
            class="truncate text-p-sm text-ink-gray-5"
            :class="item.disabled ? 'opacity-65' : ''"
          >
            {{ item.description }}
          </div>
        </div>
      </template>
    </Select>
  </div>
</template>
```

### Inline in a sentence

`#trigger` replaces the whole trigger, so the choice reads as part of the
sentence.

```vue
<script setup lang="ts">
import { ref } from 'vue'
import { Select } from 'frappe-ui'

const frequency = ref('mentions')

const options = [
  { label: 'for every message', value: 'all' },
  { label: 'for @mentions only', value: 'mentions' },
  { label: 'never', value: 'never' },
]
</script>

<template>
  <!--
    An inline trigger reads as part of the sentence rather than as a form
    control. `#prefix` / `#suffix` can't get here — they add to the default
    trigger shell, while `#trigger` replaces it. `variant="ghost"` and
    `size="sm"` keep the shell from drawing a border or a tall row.
  -->
  <p class="max-w-md text-base text-ink-gray-7">
    Notify me
    <Select
      v-model="frequency"
      :options="options"
      variant="ghost"
      size="sm"
      class="!px-0"
    >
      <template #trigger="{ selectedOption, open }">
        <span
          :class="[
            'rounded-4 px-1 -mx-1 font-medium text-ink-gray-8 decoration-outline-gray-3 underline-offset-4 transition-colors duration-150',
            open ? 'bg-surface-gray-3' : 'underline hover:bg-surface-gray-3',
          ]"
        >
          {{ selectedOption?.label }}
        </span>
      </template>
    </Select>
    in this project.
  </p>
</template>
```

### Timezone picker

The `#footer` slot sits below the options and stays in place while the list
scrolls.

```vue
<script setup lang="ts">
import { ref } from 'vue'
import { Select } from 'frappe-ui'

const value = ref('')

// A long list so the viewport scrolls — the footer should stay pinned to the
// bottom of the popover instead of scrolling away with the options.
const timezones = [
  'UTC-12:00',
  'UTC-11:00',
  'UTC-10:00',
  'UTC-09:00',
  'UTC-08:00',
  'UTC-07:00',
  'UTC-06:00',
  'UTC-05:00',
  'UTC-04:00',
  'UTC-03:00',
  'UTC-02:00',
  'UTC-01:00',
  'UTC+00:00',
  'UTC+01:00',
  'UTC+02:00',
  'UTC+03:00',
  'UTC+04:00',
  'UTC+05:00',
  'UTC+05:30',
  'UTC+06:00',
  'UTC+07:00',
  'UTC+08:00',
  'UTC+09:00',
  'UTC+10:00',
  'UTC+11:00',
  'UTC+12:00',
]
</script>

<template>
  <div class="grid gap-3">
    <Select v-model="value" :options="timezones" placeholder="Pick a timezone">
      <template #footer="{ selectedOption, clear, setOpen }">
        <div
          class="flex items-center justify-between border-t border-outline-gray-1 px-3 py-2 text-sm text-ink-gray-5"
        >
          <span>{{ timezones.length }} timezones</span>
          <button
            v-if="selectedOption"
            class="text-ink-gray-7 hover:text-ink-gray-8"
            @click="
              () => {
                clear()
                setOpen(false)
              }
            "
          >
            Clear
          </button>
          <span v-else class="text-ink-gray-7">Footer stays fixed</span>
        </div>
      </template>
    </Select>

    <div class="text-sm text-ink-gray-5">
      Selected: <code class="text-ink-gray-7">{{ value || 'None' }}</code>
    </div>
  </div>
</template>
```

## Behavior

### Options

`Select` takes a flat list of options. It has no groups. Empty and nullish
options are left out. Option values are `string | number`. An option with
`disabled: true` shows in the list but cannot be picked.

### Empty value

When nothing is selected, the value is `null`, the same as in `Combobox`. An
empty string is a real value, so a "None" option with `value: ''` round-trips.

For a "Sort by" menu, add a first option with `value: ''` and
`disabled: true`. It shows as the label while nothing else is picked, and
people cannot select it.

### Width

`Select` sizes itself to fit its options. The trigger fits the selected value,
and the menu grows to fit longer options. Add `class="w-full"` for a
full-width trigger.

### Item slots

`#item-prefix`, `#item-label` and `#item-suffix` change parts of the standard
row. `#item` replaces the whole row, including its outer element.

### Trigger slots

`#trigger` replaces the trigger content. For smaller changes, `#prefix` and
`#suffix` sit inside the default trigger. `#suffix` replaces the chevron.

`#trigger`, `#prefix`, `#suffix` and `#footer` all receive
`{ open, disabled, selectedOption, clear, setOpen, close }`. `close()` is the
same as `setOpen(false)`.

### Open state

Use `v-model:open` when a parent component controls the menu. Inside
`#trigger` or `#footer`, use the `setOpen` slot prop.

### Menu placement

By default the menu opens over the trigger, lined up with the selected option.
Pass `side`, `align` or `offset` to place it next to the trigger instead.
`portalTo` changes where the menu is teleported in both modes.

### Label, description and error

`label` renders above the trigger and `description` below it. `error` renders
below the trigger and hides `description`. It takes a string, an array of
strings (one line each), or an `Error`, the same values as
[ErrorMessage](errormessage.md). An empty string or an empty array means no
error. `required` adds a red asterisk to the label.

The `#label` slot replaces the label text and the required marker, and receives
`{ required }`. A `#description` slot is not hidden by `error`. It renders
above the error.

## Accessibility

While `error` is set, the trigger gets `aria-invalid` and an
`aria-errormessage` that points to the error text.

## API reference

```ts
const propsData = [
  {
    name: 'size',
    description: 'Size of the select input.',
    required: false,
    type: 'InputSize',
    default: '"sm"'
  },
  {
    name: 'variant',
    description: 'Visual style of the select input.',
    required: false,
    type: 'InputVariant',
    default: '"subtle"'
  },
  {
    name: 'placeholder',
    description: 'Placeholder text displayed when no option is selected.',
    required: false,
    type: 'string',
    default: '"Select option"'
  },
  {
    name: 'disabled',
    description: 'If true, disables the select input.',
    required: false,
    type: 'boolean'
  },
  {
    name: 'modelValue',
    description: 'The currently selected value. `null` when nothing is selected.',
    required: false,
    type: 'SelectOptionValue | null'
  },
  {
    name: 'open',
    description: 'Controls the visibility of the select menu.',
    required: false,
    type: 'boolean',
    default: 'false'
  },
  {
    name: 'options',
    description: 'Options to display in the dropdown.',
    required: false,
    type: 'SelectOption[]',
    default: '[]'
  },
  {
    name: 'emptyText',
    description: 'Fallback empty-state copy rendered when no options are available.',
    required: false,
    type: 'string',
    default: '"No options"'
  },
  {
    name: 'side',
    description: 'Preferred popover side. Defaults to `\'bottom\'`.\n\nSetting `side`, `align`, or `offset` switches the menu from its default\nitem-aligned placement (anchored over the trigger, macOS-style) to\nstandard popper placement below/beside the trigger.',
    required: false,
    type: 'PopoverSide'
  },
  {
    name: 'align',
    description: 'Preferred popover alignment. Defaults to `\'start\'`. See `side`.',
    required: false,
    type: 'PopoverAlign'
  },
  {
    name: 'offset',
    description: 'Gap in px between trigger and content. Defaults to `4`. See `side`.',
    required: false,
    type: 'number'
  },
  {
    name: 'portalTo',
    description: 'Teleport target for the popover content. Unset, an embedding host\'s target is used, else `body`.',
    required: false,
    type: 'PortalTarget'
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
    name: 'trigger',
    description: 'Fully custom trigger renderer.',
    type: 'SelectSlotProps'
  },
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
    description: 'Content rendered before the trigger value. Receives the same shape\nas `#trigger` and `#suffix` (`SelectSlotProps`).',
    type: 'SelectSlotProps'
  },
  {
    name: 'suffix',
    description: 'Content rendered after the trigger value. Providing this slot\n**replaces the default chevron** — render your own fallback when\nyour slot content is conditional.',
    type: 'SelectSlotProps'
  },
  {
    name: 'item',
    description: 'Replaces the entire option row, including the row shell. A per-option\n`slot` (`#item-<name>`) takes precedence over this slot.',
    type: 'SelectItemSlotProps'
  },
  {
    name: 'empty',
    description: 'Fallback content rendered when no options are available.',
    type: 'any'
  },
  {
    name: 'footer',
    description: 'Content rendered below the option list. Stays pinned below the\nscrollable options. Receives the same shape as `#trigger`.',
    type: 'SelectSlotProps'
  },
  {
    name: 'item-prefix',
    description: 'Content rendered before the standard option label.',
    type: 'SelectItemSlotProps'
  },
  {
    name: 'item-label',
    description: 'Content rendered for the standard option label area.',
    type: 'SelectItemSlotProps'
  },
  {
    name: 'item-suffix',
    description: 'Content rendered after the standard option label.',
    type: 'SelectItemSlotProps'
  }
]

  const emitsData = [
  {
    name: 'update:open',
    description: 'Fired when the open state changes.',
    type: '[value: boolean]'
  },
  {
    name: 'update:modelValue',
    description: 'Fired when the model value changes.',
    type: '[value: SelectOptionValue | null | undefined]'
  }
]

  const exposedData = [
  {
    name: 'clear',
    description: 'Clears the current selection. It leaves the search query alone.',
    type: '() => void'
  },
  {
    name: 'focus',
    description: 'Moves focus to the component\'s interactive element.',
    type: '(options?: FocusOptions) => void'
  }
]
```
