<!-- Upstream: https://github.com/frappe/frappe-ui/blob/b435c66b51c0b19425a2a049b43e3ef2e308a8ab/src/charts/docs/ChartContainer.md -->

# ChartContainer

Lays out everything around the plot: the title, the value-axis titles, the
three states and the legend row. Every built-in chart uses it, so wrap a plot
you draw yourself in it to match the built-in charts.

```js
import { ChartContainer } from 'frappe-ui/charts'
```

The default slot is the plot. It gets the space left after the title, axis
titles and legend.



## Title and actions

`title` names the chart and `subtitle` captions it. The row is only drawn when
one of them is set, or when something fills `#actions`.

`#actions` sits at the end of that row, opposite the title. Use it for a
period picker, a menu, or a link to the records behind the chart. It keeps its
width and the title truncates, because a truncated title is still readable but a
shrunk control is hard to press.

The header row is one title line tall. An action taller than that is centered on
the title instead of making the row taller.

`#title-suffix` goes right after the title text. Use it for an icon that
belongs to the title, such as a lock on a chart whose rows are filtered per
user. It keeps its width and the title truncates before it. It uses the title's
font size, so an icon sized in `em` scales with the title, which is smaller on a
`NumberCard`.

## Value-axis titles

`yAxisTitle` titles the primary value axis and `y2AxisTitle` the second one.
They are drawn as a row above the plot, not as echarts axis names, because an
axis name inside the plot has to be rotated to fit, which makes it hard to read.

Each title sits above the side its axis is on, so the row mirrors in RTL.
`axisTitlePlacement` moves the row: `'top'` by default, or `'bottom'` for a chart
whose value axis runs along the bottom. A horizontal `BarChart` uses `'bottom'`
and puts the titles at the far end, because the near end is above the category
labels, where a title would look like their heading.

The titles show only in the `ready` state, because there is no plot to title in
the other states.

## States

`loading`, `error` and `empty` switch the container between four states
(`ready` is the fourth). A non-empty `error` wins over `loading`, and `loading`
wins over `empty`.

The plot stays mounted in all four states. Unmounting it would dispose the
echarts instance and it would have to be built again, so it is hidden instead.

Each state has a slot that replaces its content: `#loading`, `#error` and
`#empty`. `#error` gets the message as a slot prop, so you can show a retry
button next to it. The loading placeholder fills the whole plot box, not a row
in the middle, so while a dashboard loads, each card keeps its shape in the
grid. See [States](states.md).

## The legend

`#legend` is a row under the plot. Put [`ChartLegend`](chartlegend.md)
in it. When the slot is empty, the container adds padding in place of the row, so a
chart with one series lines up with a chart that has several.

## Direction

`dir` sets the layout direction to `'ltr'` or `'rtl'`. It defaults to
`document.documentElement.dir`.

## API reference

```ts
const propsData = [
  {
    name: 'title',
    description: 'Heads the card. Left out, the container draws no header row at all.',
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
    name: 'yAxisTitle',
    description: 'Title of the primary value axis. The container draws it above the plot\nrather than along the axis, where it would have to be turned sideways.',
    required: false,
    type: 'string'
  },
  {
    name: 'y2AxisTitle',
    description: 'Title of the second value axis, drawn over the edge that axis sits on.',
    required: false,
    type: 'string'
  },
  {
    name: 'axisTitlePlacement',
    description: 'Edge of the plot the value-axis titles head. Defaults to the top.',
    required: false,
    type: 'AxisTitlePlacement'
  },
  {
    name: 'loading',
    description: 'Draws the placeholder in place of the plot, for data still on its way.',
    required: false,
    type: 'boolean'
  },
  {
    name: 'error',
    description: 'Non-empty switches the container into its error state.',
    required: false,
    type: 'string | null'
  },
  {
    name: 'empty',
    description: 'Draws the empty state: there is data, and it plots to nothing.',
    required: false,
    type: 'boolean'
  },
  {
    name: 'centered',
    description: 'Centres the plot and its legend in the height the card has to spare, for\na plot as tall as its content. Otherwise the plot takes that height.',
    required: false,
    type: 'boolean'
  },
  {
    name: 'dir',
    description: 'Forces layout direction; defaults to document.documentElement.dir',
    required: false,
    type: 'ChartDir'
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
    name: 'default',
    description: 'The plot itself, drawn into a box the container sizes and states.',
    type: 'any'
  },
  {
    name: 'legend',
    description: 'The row under the plot, e.g. a `ChartLegend` or a ramp scale.',
    type: 'any'
  }
]
```
