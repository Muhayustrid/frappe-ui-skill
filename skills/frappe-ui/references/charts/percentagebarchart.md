<!-- Upstream: https://github.com/frappe/frappe-ui/blob/b435c66b51c0b19425a2a049b43e3ef2e308a8ab/src/charts/docs/PercentageBarChart.md -->

# PercentageBarChart

A bar that shows each category's share of a total.

It is the ring of `DonutChart` unrolled, for a strip under a number. It draws plain divs, with no echarts, so a chart ref hands back nothing.

## Share of a total

`category` and `value` name the two columns the bar needs, the same two as `DonutChart`. Each slice is as wide as its share of the total, and the biggest slice comes first. Hovering a slice shows its value, name and share.

`format` formats the values in the tooltip and the legend. The shares are whole numbers that add up to 100.



## Grouping the tail

`maxSlices` limits how many slices the bar draws, and "Others" counts as one of them. So `maxSlices` of 4 draws three named slices plus "Others", as in this example. It defaults to 9, as on `DonutChart`, so a breakdown is cut into the same slices on either chart. If there are no more categories than the limit, the bar draws them all with no "Others" slice. `select` reports the data rows behind the slice, and for "Others" that is every grouped row.



## Keeping the rows' order

The biggest slice comes first by default. `order="rows"` draws the slices in the order the rows arrive instead, for categories that already have an order, such as ageing buckets or months. `maxSlices` still keeps the largest slices, and "Others" comes last.

## Pinning a color

`colors` pins a color to a category, so a slice keeps its meaning whatever its size. Key it by the category value, and by `OTHERS_KEY` for "Others". A category grouped into "Others" takes that slice's color. The other slices keep the palette colors they would have had. A color can be a CSS variable, such as `var(--ink-red-5)`.

```vue
<PercentageBarChart
  :data="invoices"
  category="status"
  value="count"
  :colors="{ Overdue: 'var(--ink-red-5)', Paid: 'var(--ink-green-5)' }"
/>
```

## Hiding slices

`v-model:hiddenSlices` works as on `DonutChart`. Bind it to hide slices from your own code, or to keep hidden slices after a reload. When it is not bound, the legend manages the list. The remaining slices share the whole bar.

## API reference

```ts
const propsData = [
  {
    name: 'title',
    description: 'Heads the card. Left out, the chart draws no header row at all.',
    required: false,
    type: 'string'
  },
  {
    name: 'subtitle',
    description: 'A second line under the title, e.g. the period the numbers cover.',
    required: false,
    type: 'string'
  },
  {
    name: 'dir',
    description: 'Forces layout direction; defaults to document.documentElement.dir',
    required: false,
    type: 'ChartDir'
  },
  {
    name: 'loading',
    description: 'Draws the placeholder in place of the plot, for data still on its way.',
    required: false,
    type: 'boolean'
  },
  {
    name: 'error',
    description: 'Puts the chart in its error state and prints this message under it. Data\nthe chart cannot draw shows the empty state, not this one.',
    required: false,
    type: 'string | null'
  },
  {
    name: 'data',
    description: 'The rows to draw. One row is one slice, before the "Others" grouping.',
    required: true,
    type: 'Record<string, any>[]'
  },
  {
    name: 'category',
    description: 'Row key holding the slice name.',
    required: true,
    type: 'string'
  },
  {
    name: 'value',
    description: 'Row key holding the slice size.',
    required: true,
    type: 'string'
  },
  {
    name: 'maxSlices',
    description: 'How many slices the bar holds, "Others" included. Past that the bar keeps\nthe largest `maxSlices - 1` and sums the tail into a single "Others" slice,\nnamed `OTHERS_KEY`. Defaults to 9, as on `DonutChart`, so a breakdown cuts\ninto the same slices on either chart.',
    required: false,
    type: 'number'
  },
  {
    name: 'order',
    description: 'The order the slices go along the bar. `\'size\'` puts the biggest first.\n`\'rows\'` keeps the rows\' order, for categories that already have one, such\nas ageing buckets or months. Either way `maxSlices` keeps the largest, and\n"Others" comes last. Defaults to `\'size\'`, as on `DonutChart`.',
    required: false,
    type: 'SliceOrder'
  },
  {
    name: 'hiddenSlices',
    description: 'Slices the legend has switched off, by name. Bind it with\n`v-model:hiddenSlices` to drive the legend from the app. Left unbound, the\nlegend owns it.',
    required: false,
    type: 'string[]',
    default: '[]'
  },
  {
    name: 'format',
    description: 'Prints the tooltip and each slice\'s accessible name.',
    required: false,
    type: 'ChartValueFormatter'
  },
  {
    name: 'palette',
    description: 'Defaults to `\'categorical\'`: slices are unrelated categories, not steps.',
    required: false,
    type: 'ChartPalette'
  },
  {
    name: 'colors',
    description: 'Pins a color to a slice, keyed by its category value, e.g. `{ Overdue:\n\'var(--ink-red-5)\' }`. `OTHERS_KEY` pins "Others". A category grouped into\n"Others" takes that slice\'s color. The other slices keep the palette color\nthey would have had.',
    required: false,
    type: 'Record<string, string>'
  }
]

  const slotsData = [
  {
    name: 'loading',
    description: 'Replaces the whole placeholder, e.g. with a skeleton of the app\'s own.',
    type: 'any'
  },
  {
    name: 'error',
    description: 'Replaces the message, e.g. to put a retry button beside it.',
    type: '{ error?: string | null | undefined; }'
  },
  {
    name: 'empty',
    description: 'Replaces the "no data" line, e.g. with a hint about the filters.',
    type: 'any'
  },
  {
    name: 'actions',
    description: '',
    type: 'any'
  },
  {
    name: 'title-suffix',
    description: 'A mark right after the title, on the same line. The title truncates\naround it and it keeps its width. It renders in the title\'s font size, so\ncontent sized in `em` is smaller on a NumberCard than on a chart.',
    type: 'any'
  },
  {
    name: 'tooltip',
    description: 'Replaces the tooltip body. `items` holds the hovered slice alone. A named\nslice carries one row, and the "Others" slice every row it collapsed.',
    type: 'ChartTooltipSlotProps'
  }
]

  const emitsData = [
  {
    name: 'select',
    description: 'A slice was selected, by click or by Enter on the keyboard cursor. `name`\nidentifies the slice and `label` is what it printed. The collapsed tail is\nnamed `OTHERS_KEY` and carries every row it grouped, so a caller can drill\ninto it as well as into a named slice.',
    type: '[event: DonutSliceEvent]'
  },
  {
    name: 'update:hiddenSlices',
    description: 'The legend switched a slice off or back on. Carries the new list.',
    type: '[value: string[]]'
  }
]
```
