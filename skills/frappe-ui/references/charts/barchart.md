<!-- Upstream: https://github.com/frappe/frappe-ui/blob/b435c66b51c0b19425a2a049b43e3ef2e308a8ab/src/charts/docs/BarChart.md -->

# BarChart

Bars for comparing categories, or a measure over time.

## Wide data

One `y` column per series. `xAxis.type` is inferred: a column of dates becomes
a time axis, anything else becomes categories. You pass functions to format the
axes and the tooltip. This example stores the chart as one typed object and
passes it with `v-bind`.



## Long data and stacking

Long data has one row per point, and `splitBy` names the column that splits
the rows into series. `stacked` sums those series into one column per
category.



## Shares instead of totals

`stacked="normalized"` draws a 100% stacked chart. Each value is drawn as its
share of its column, and the value axis is fixed at 0-100 so every column fills
the plot. `maxSeries` limits how many series `splitBy` creates. The rest are
added into one "Others" series, the same way `maxSlices` groups the smallest
slices of a donut. The chart keeps the largest series, measured over every x,
and keeps them in the order they appear in the data.



The data rows do not change, so the plot shows the share and the tooltip shows
both the share and the measured number. `yAxis.format` formats that number in
the tooltip. The axis ticks always show percentages, even if you set
`yAxis.format` or an `axisLabel.formatter` in that axis's `echartOptions`.

Shares are computed per stack. Two stacks with different `stackName`s each
reach 100, and a bar stack and an area stack are separate totals. A series that
stacks with nothing (a line, or a bar or area on its own) keeps its own values.
If its axis is fixed at 0-100, you get a warning in dev mode. Move it to `y2` to
give it its own scale. A `min` or `max` on a fixed axis is ignored, also with a
warning, because a column that stops short of the top no longer shows a whole.

`maxSeries` applies to `splitBy` only. A `y` list names each column, so no
column is dropped, and setting `maxSeries` gives a warning instead. The
"Others" series has the reserved key `__others__`, so a group that is really
named "Others" does not clash with it. A `seriesConfig.__others__` entry renames
or recolors it like any other series.

## Horizontal bars

`horizontal` moves the category axis to Y, so long labels stay readable.
`seriesConfig` sets the look of each series, keyed by series name. Here it
prints the value on each bar. `showDataLabels` on the chart prints them
on every series at once, and a `seriesConfig` entry overrides it for one.



## Labels that do not fit

The category axis measures its labels against the space available and shows
as much of each one as fits. If every label fits, nothing changes. If one does
not, the axis picks the layout that shows more text: flat and truncated in the
middle, or tilted to 45°, which uses the space below the axis. Wide slots stay
flat, because flat text is easier to read. Crowded slots tilt, where a flat
label would shrink to a few letters. All labels on the axis use the same layout,
because labels at two angles look like two axes.

A tilt is used only when it can label every category with space between the labels. When the slots are narrower than that, labels must be dropped in either layout, so the axis stays flat and labels every few categories instead, such as every seventh day of a month. The labels are spread evenly from the first category to the last, and each label is shown whole up to the length a tilted label can show.

A label from `xAxis.format` is measured and truncated the same way, in the middle.

There is no angle prop and no truncation length. The right angle changes with
the chart width, and the chart already knows the width and has measured the
text. A `horizontal` chart never tilts its labels: they are already one per line
down the side, so they are truncated to fit that column. A time axis never tilts
either. It picks its own ticks, and uses a larger interval instead of crowding
them.

## Counts

When every value on a value axis is a whole number, its ticks are whole numbers too, so a count of 2 never gets a tick at 0.5. This applies to `y`, `y2` and the value axis of a `horizontal` chart, each decided from its own series. Set `yAxis.integer` (or `y2Axis.integer`) to `true` or `false` to override it.

## A line among the bars

`seriesConfig[key].type` sets how one series draws: `'bar'`, `'line'` or
`'area'`. The other series use the component's default, so a `BarChart` with
one `'line'` series is a combo chart. Put a rate in `y2` instead of `y` to give
it its own scale.



The sequential ramp assigns colors by mark type, not by series order: lines
get the dark end and bars get the light colors. A bar is large enough to see in
a light color, but a 2px line in the same color disappears. The other palettes
keep series order, because the order of a custom list, a diverging ramp or a
categorical set has meaning.

Bars stack with bars and areas with areas. A line never stacks. `horizontal`
draws bars only. A series set to another type is drawn as a bar, with a warning
in dev mode.

## Targets and thresholds

`referenceLines` draws a line across the plot at a fixed value, such as a
quota, a break-even point or a release date. Each line takes a `value` and an
optional `label`, `color` and `dashed`. Reference lines are not series: they
have no legend entry and cannot be hidden.

`labelPlacement` moves the label when it covers something. It names an end of
the line and a side of it: `'end-top'` (the default), `'end-bottom'`,
`'start-top'` or `'start-bottom'`. The ends follow the direction of the axis the
line runs along, so an RTL chart swaps them. A vertical line has a rotated
label, and its two sides are left and right.



`axis` sets which axis `value` belongs to: `'y'` (the default) or `'y2'` for a
horizontal line at a value, `'x'` for a vertical line at a category or date.
`horizontal` swaps the two axes and the lines follow. The value axis stretches
to reach every line, so a target above the data stays in view, and goes up a
tick when a line at its top end leaves no room for the label.

## Context in the tooltip

`tooltipColumns` names columns that show only in the tooltip. They get no
mark, no legend entry, no color and no place on the value axis. See
[LineChart](linechart.md#context-in-the-tooltip).

## Hiding a series

A chart with more than one series draws a legend under the plot. Press an entry
to hide that series, and the value axis rescales to the series that are left.

Bind `v-model:hiddenSeries` to start with a series hidden, or to hide series
from your own control. It holds series keys: a `y` column name for wide data, a
value of the `splitBy` column for long data, or `__others__` for the "Others"
series. `LineChart`, `AreaChart` and
`ScatterChart` take the same model.

## Selecting a bar

`select` reports the series, the value and the data row of the bar, so you can
open the records behind it. It fires on click, and on Enter or Space when the
keyboard cursor is on the bar.



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
  },
  {
    name: 'horizontal',
    description: 'Bars run left-to-right; the category axis moves to Y. Bars only.',
    required: false,
    type: 'boolean'
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
