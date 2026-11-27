<!-- Upstream: https://github.com/frappe/frappe-ui/blob/b435c66b51c0b19425a2a049b43e3ef2e308a8ab/experimental/Calendar/Calendar.md -->

# Calendar

A date and event view for schedules, with Month, Week, Day, and Agenda modes.

> **Parked** — `Calendar` left the root export in `1.0.0` and now ships from
> [`frappe-ui/experimental`](../experimental.md) with its public API unchanged.
> It stays there, exempt from the deprecation policy, until a redesigned
> calendar family replaces it.

```ts
import { Calendar } from 'frappe-ui/experimental'
import type { CalendarEvent, CalendarConfig } from 'frappe-ui/experimental'
```

## Default

```vue
<script setup>
import { ref } from 'vue'
import { Calendar } from '..'

const config = {
  defaultMode: 'Month',
  isEditMode: true,
  eventIcons: {},
  enableShortcuts: false,
}

function getCurrentMonthYear() {
  const date = new Date()
  const year = date.getFullYear()
  const month = String(date.getMonth() + 1).padStart(2, '0')
  return `${year}-${month}`
}

const currentMonthYear = getCurrentMonthYear()

const events = ref([
  {
    title: 'English by Ryan Mathew',
    participant: 'Ryan Mathew',
    id: 'EDU-CSH-2024-00091',
    venue: 'CNF-ROOM-2024-00001',
    fromDate: currentMonthYear + '-02',
    toDate: currentMonthYear + '-02',
    fromTime: '16:30',
    toTime: '17:30',
    color: 'violet',
  },
  {
    title: 'English by Ryan Mathew',
    participant: 'Ryan Mathew',
    id: 'EDU-CSH-2024-00092',
    venue: 'CNF-ROOM-2024-00002',
    fromDate: currentMonthYear + '-04',
    toDate: currentMonthYear + '-04',
    fromTime: '13:30',
    toTime: '17:30',
    color: 'green',
  },
  {
    title: 'English by Sheldon',
    participant: 'Sheldon',
    id: 'EDU-CSH-2024-00093',
    venue: 'CNF-ROOM-2024-00001',
    fromDate: currentMonthYear + '-16',
    toDate: currentMonthYear + '-16',
    fromTime: '10:30',
    toTime: '11:30',
    color: 'blue',
  },
  {
    title: 'English by Ryan Mathew',
    participant: 'Ryan Mathew',
    id: 'EDU-CSH-2024-00094',
    venue: 'CNF-ROOM-2024-00001',
    fromDate: currentMonthYear + '-21',
    toDate: currentMonthYear + '-21',
    fromTime: '16:30',
    toTime: '17:30',
    color: 'pink',
  },
  {
    title: 'Frappe Meet with John',
    participant: 'John',
    id: '#htrht41',
    venue: 'Frappe Meet',
    fromDate: currentMonthYear + '-11',
    toDate: currentMonthYear + '-11',
    fromTime: '00:00',
    toTime: '02:00',
    color: 'amber',
    isFullDay: true,
  },
  {
    title: 'Frappe Meet with Sheldon',
    participant: 'Sheldon',
    id: '#htrht42',
    venue: 'Frappe Meet',
    fromDate: currentMonthYear + '-07',
    toDate: currentMonthYear + '-07',
    fromTime: '00:00',
    toTime: '02:00',
    color: 'amber',
    isFullDay: true,
  },
  {
    title: 'Offsite in Goa',
    participant: 'Everyone',
    id: '#offsite',
    venue: 'Goa',
    fromDate: currentMonthYear + '-09',
    toDate: currentMonthYear + '-13',
    color: 'cyan',
    isFullDay: true,
  },
  {
    title: 'Hackathon',
    participant: 'Engineering',
    id: '#hackathon',
    venue: 'HQ',
    fromDate: currentMonthYear + '-16',
    toDate: currentMonthYear + '-17',
    fromTime: '10:00',
    toTime: '18:00',
    color: 'orange',
  },
  {
    title: 'Release night',
    participant: 'Ops',
    id: '#release',
    venue: 'Online',
    fromDate: currentMonthYear + '-21',
    toDate: currentMonthYear + '-22',
    fromTime: '22:00',
    toTime: '02:00',
    color: 'violet',
  },
])
</script>

<template>
  <div
    class="w-full flex flex-wrap gap-3 items-center h-screen overflow-hidden"
  >
    <Calendar
      :config="config"
      :events="events"
      @create="(event) => console.log('createEvent', event)"
      @update="(event) => console.log('updateEvent', event)"
      @delete="(eventID) => console.log('deleteEvent', eventID)"
    />
  </div>
</template>
```

## Custom Header

Pass a `#header` slot to replace the default toolbar. The slot receives the
current title (`currentMonthYear`), the active view (`activeView`), the enabled
view options (`enabledModes`), and navigation functions (`increment`,
`decrement`, `updateActiveView`, `setCalendarDate`, `onMonthYearChange`).

```vue
<script setup>
import { ref } from 'vue'
import { Button, DatePicker, Select } from 'frappe-ui'
import { Calendar } from '..'

const config = {
  defaultMode: 'Month',
  isEditMode: true,
  eventIcons: {},
  enableShortcuts: false,
}

function getCurrentMonthYear() {
  const date = new Date()
  const year = date.getFullYear()
  const month = String(date.getMonth() + 1).padStart(2, '0')
  return `${year}-${month}`
}

const currentMonthYear = getCurrentMonthYear()

const events = ref([
  {
    title: 'English by Ryan Mathew',
    participant: 'Ryan Mathew',
    id: 'EDU-CSH-2024-00091',
    venue: 'CNF-ROOM-2024-00001',
    fromDate: currentMonthYear + '-02',
    toDate: currentMonthYear + '-02',
    fromTime: '16:30',
    toTime: '17:30',
    color: 'violet',
  },
  {
    title: 'English by Ryan Mathew',
    participant: 'Ryan Mathew',
    id: 'EDU-CSH-2024-00092',
    venue: 'CNF-ROOM-2024-00002',
    fromDate: currentMonthYear + '-04',
    toDate: currentMonthYear + '-04',
    fromTime: '13:30',
    toTime: '17:30',
    color: 'green',
  },
  {
    title: 'English by Sheldon',
    participant: 'Sheldon',
    id: 'EDU-CSH-2024-00093',
    venue: 'CNF-ROOM-2024-00001',
    fromDate: currentMonthYear + '-16',
    toDate: currentMonthYear + '-16',
    fromTime: '10:30',
    toTime: '11:30',
    color: 'blue',
  },
  {
    title: 'English by Ryan Mathew',
    participant: 'Ryan Mathew',
    id: 'EDU-CSH-2024-00094',
    venue: 'CNF-ROOM-2024-00001',
    fromDate: currentMonthYear + '-21',
    toDate: currentMonthYear + '-21',
    fromTime: '16:30',
    toTime: '17:30',
    color: 'pink',
  },
  {
    title: 'Frappe Meet with John',
    participant: 'John',
    id: '#htrht41',
    venue: 'Frappe Meet',
    fromDate: currentMonthYear + '-11',
    toDate: currentMonthYear + '-11',
    fromTime: '00:00',
    toTime: '02:00',
    color: 'amber',
    isFullDay: true,
  },
  {
    title: 'Frappe Meet with Sheldon',
    participant: 'Sheldon',
    id: '#htrht42',
    venue: 'Frappe Meet',
    fromDate: currentMonthYear + '-07',
    toDate: currentMonthYear + '-07',
    fromTime: '00:00',
    toTime: '02:00',
    color: 'amber',
    isFullDay: true,
  },
])
</script>

<template>
  <div
    class="w-full flex flex-wrap gap-3 items-center h-screen overflow-hidden"
  >
    <Calendar
      :config="config"
      :events="events"
      @create="(event) => console.log('createEvent', event)"
      @update="(event) => console.log('updateEvent', event)"
      @delete="(eventID) => console.log('deleteEvent', eventID)"
    >
      <template #header="headerProps">
        <div class="mb-2 flex items-center justify-between gap-3">
          <div class="flex items-center gap-2">
            <DatePicker
              :modelValue="headerProps.selectedMonthDate"
              @update:modelValue="(val) => headerProps.onMonthYearChange(val)"
              :clearable="false"
            >
              <template #trigger="{ setOpen, open }">
                <Button
                  variant="ghost"
                  class="text-lg-medium text-ink-gray-7"
                  :label="headerProps.currentMonthYear"
                  iconRight="lucide-chevron-down"
                  @click="setOpen(!open)"
                />
              </template>
            </DatePicker>
          </div>
          <div class="flex items-center gap-2">
            <Button
              variant="ghost"
              icon="lucide-chevron-left"
              @click="headerProps.decrement"
            />
            <Button
              label="Today"
              variant="ghost"
              @click="headerProps.setCalendarDate()"
            />
            <Button
              variant="ghost"
              icon="lucide-chevron-right"
              @click="headerProps.increment"
            />
          </div>
          <div>
            <Select
              class="!w-20"
              size="sm"
              variant="ghost"
              :options="headerProps.enabledModes"
              :modelValue="headerProps.activeView"
              @update:modelValue="(v) => headerProps.updateActiveView(v)"
            />
          </div>
        </div>
      </template>
    </Calendar>
  </div>
</template>
```

## Events

Each entry in `events` is a `CalendarEvent`:

```ts
{
  id: 'EV-001',
  title: 'Design review',
  participant: 'Jane Doe',
  venue: 'Room 1',
  fromDate: '2026-08-10',
  toDate: '2026-08-10',
  fromTime: '10:00',
  toTime: '11:00',
  color: 'blue',       // amber | violet | pink | cyan | blue | orange | green
  isFullDay: false,
}
```

An event runs from `fromDate fromTime` to `toDate toTime`, dates inclusive. One
whose `toDate` is later than its `fromDate` spans those days: the Month view
draws it as a single bar across them, and the Week view puts it in the all-day
row. A timed event that crosses midnight but is shorter than a day (an evening
running late) stays in the time grid, cut at midnight into a piece per day. A
timed event ending at `00:00` stops as that day begins, so it does not occupy
it. Dragging a spanning event moves both ends together.

`isFullDay` events ignore their times and cover `fromDate`..`toDate` whole.

The calendar keeps an internal copy of `events` and refreshes it when the prop
changes. Edits made inside the calendar (create, drag, resize, delete) mutate
the copy and come back through the `create`, `update`, and `delete` emits —
persist them and refresh your source of truth from there.

`CalendarColorMap` exports the color palette (`amber`, `violet`, `pink`, `cyan`,
`blue`, `orange`, `green`) with the CSS variables used per state, for building
matching UI such as a color picker.

## Month view

The Month view is a strip of week rows covering the month in view, and it
scrolls when the rows outgrow the calendar's height. The arrows, Today, and the
month picker move the month and scroll the strip to their date.

Each row is as tall as its busiest day needs. Multi-day events run as bars
across the top of the row; single-day events sit beneath them in their cells
with the title wrapping to a second line, so every event is shown — there is no
"n more".

Below the `sm` breakpoint it is the same grid a size down: shorter pills, a
tighter lane, and a cell that shows what fits in it with a `+n` count for the
rest — a full week trades its last lane of bars for those counts. Clicking a
date number at either width opens that day in the Day view.

`rangeChange` reports the strip's full extent for the Month view — the padding
days of the first and last weeks included — so a data source that fetches by
range has events for every cell.

## Agenda view

The Agenda view is three months as a list of days, each day a card: its name,
its date, how much is on it once that is more than one thing, then its events as
rows. A day with nothing on it is not listed — an empty row says nothing the
dates either side of it do not, and neither does a line counting how many were
skipped. Cards are grouped under the week they fall in, which replaces the month
dividers a flat list needs. Today's card carries a dot before its name; it, the
day before it and the day after say so in words beside their dates, and a day
already spent fades its header. An event that has ended is dimmed, the way a
pill in the grid is once its time has passed. An event under way says so with
its own tag.

The window covers the month in view and the two after it, padded out to whole
weeks at either end the way the Month view's strip is — the list groups its days
under the week they fall in, and a week is either listed or it is not. A single
month would be 31 days on the 1st and one day on the 31st, which is why it is
three. The arrows step a month at a time, so each move keeps two thirds of what
was on screen, and `rangeChange` reports exactly the span listed, padding
included, so a data source fetching by range agrees with it. The header names
the three months themselves, not the days the padding reaches into.

Rows have the room a grid pill does not, so they carry a description line and
tags. `Calendar` fills in the description itself — where the event is, and which
day of a stay the row is (`Day 2/3`) — and marks a draft as a pill does, an
outline in its colour on the plain ground rather than a tinted block. Where it stands against the clock
reads right after that: `Now` in blue while it runs and `Soon` in amber in the
hour before it, and nothing beyond that: an hour count would only restate the
time written beside it. Who is coming (`participant`) stands at the row's far
end, the one edge a card aligns on other than the time column, so the counts of
a day's rows line up — and the `#event-description` and `#event-suffix` slots
let you say the rest.

Drawn narrower than 640px — a phone, or a pane a sidebar has squeezed — the list
stacks. A day's name becomes a band over its rows rather than a column beside
them, and each row takes two lines: the title, its tags and who is coming on the
first, the time and the description on the second. The list measures its own
width for this, so a calendar in a narrow pane reads the same way a phone's
does.

```vue
<script setup>
import { ref } from 'vue'
import { Calendar } from '..'
import { Badge } from '../../../src/components/Badge'

const config = {
  defaultMode: 'Agenda',
  isEditMode: true,
  enableShortcuts: false,
}

// Days relative to today, so the story reads the same whenever it is opened —
// and so the "starts at today" rule has something on either side of the line.
function day(offset) {
  const date = new Date()
  date.setDate(date.getDate() + offset)
  const month = String(date.getMonth() + 1).padStart(2, '0')
  return `${date.getFullYear()}-${month}-${String(date.getDate()).padStart(2, '0')}`
}

const events = ref([
  {
    id: 'AG-1',
    title: 'Frappeverse Mock 2 – AV',
    venue: 'Hall 2',
    participant: '14 going',
    fromDate: day(0),
    toDate: day(0),
    fromTime: '12:00',
    toTime: '13:00',
    color: 'green',
  },
  {
    id: 'AG-2',
    title: 'Frappe Suite Standup',
    venue: 'Meet',
    fromDate: day(0),
    toDate: day(0),
    fromTime: '14:30',
    toTime: '15:30',
    color: 'green',
  },
  {
    id: 'AG-3',
    title: 'Untitled event',
    fromDate: day(0),
    toDate: day(0),
    isFullDay: true,
    isDraft: true,
    color: 'amber',
  },
  {
    id: 'AG-4',
    title: '1:1 with Faris',
    venue: 'Meet',
    fromDate: day(1),
    toDate: day(1),
    fromTime: '16:00',
    toTime: '16:30',
    color: 'amber',
  },
  // Nothing on days 2-3: the agenda collapses them into one line.
  {
    id: 'AG-5',
    title: 'Onsite sprint – Mumbai office',
    venue: 'BKC',
    fromDate: day(4),
    toDate: day(6),
    isFullDay: true,
    color: 'blue',
  },
  {
    id: 'AG-6',
    title: 'Fire Safety Training',
    participant: 'Invited · not responded',
    fromDate: day(4),
    toDate: day(4),
    fromTime: '13:45',
    toTime: '14:15',
    color: 'violet',
  },
])
</script>

<template>
  <div class="h-[600px]">
    <Calendar :events="events" :config="config">
      <!-- What the library cannot know: this app's own reading of an event. -->
      <template #event-suffix="{ calendarEvent }">
        <Badge
          v-if="calendarEvent.venue === 'Meet'"
          theme="blue"
          label="Remote"
          size="sm"
        />
        <Badge
          v-if="String(calendarEvent.participant).includes('not responded')"
          theme="amber"
          label="RSVP"
          size="sm"
        />
      </template>
    </Calendar>
  </div>
</template>
```

## Config

The `config` prop takes a partial `CalendarConfig`; unset keys use these
defaults:

```ts
{
  defaultMode: 'Month',   // 'Day' | 'Week' | 'Month' | 'Agenda'
  disableModes: [],       // views removed from the view switcher
  isEditMode: false,      // create / drag / resize / delete
  enableShortcuts: true,  // keyboard shortcuts (below)
  scrollToHour: 15,       // hour Week and Day views scroll to
  hourHeight: 50,         // pixel height of one hour row
  timeFormat: '12h',      // '12h' | '24h'
  weekends: ['sunday'],   // days shaded as weekend
  eventIcons: {},         // icons keyed by an event's `type`
  showIcon: true,         // show the eventIcons icon on cards
  noBorder: false,        // remove the outer grid border
}
```

`eventIcons` values are Vue components.

## Keyboard shortcuts

With `enableShortcuts` on: `m` / `w` / `d` / `a` switch views, `t` jumps to
today, `←` / `→` navigate, and `Delete` removes the event whose popover is open
(edit mode only).

They stay out of the way of whatever is on top: nothing fires while a field has
focus, or while a dialog, popover, menu or select is open anywhere on the page.

## Click handling

By default, a single click on an event opens its detail popover and a double
click opens the edit modal (edit mode only). Clicking an empty cell opens the
new-event modal in edit mode. Each behavior is replaceable with the `onClick`,
`onDblClick`, and `onCellClick` callback props — passing one turns the default
off for that interaction.

The popover's content is replaceable with the `#event-popover-content` slot,
which receives `{ calendarEvent, date, isEditMode, close }`.

The Agenda's rows take three more slots: `#event-description` for the line
under the title, `#event-suffix` for the tags beside it, and
`#event-participant` for the row's far end, where the event's `participant`
string is shown unless you fill it — with faces in front of the count, say. All
three receive `{ calendarEvent, date, description, timing }`, where
`description` and `timing` are what the calendar derived itself, so you can add
to them rather than work them out again. The suffix has no tags of its own to
hand you — it is yours to fill. Grid pills have no room for any of these and
ignore the slots.

`CalendarActiveEvent` exports the ref holding the id of the event whose popover
is open. Set it from outside to highlight an event, or clear it with an empty
string. The ref is module-level: every `<Calendar>` on the page shares it.

## Template ref

A template ref on `<Calendar>` exposes the calendar's state and navigation:

```vue
<Calendar ref="calendar" :events="events" />
```

```ts
const calendar = useTemplateRef('calendar')

calendar.value.setCalendarDate('2026-08-10') // jump to a date (today if omitted)
calendar.value.updateActiveView('Week') // switch the view
calendar.value.activeView // the visible view
calendar.value.increment() // move forward one day / week / month
calendar.value.decrement() // move back one day / week / month
calendar.value.reloadEvents() // re-sync the internal copy of `events`
calendar.value.currentMonthYear // formatted title, e.g. "August 2026"
calendar.value.currentYear // 2026
calendar.value.currentMonth // 7 (0 = January)
calendar.value.currentDay // day of month anchoring the view
calendar.value.enabledModes // view options not disabled via config (fixed at mount)
calendar.value.selectedMonthDate // the month picker's date, `YYYY-MM-DD`
calendar.value.onMonthYearChange // jump to a date and sync the month picker
```

## API reference

```ts
const propsData = [
  {
    name: 'events',
    description: 'Events to render. Each needs an `id`, a title, and date/time fields.',
    required: true,
    type: 'CalendarEvent[]',
    default: '[]'
  },
  {
    name: 'loading',
    description: 'Whether the events for the visible range are still on their way.\n\nOnly the Agenda reads it, and only to tell an empty list apart from one that\nhas not arrived: a grid with nothing in it still draws the days, where a list\nwith nothing in it is a blank panel, and saying "nothing on" of a range still\nbeing fetched is saying something that may not be true.',
    required: false,
    type: 'boolean'
  },
  {
    name: 'config',
    description: 'Behavior overrides, merged over the defaults.',
    required: false,
    type: 'Partial<CalendarConfig>',
    default: '{}'
  },
  {
    name: 'onClick',
    description: 'Replaces the default single-click behavior (opening the event\npopover) with your own handler.',
    required: false,
    type: '((data: { e: MouseEvent | KeyboardEvent; calendarEvent: CalendarEvent; }) => void)'
  },
  {
    name: 'onDblClick',
    description: 'Replaces the default double-click behavior (opening the edit\nmodal) with your own handler.',
    required: false,
    type: '((data: { e: MouseEvent | null; calendarEvent: CalendarEvent; }) => void)'
  },
  {
    name: 'onCellClick',
    description: 'Replaces the default cell-click behavior (opening the new-event\nmodal in edit mode) with your own handler.',
    required: false,
    type: '((data: CalendarCellClickData) => void)'
  }
]

  const slotsData = [
  {
    name: 'header',
    description: '',
    type: '{ currentMonthYear: string; currentYear: number; currentMonth: number; enabledModes: CalendarActionO'
  },
  {
    name: 'event-popover-content',
    description: '',
    type: '{ calendarEvent: CalendarEvent; date: Date; isEditMode: boolean; close: () => void; }'
  },
  {
    name: 'event-description',
    description: '',
    type: '{ calendarEvent: CalendarEvent; date: Date; description: string; timing: CalendarRowTag | null; }'
  },
  {
    name: 'event-suffix',
    description: '',
    type: '{ calendarEvent: CalendarEvent; date: Date; description: string; timing: CalendarRowTag | null; }'
  },
  {
    name: 'event-participant',
    description: '',
    type: '{ calendarEvent: CalendarEvent; date: Date; description: string; timing: CalendarRowTag | null; }'
  }
]

  const emitsData = [
  {
    name: 'delete',
    description: '',
    type: '[eventID: string | number | undefined]'
  },
  {
    name: 'create',
    description: '',
    type: '[event: CalendarEvent]'
  },
  {
    name: 'update',
    description: '',
    type: '[event: CalendarEvent]'
  },
  {
    name: 'rangeChange',
    description: '',
    type: '[payload: { view: CalendarMode; startDate: string; endDate: string; }]'
  }
]

  const exposedData = [
  {
    name: 'reloadEvents',
    description: 'Rebuilds the calendar\'s own copy of `events`. The calendar already does\nthis whenever `events` changes.',
    type: '() => void'
  },
  {
    name: 'currentMonthYear',
    description: 'The title of the visible range, e.g. "August 2026".',
    type: 'string'
  },
  {
    name: 'currentYear',
    description: 'The year of the visible month.',
    type: 'number'
  },
  {
    name: 'currentMonth',
    description: 'The visible month, `0` for January.',
    type: 'number'
  },
  {
    name: 'currentDay',
    description: 'The day of the month the view is anchored on.',
    type: 'number | null'
  },
  {
    name: 'enabledModes',
    description: 'The views that `config` does not disable. Read once, when the calendar\nmounts.',
    type: 'CalendarActionOption[]'
  },
  {
    name: 'activeView',
    description: 'The visible view.',
    type: 'CalendarMode'
  },
  {
    name: 'decrement',
    description: 'Moves back one day, week or month, depending on the view.',
    type: '() => void'
  },
  {
    name: 'increment',
    description: 'Moves forward one day, week or month, depending on the view.',
    type: '() => void'
  },
  {
    name: 'updateActiveView',
    description: 'Switches to this view.',
    type: '(value: CalendarMode, d?: Date, isPreviousMonth?: boolean, isNextMonth?: boolean) => void'
  },
  {
    name: 'setCalendarDate',
    description: 'Jumps to this date, or to today when no date is given.',
    type: '(d?: string | Date) => void'
  },
  {
    name: 'onMonthYearChange',
    description: 'Jumps to this date and moves the month picker to it.',
    type: '(val?: string | Date) => void'
  },
  {
    name: 'selectedMonthDate',
    description: 'The month picker\'s date, as `YYYY-MM-DD`.',
    type: 'string'
  }
]
```
