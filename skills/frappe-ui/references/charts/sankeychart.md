<!-- Upstream: https://github.com/frappe/frappe-ui/blob/b435c66b51c0b19425a2a049b43e3ef2e308a8ab/src/charts/docs/SankeyChart.md -->

# SankeyChart

Shows flow from one set of nodes to another, with one band for each row.

## Flow between two columns

`source`, `target` and `value` name the three columns a flow needs, so the data
has one row per band. The nodes are all distinct values from the source and
target columns. A node that is the target of one row and the source of another
sits between them, which is how you draw a flow with several stages. `select`
reports the data row of the band.



## Vertical flow

`vertical` makes the flow run downwards, which suits a tall card or a graph
with few stages. `format` formats every value the chart shows, on the node
labels and in the tooltip. `nodeAlign` sets where a node with no outgoing flow
sits: `justify` puts it at the far end, and `left` and `right` put it at that
end.



## Flow runs one way

A sankey places its nodes in columns, which only works if the flow never loops
back. A row whose target already flows into its source (including a node linked
to itself) has no column to go in, so it is dropped, with a warning in
development. Group the data into stages before you plot it.

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
    description: 'One row per flow, i.e. one band from a source node to a target node.',
    required: true,
    type: 'Record<string, any>[]'
  },
  {
    name: 'source',
    description: 'Row key holding the node a flow leaves.',
    required: true,
    type: 'string'
  },
  {
    name: 'target',
    description: 'Row key holding the node a flow arrives at.',
    required: true,
    type: 'string'
  },
  {
    name: 'value',
    description: 'Row key holding how much flows along the link.',
    required: true,
    type: 'string'
  },
  {
    name: 'vertical',
    description: 'Flow runs top to bottom, in rows of nodes. Defaults to left to right.',
    required: false,
    type: 'boolean'
  },
  {
    name: 'nodeAlign',
    description: 'Where a node sits along the flow. Defaults to `\'justify\'`.',
    required: false,
    type: 'SankeyNodeAlign'
  },
  {
    name: 'format',
    description: 'Prints every number the flow shows, i.e. what a band or node carries.',
    required: false,
    type: 'ChartValueFormatter'
  },
  {
    name: 'palette',
    description: 'Defaults to `\'categorical\'`: nodes are unrelated categories.',
    required: false,
    type: 'ChartPalette'
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
    description: 'Replaces the tooltip body. `items` holds the hovered band or node alone.\nA node\'s `rows` is empty: it stands for every row through it, not one.',
    type: 'ChartTooltipSlotProps'
  }
]

  const emitsData = [
  {
    name: 'select',
    description: 'A band was selected, by click or by Enter on the keyboard cursor. Carries\nits two nodes and the row behind it. A node emits nothing: it stands for\nevery row that passes through it, not one.',
    type: '[event: SankeyLinkEvent]'
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
