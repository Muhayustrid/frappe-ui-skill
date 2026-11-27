<!-- Upstream: https://github.com/frappe/frappe-ui/blob/b435c66b51c0b19425a2a049b43e3ef2e308a8ab/src/charts/docs/LineChart.md -->

# LineChart

Lines for a value that changes over time.

## Trends

Two series on a time axis, with the comparison series dashed. Use `dashed` for
a projection, or for a comparison that was not measured like the rest.

`dashed`, `smooth`, `showDataPoints`, `connectNulls` and `showDataLabels` are
chart props and `seriesConfig` keys both. The chart-level value is every
series' default, and a `seriesConfig` entry overrides it for one series, on or
off. The chart prop is the only way to set every series of a `splitBy` chart,
because the data names those series, not you.



`xAxis.format` prints the date where a value is read, such as the tooltip. It does not reach the tick labels of a time axis, which keep their levels: the year that opens a run of months, then the months.

## A numeric x axis

`xAxis.type: 'value'` treats the x column as numbers on a scale. Each point sits
at its own value, so points at 5 and 60 are spaced by the difference, and rows
draw in numeric order whatever order they come in.

The default is `'category'`, which gives every row equal space. A column of
dates becomes `'time'` automatically, but a column of numbers never becomes a
scale on its own, because category columns often hold numbers. A horizontal
`BarChart` ignores this setting, because each bar's size comes from its slot.
`horizontal` is a `BarChart` prop; a line chart does not have it.



## A second value axis

`y2` names the value columns to measure against a second value axis, drawn on
the opposite side. Use it for a measure in another unit, like a rate next to
dollars. Set `y2Axis.min` and `max` to fix that scale, so the line shows how far
it is over or under plan instead of its own trend. The axis is drawn only when
`y2` names a column.



`y` and `y2` form one list, in order: every `y` column draws and takes its
color first, then every `y2` column. The three column props pair with the three
axis options (`x`/`xAxis`, `y`/`yAxis`, `y2`/`y2Axis`), and a series uses the
scale of the prop that names it. A `y2` series draws with the component's
default type, and `seriesConfig[key].type` can change it like any other series.

`splitBy` splits `y` only. A `y2` column has one value per category, not per
group, so it draws as one series next to the ones `splitBy` creates, and
`maxSeries` limits only those.

## Filling one series

`seriesConfig[key].type` sets how one series draws. Set `type: 'area'` to fill
one line of a `LineChart` while the rest stay unfilled. There is no separate
fill prop, because an area is a filled line. The same key takes `'bar'`, for a
combo chart.



## Targets and thresholds

`referenceLines` draws a line across the plot at a fixed value. `axis` sets
which axis `value` belongs to: `'y'` (the default) or `'y2'` for a horizontal
line at a value, `'x'` for a vertical line at a category, a date, or a number on
a numeric x axis. Each line also takes an optional `label`, `color`
and `dashed`.

`labelPlacement` moves the label when it covers something. See
[BarChart](barchart.md#targets-and-thresholds).



A reference line is not a series. It has no legend entry, never shows in the
tooltip, and cannot be hidden, so a threshold always stays visible for
comparison. The value axis stretches to reach every line, so a threshold
outside the data stays in view. It goes up a tick when a line at its top end
leaves no room for the label.

## Context in the tooltip

`tooltipColumns` names columns that show only in the tooltip. They get no
mark, no legend entry, no color and no place on the value axis. Use it for a
number that does not belong on the same scale, such as the order count behind
a conversion rate.

```vue
<LineChart
  :data="data"
  x="month"
  :y="['conversion_rate']"
  :tooltip-columns="[{ name: 'orders', label: 'Orders' }]"
/>
```



Columns print after the series rows, in the order given. They are not sorted
by value, because a value in another unit cannot be compared with the series.

`name` is the row key. `label` defaults to it, and `format` formats the value.
A column of text prints as is.

For a custom tooltip, read `rows` from the `tooltip` slot instead.

```vue
<LineChart :data="data" x="month" y="conversion_rate">
  <template #tooltip="{ rows }">{{ rows[0].orders }} orders</template>
</LineChart>
```

## Hiding a series

A chart with more than one series draws a legend under the plot. Press an entry
to hide that line, or bind `v-model:hiddenSeries` to control that list
yourself. See
[BarChart](barchart.md#hiding-a-series).

## Gaps

Null values break the line, so a gap in the data shows as a gap.
`connectNulls` joins the line across them instead, on every series or on one
series through `seriesConfig`.



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
    description: 'The rows to plot. One row is one position on the category axis.',
    required: true,
    type: 'Record<string, any>[]'
  },
  {
    name: 'x',
    description: 'Column holding the category or time each point sits at.',
    required: true,
    type: 'string'
  },
  {
    name: 'y',
    description: 'Value column(s) measured against the primary value axis. A list reads wide\ndata: one series per column, drawn and colored in the order given.',
    required: true,
    type: 'string | string[]'
  },
  {
    name: 'y2',
    description: 'Value column(s) measured against the second value axis, for a measure in\nanother unit or magnitude. The axis is only drawn when this names a column,\nand it is ignored on a horizontal bar chart, which has no second value\naxis.\n\nThese series draw and take their palette slots after every `y` column, and\nthey draw as the chart component\'s own mark unless `seriesConfig[key].type`\nsays otherwise.\n\nWith `splitBy` the column is not split: it reads per category, so it must\nhold one value per `x` and the first row at each `x` is the one read. Rows\nthat disagree there warn in development.',
    required: false,
    type: 'string | string[]'
  },
  {
    name: 'splitBy',
    description: 'Splits `y` into one series per distinct value, i.e. long data. Use with a\nsingle `y`. A `y2` column is not split: it draws as one series of its own.',
    required: false,
    type: 'string'
  },
  {
    name: 'maxSeries',
    description: 'Caps how many series `splitBy` produces. The rest are summed\ninto a single "Others" series, keyed `OTHERS_KEY` so `seriesConfig` can\nstyle it. Uncapped by default, and ignored when `y` names the columns:\nthose the caller chose one by one.',
    required: false,
    type: 'number'
  },
  {
    name: 'seriesConfig',
    description: 'Keyed by series identity: a `y` or `y2` column, or a value of `splitBy`.',
    required: false,
    type: 'Record<string, SeriesStyle>'
  },
  {
    name: 'showDataLabels',
    description: 'Prints every series\' value beside its marks. A `seriesConfig` entry\noverrides it for one series, on or off.',
    required: false,
    type: 'boolean'
  },
  {
    name: 'smooth',
    description: 'Rounds the corners of every line instead of drawing straight segments. Line\nand area series. A `seriesConfig` entry overrides it for one series.',
    required: false,
    type: 'boolean'
  },
  {
    name: 'showDataPoints',
    description: 'Marks every datapoint with a dot, on every series. Line and area series. A\n`seriesConfig` entry overrides it for one series.',
    required: false,
    type: 'boolean'
  },
  {
    name: 'dashed',
    description: 'Breaks every series\' line into a dash. Line and area series. A\n`seriesConfig` entry overrides it for one series.',
    required: false,
    type: 'boolean'
  },
  {
    name: 'connectNulls',
    description: 'Bridges gaps left by nulls, on every series. Line and area series. A\n`seriesConfig` entry overrides it for one series.',
    required: false,
    type: 'boolean'
  },
  {
    name: 'hiddenSeries',
    description: 'Series the legend has switched off, by name. Bind it with\n`v-model:hiddenSeries` to drive the legend from the app, or to keep what a\nreader hid across a reload. Left unbound, the legend owns it.',
    required: false,
    type: 'string[]',
    default: '[]'
  },
  {
    name: 'tooltipColumns',
    description: 'Columns that reach the tooltip and nothing else: no mark, no legend entry,\nno palette slot, and no effect on the value axis. For context in another\nunit, such as the count behind a rate. They print after the series rows,\nin the order given: a value in another unit cannot be ranked among them.\n\nWith `splitBy` a column reads per category, so it must hold one value per\n`x` and the first row at each `x` is the one read. Rows that disagree there\nwarn in development.',
    required: false,
    type: 'ChartTooltipColumn[]'
  },
  {
    name: 'xAxis',
    description: 'The category axis: its title, how the `x` column reads, and label format.',
    required: false,
    type: 'ChartXAxisOptions'
  },
  {
    name: 'yAxis',
    description: 'The primary value axis: its title, its range, and how a value prints.',
    required: false,
    type: 'ChartValueAxisOptions'
  },
  {
    name: 'y2Axis',
    description: 'The second value axis. Only drawn when `y2` names a column.',
    required: false,
    type: 'ChartValueAxisOptions'
  },
  {
    name: 'palette',
    description: 'Ramp series colors are drawn from. Defaults to `\'sequential\'`, or to the\ncategorical ramp for a chart of two or more lines and no other mark.',
    required: false,
    type: 'ChartPalette'
  },
  {
    name: 'stacked',
    description: 'Series sum on top of each other. Bar and area series; a line never stacks.\n`\'normalized\'` reads each value as its share of the stack it sits in\ninstead of its own magnitude, and pins that value axis to 0-100.',
    required: false,
    type: 'boolean | "normalized"'
  },
  {
    name: 'referenceLines',
    description: 'Targets, thresholds and other fixed marks drawn over the plot. They are\nannotations, not series: no legend entry, and no way to switch one off.',
    required: false,
    type: 'ReferenceLine[]'
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
    description: 'Replaces the tooltip body. `items` holds one entry per visible series at\nthe hovered category, biggest first. `rows` holds the data row behind\nthem, so a replacement body can read a column the chart never plotted.',
    type: 'ChartTooltipSlotProps'
  }
]

  const emitsData = [
  {
    name: 'select',
    description: 'A mark was selected, by click or by Enter on the keyboard cursor. Carries\nthe series it belongs to, its value, and the row behind it.',
    type: '[event: ChartDatapointEvent]'
  },
  {
    name: 'update:hiddenSeries',
    description: 'The legend switched a series off or back on. Carries the new list.',
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
