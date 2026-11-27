<!-- Upstream: https://github.com/frappe/frappe-ui/blob/b435c66b51c0b19425a2a049b43e3ef2e308a8ab/src/charts/docs/ScatterChart.md -->

# ScatterChart

Plots two measures against each other, with one point for each row.

## Two measured axes

`x` and `y` name the two value columns. Both axes are numeric scales. A scatter
has no category axis and cannot be given one. Each scale follows the data
instead of starting at zero, so the points fill the plot. `label` names the
column that titles a point in the tooltip, and `select` reports the data row of
the point.

The `xAxis` title is drawn on its axis and the `yAxis` title above the plot, as
on the other axis charts.



## A third measure as size

`size` names a numeric column. Each point's diameter scales linearly with that
value, within a readable range. The scale covers the whole plot, not each group,
so a bubble size means the same thing everywhere. If every row has the same
value, all bubbles are drawn at the middle size, because there is no difference
to show and the smallest size would suggest they are the smallest values.

`splitBy` splits the rows into one group per distinct value, each with its own
color and legend entry. Press a legend entry to hide a group, or bind
`v-model:hiddenSeries` to control that list yourself.



## Quadrants and other reference lines

`referenceLines` draws a line across the plot at a fixed value, such as a
target, a threshold or a median. Each line takes a `value` and an optional
`label`, `color` and `dashed`. Reference lines are not series: they have no
legend entry or tooltip entry and cannot be hidden, so they stay visible when you
hide a group.

`labelPlacement` moves the label when it covers something. See
[BarChart](barchart.md#targets-and-thresholds).

`axis` sets which scale `value` belongs to. Both scales are numeric here, so
`'x'` and `'y'` both take a number. On an axis chart, `'x'` takes a category or
a date instead, unless `xAxis.type` is `'value'`. `'y'` (the default) draws a
horizontal line and `'x'` a vertical one. A scatter has no second value axis, so
`'y2'` is treated as `'y'`, with a warning in dev mode.

To split a scatter into quadrants, add one reference line per axis. There is no
`quadrants` prop, because a quadrant divider is a reference line, and the axis
charts already use `referenceLines` for that.



Each scale stretches to reach the lines on it, so a line outside the data stays
in view.

## Naming the points

`showDataLabels` prints each point's name beside it, as in the chart above, so
you can see which quadrant each product is in without hovering. It prints the
`label` column. If no `label` is set, nothing prints and you get a warning in
dev mode. It does not print the x or y value, because the axes already show
them.

Points often overlap, and so would their names. A name that overlaps a
neighbor is hidden, so a dense plot shows only a few names.

## Formatting

`format` formats every number the chart shows. `xAxis.format` and
`yAxis.format` override it for their own axis, for a chart whose two measures
are in different units. The size measure has no axis, so `format` formats it in
the tooltip.

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
    description: 'One row per point.',
    required: true,
    type: 'Record<string, any>[]'
  },
  {
    name: 'x',
    description: 'Row key holding the horizontal measure.',
    required: true,
    type: 'string'
  },
  {
    name: 'y',
    description: 'Row key holding the vertical measure.',
    required: true,
    type: 'string'
  },
  {
    name: 'size',
    description: 'Row key holding the magnitude each point is sized by.',
    required: false,
    type: 'string'
  },
  {
    name: 'splitBy',
    description: 'Splits the points into one series per distinct value.',
    required: false,
    type: 'string'
  },
  {
    name: 'hiddenSeries',
    description: 'Groups the legend has switched off, by name. Bind it with\n`v-model:hiddenSeries` to drive the legend from the app. Left unbound, the\nlegend owns it.',
    required: false,
    type: 'string[]',
    default: '[]'
  },
  {
    name: 'label',
    description: 'Row key holding the point\'s own name, which heads its tooltip.',
    required: false,
    type: 'string'
  },
  {
    name: 'showDataLabels',
    description: 'Prints each point\'s own name beside it, the way an axis series prints its\nvalue. The `label` prop names the column those come from; without it there\nis nothing to print, and a development build warns. A name that would\ncollide with its neighbour is dropped, so a dense cloud carries few.',
    required: false,
    type: 'boolean'
  },
  {
    name: 'xAxis',
    description: 'The horizontal scale. Both axes are value axes: a scatter has no categories.',
    required: false,
    type: 'ChartValueAxisOptions'
  },
  {
    name: 'yAxis',
    description: 'The vertical scale.',
    required: false,
    type: 'ChartValueAxisOptions'
  },
  {
    name: 'palette',
    description: 'Defaults to `\'categorical\'`: the groups are unrelated categories.',
    required: false,
    type: 'ChartPalette'
  },
  {
    name: 'referenceLines',
    description: 'Targets, thresholds and quadrant dividers drawn over the plot. They are\nannotations, not series: no legend entry, and no way to switch one off.\nBoth axes are measured here, so `axis: \'x\'` takes a number too — a pair of\nlines, one per axis, is what divides a scatter into quadrants.',
    required: false,
    type: 'ReferenceLine[]'
  },
  {
    name: 'format',
    description: 'Prints every number the chart shows. `xAxis.format` and `yAxis.format`\noverride it for their own axis; the size measure has no axis, so this is\nwhat prints it.',
    required: false,
    type: 'ChartValueFormatter'
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
    name: 'tooltip',
    description: 'Replaces the tooltip body. `items` holds the point\'s two measures, and\nits size when the chart draws one. `rows` holds the row behind the point,\nso a body can read a column the plot never drew.',
    type: 'ChartTooltipSlotProps'
  }
]

  const emitsData = [
  {
    name: 'select',
    description: 'A point was selected, by click or by Enter on the keyboard cursor. Carries\nboth measures and the row behind it.',
    type: '[event: ScatterPointEvent]'
  },
  {
    name: 'update:hiddenSeries',
    description: 'The legend switched a group off or back on. Carries the new list.',
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
