<!-- Upstream: https://github.com/frappe/frappe-ui/blob/b435c66b51c0b19425a2a049b43e3ef2e308a8ab/src/charts/docs/CalendarHeatmapChart.md -->

# CalendarHeatmapChart

Shows a value for each day, with weeks as columns and weekdays as rows.

## A year of days

`date` and `value` name the day and the value of each row. The day is a `Date` or an ISO string such as `2024-03-01`. A time or offset after the date is ignored. A string is the day it spells, but `new Date('2024-03-01')` is UTC midnight, which west of Greenwich is the day before, so build a `Date` with `new Date(y, m, d)`. Days with no row are drawn empty, so gaps show. `format` formats the numbers in the tooltip and on the scale under the calendar.

`weekStart` sets the weekday at the top of each column, `sunday` to `saturday`. Month and weekday names follow the dayjs locale, as the tooltip does, and so does the week start when `weekStart` is not set: Monday in `de` or `en-gb`, Sunday in `en`. An app that stores its own setting, such as Frappe's `first_day_of_the_week`, passes it lowercased.



## Rows on the same day

The chart draws one row per day and does not combine them: of two rows on the same day, the later one is drawn, as on `HeatmapChart`. Sum, count or average per day before the rows reach the chart. `select` reports the day, its value, and the row behind it.

## Longer ranges

`start` and `end` set the days drawn. Each defaults to the first or last day in the data, rounded out to the whole month. Given both, the chart draws the range even when no row falls in it, because an empty year is a reading too. With no data and no range, it shows the empty state. A range of a year or less draws as one band. A longer range draws in bands of twelve months from its first month, so a fiscal year from April is one band, not two halves. Each band has its years beside it: `2024` for a calendar year, `2023–24` for one that crosses into the next. The cells shrink so every band fits the card. Every band lays out twelve whole months, so the months line up from band to band, and only the days in the range are drawn.



## Scale

`palette`, `min` and `max` work as on [HeatmapChart](heatmapchart.md). The scale covers the days in the range only. When no day is negative, it starts at 0. The ramp starts a step past its palest color, so every day with a row, 0 included, reads darker than a day without one.

One outlier can wash out the rest of the range: a day of 240 among days of 1 to 10 leaves every other day pale. Set `max` to where the ordinary days end. Days above it draw in the deepest color, and the scale reads `≥ 10`, so it does not claim that no day went higher. `min` works the same way at the low end.

The calendar always runs left to right, also in RTL. The header, scale and tooltip follow `dir`.

The calendar is one tab stop. Up and Down move a day, Left and Right a week, and Home and End go to the first and last day. Days with no row are reached as well and read as no data. Enter selects a day with data.

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
    description: 'The rows to plot, one per day. Of two on the same day, the later one is drawn.',
    required: true,
    type: 'Record<string, any>[]'
  },
  {
    name: 'date',
    description: 'Row key holding the day: a `Date`, or an ISO string such as `2024-03-01`.\nA time or offset after the date is ignored.',
    required: true,
    type: 'string'
  },
  {
    name: 'value',
    description: 'Row key holding the magnitude the day is colored by.',
    required: true,
    type: 'string'
  },
  {
    name: 'start',
    description: 'First day drawn, as a `Date` or an ISO string. Defaults to the first of the\nmonth the data starts in. Given with `end`, the range is drawn even with no\ndata in it.',
    required: false,
    type: 'string | Date'
  },
  {
    name: 'end',
    description: 'Last day drawn. Defaults to the last of the month the data ends in.',
    required: false,
    type: 'string | Date'
  },
  {
    name: 'weekStart',
    description: 'The weekday at the top of each column. Defaults to the week start of the\ndayjs locale, and to `\'sunday\'` when the locale names none.',
    required: false,
    type: 'CalendarHeatmapWeekStart'
  },
  {
    name: 'min',
    description: 'Bottom of the color scale. Defaults to 0 when no day is negative, else to\nthe smallest day in the range.',
    required: false,
    type: 'number'
  },
  {
    name: 'max',
    description: 'Top of the color scale. Defaults to the largest day in the range. Days\nabove it draw in the deepest color, and the scale reads `≥ max`.',
    required: false,
    type: 'number'
  },
  {
    name: 'format',
    description: 'Prints every number the calendar shows: the scale ends and the tooltip.',
    required: false,
    type: 'ChartValueFormatter'
  },
  {
    name: 'palette',
    description: 'Ramp days are colored from. Defaults to `\'sequential\'`; `\'diverging\'` is\nfor signed data and centers on zero.',
    required: false,
    type: 'HeatmapPalette'
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
    description: 'Replaces the tooltip body. `items` holds the hovered day alone, and\n`rows` the row behind it. A day with no row reads `No data`, with no row.',
    type: 'ChartTooltipSlotProps'
  }
]

  const emitsData = [
  {
    name: 'select',
    description: 'A day with data was selected, by click or by Enter on the keyboard cursor.\nCarries the day, its value and the row behind it.',
    type: '[event: CalendarHeatmapCellEvent]'
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
