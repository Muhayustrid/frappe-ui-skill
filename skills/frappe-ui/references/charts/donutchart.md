<!-- Upstream: https://github.com/frappe/frappe-ui/blob/b435c66b51c0b19425a2a049b43e3ef2e308a8ab/src/charts/docs/DonutChart.md -->

# DonutChart

A ring that shows each category's share of a total.

## Share of a total

`category` and `value` name the two columns the ring needs. The center shows
the total, with `centerLabel` as its caption. Hovering a slice shows that
slice's value, name and share in the center instead.

`format` formats the numbers in the center and in the tooltip. The slice labels
show shares, not values, so `format` does not apply to them.



## Grouping the tail

`maxSlices` limits how many slices the ring draws, and "Others" counts as one
of them. So `maxSlices` of 4 draws three named slices plus "Others", as in this
example. If there are no more categories than the limit, the ring draws them all
with no "Others" slice. `select` reports the data rows behind the slice, and for
"Others" that is every grouped row.



## Keeping the rows' order

The biggest slice comes first by default. `order="rows"` draws the slices in the order the rows arrive instead, for categories that already have an order, such as ageing buckets or months. `maxSlices` still keeps the largest slices, and "Others" comes last.

## Pinning a color

`colors` pins a color to a category, so a slice keeps its meaning whatever its size. Key it by the category value, and by `OTHERS_KEY` for "Others". A category grouped into "Others" takes that slice's color. The other slices keep the palette colors they would have had. A color can be a CSS variable, such as `var(--ink-red-5)`.

```vue
<DonutChart
  :data="invoices"
  category="status"
  value="count"
  :colors="{ Overdue: 'var(--ink-red-5)', Paid: 'var(--ink-green-5)' }"
/>
```

## Half ring

`variant="half"` draws the ring as a semicircle, which fits a short card.
Ordered categories read better on the `sequential` ramp than on separate hues.

`showDataLabels` prints each slice's name and share next to the ring and
removes the center total, since the labels already show the shares.
`centerLabel` has no effect while it is on, and neither does `format` in the
center. The legend still shows below the plot, with each slice's share.



## Hiding slices

`v-model:hiddenSlices` works like `v-model:hiddenSeries` on an axis chart.
Bind it to hide slices from your own code, or to keep hidden slices after a
reload. When it is not bound, the legend manages the list. A donut draws slices,
not series, so it is the only chart with this name for the model.

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
    description: 'The rows to plot. One row is one slice, before the "Others" grouping.',
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
    description: 'How many slices the ring holds, "Others" included. Past that the ring keeps\nthe largest `maxSlices - 1` and sums the tail into a single "Others" slice,\nnamed `OTHERS_KEY`. Defaults to 9 — a ring stops being readable long before\nthe palette runs out.',
    required: false,
    type: 'number'
  },
  {
    name: 'order',
    description: 'The order the slices go round the ring. `\'size\'` puts the biggest first.\n`\'rows\'` keeps the rows\' order, for categories that already have one, such\nas ageing buckets or months. Either way `maxSlices` keeps the largest, and\n"Others" comes last. Defaults to `\'size\'`.',
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
    name: 'showDataLabels',
    description: 'Prints each slice\'s name and share beside the ring, and drops the readout\nin the middle. Off by default. The legend already names every slice and its\nshare, without the lines that tie a label back to its arc.',
    required: false,
    type: 'boolean'
  },
  {
    name: 'centerLabel',
    description: 'Caption under the total in the middle. Defaults to the `value` key.',
    required: false,
    type: 'string'
  },
  {
    name: 'variant',
    description: '`\'half\'` draws the ring as a semicircle; only the geometry changes.',
    required: false,
    type: 'DonutVariant'
  },
  {
    name: 'format',
    description: 'Prints the readout and the tooltip. The slice labels print shares.',
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
  },
  {
    name: 'echartOptions',
    description: 'Escape hatch: deep-merged into the echarts option the props built.',
    required: false,
    type: 'EchartOptionsOverride'
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
    name: 'center',
    description: 'Replaces the readout in the middle of the ring. Reads the total, or the\nhovered slice while one is hovered.',
    type: '{ label: string; value: number; formattedValue: string; percent?: number | undefined; }'
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

  const exposedData = [
  {
    name: 'chart',
    description: 'The echarts instance, once the plot has a size to initialise into.',
    type: 'EChartsType | undefined'
  }
]
```
