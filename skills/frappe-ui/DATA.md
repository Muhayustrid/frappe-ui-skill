# Data fetching

Five composables, all exported from `frappe-ui`. They are siblings on one Frappe fetch layer, not wrappers around each other, so **each one has its own option list**. An option that exists on `useCall` is not automatically accepted by `useDoc`.

| Job | Composable |
|---|---|
| Any whitelisted method or REST path | `useCall` |
| A paginated, filterable list of a doctype | `useList` |
| One document, kept reactive | `useDoc` |
| Writes for a doctype, with no read | `useDoctype` |
| A draft of a document that does not exist yet | `useNewDoc` |

Prefer the matching `use*` composable for new code. The older `createResource` / `createListResource` / `createDocumentResource` family stays supported and un-deprecated through `1.x`, so existing code keeps working.

No Frappe backend behind the app? See [SETUP.md](SETUP.md) → Prototyping against a non-Frappe backend.

All five are named exports of the root package:

```ts
import { useCall, useList, useDoc, useDoctype, useNewDoc } from 'frappe-ui'
```

## `useCall`

**Options** (`src/data-fetching/useCall/types.ts:6-22`): `url` (string or `Ref<string>`), `method` (`GET | POST | PUT | DELETE`, default `GET`), `params` (object, or a function re-read on every request), `cacheKey`, `staleOnError` (default `false`), `immediate` (default `true`), `refetch` (default `false`), `baseUrl`, `initialData`, `beforeSubmit`, `transform`, `onSuccess`, `onError`.

**Returns a `reactive` object — read members directly, no `.value`:** `data` (`null` until the first successful response), `error`, `loading` / `isFetching`, `isFinished`, `params`, `url`, `promise` (resolves when the request settles; never rejects), `canAbort`, `aborted`, `execute()` / `fetch()` / `reload()`, `submit(params?)`, `reset()`, `abort()`.

**Read shape — auto-fetch on mount.** `immediate` defaults to `true`, so the request fires during setup. Set `refetch: true` when the `url` or `params` are reactive and the call should follow them.

```vue
<script setup lang="ts">
import { computed, ref } from 'vue'
import { useCall, Skeleton, ErrorMessage } from 'frappe-ui'

const userId = ref('Administrator')
const user = useCall<User>({
  url: computed(() => `/api/v2/document/User/${userId.value}`),
  refetch: true,
})
</script>

<template>
  <Skeleton v-if="user.loading && !user.data" class="h-4 w-40" />
  <ErrorMessage v-else-if="user.error" :message="user.error" />
  <UserCard v-else :user="user.data" />
</template>
```

**Write shape — `immediate: false` plus `await submit(params)`.** This is the write pattern for the whole file: nothing fires until you call `submit`, and the params for that one request are passed in. Always `await` it inside the handler and put the follow-up work at the await site; `onSuccess` is for side effects the caller does not sequence. The await only sequences under the `refetch: false` default — set `refetch: true` and `submit` returns before the request goes out.

```ts
const createTask = useCall<Task, { title: string; description?: string }>({
  url: '/api/v2/method/myapp.api.create_task',
  method: 'POST',
  immediate: false,
  onSuccess: (task) => {
    toast.success('Task created')
    router.push(`/tasks/${task.name}`)
  },
  onError: (err) => toast.error(err.message),
})

await createTask.submit({ title: form.title, description: form.description })
```

Bind `loading` to the submit button (`<Button :loading="createTask.loading">`) and render `error` through `<ErrorMessage :message="createTask.error" />` next to the form.

**`cacheKey` is read once at setup.** It persists the response in memory and IndexedDB, and a later call with the same key shows the cached value while it refetches. It is not reactive: a `Ref` inside the key is stringified, not unwrapped. For a call whose `url` varies by id, either leave `cacheKey` out or key the component itself per id, so the cache cannot serve the previous id's body.

## `useList`

```ts
const todos = useList<Todo>({ doctype: 'ToDo', fields: ['name', 'status'], filters: { status: 'Open' }, orderBy: 'modified desc' })
```

**Options** (`src/data-fetching/useList/types.ts:26-46`): `doctype` (required), `fields`, `filters` and `orderBy` (both `MaybeRefOrGetter` — pass a ref or getter and the list refetches when it changes), `start` (default `0`), `limit` (default `20`), `groupBy`, `parent`, `debug`, `cacheKey`, `staleOnError`, `initialData`, `immediate` (default `true`), `refetch` (**default `true`**, unlike `useCall`), `baseUrl`, `url`, `transform`, `onSuccess`, `onError`.

**Typing a reactive `orderBy`.** `orderBy` is typed as the template union `OrderBy`, but a computed template string infers plain `string` and fails type-check. Annotate the computed:

```ts
import { computed } from 'vue'
import type { OrderBy } from 'frappe-ui'
const orderBy = computed<OrderBy>(() => `${sortField.value} ${sortOrder.value}`)
```

**Returns:** `data` (the current page's rows, `null` until the first response unless `initialData` seeds it, so guard it before binding to `ListRows :items`), `error`, `loading` / `isFetching`, `isFinished`, `hasNextPage` / `hasPreviousPage`, `start` and `limit` (both **readonly** — page with `next()` / `previous()`, do not assign), `url`, `canAbort`, `aborted`, `execute()` / `fetch()` / `reload()`, `abort()`, `next()` / `previous()`, `updateRow(doc)` / `removeRow(name)`, plus three write helpers: `insert.submit(values)`, `setValue.submit({ name, ...values })`, `delete.submit({ name })`. Each helper carries its own `data`, `error`, `loading` and `isLoading(...)`.

## `useDoc`

```ts
const todoId = 'TODO-0001'
const todo = useDoc<Todo>({ doctype: 'ToDo', name: todoId })
await todo.setValue.submit({ status: 'Closed' })
```

**Options — this is the whole list** (`src/data-fetching/useDoc/useDoc.ts:36-44`): `doctype`, `name` (string, `Ref<string>` or getter), `baseUrl`, `url`, `methods`, `immediate` (default `true`), `staleOnError` (default `false`), `transform`.

There is no `cacheKey`, `refetch`, `params`, `initialData`, `onSuccess` or `onError` option on `useDoc`. Documents are shared through a reactive store instead: every `useDoc` and matching `useList` row for the same `doctype`/`name` reads the same object, and a write from any of them updates all of them.

**Returns:** `doc` — **not `data`** — plus `error`, `loading` / `isFetching`, `isFinished`, `canAbort`, `aborted`, `execute()` / `fetch()` / `reload()`, `abort()`, `setValue.submit(values)`, `delete.submit()`, `onSuccess(callback)` (registers a callback, returns an unsubscribe function), and one `useCall`-shaped member per entry in `methods`.

`methods` maps a member name to a server method name, or to `useCall` options plus a required `name`. Each member defaults to `method: 'POST'` and `immediate: false`, so you call it with `submit()`.

## `useDoctype`

`useDoctype(doctype, { baseUrl? })` groups the write operations for a doctype. It fetches nothing and holds no document.

**Returns:** `insert`, `delete`, `setValue`, `runDocMethod`, `runMethod`. Each has `data`, `error`, `loading`, `isLoading(...)` and `submit(params)`.

```ts
const todos = useDoctype<Todo>('ToDo')
await todos.setValue.submit({ name: id, status: 'Closed' })
await todos.runDocMethod.submit({ name: id, method: 'send_reminder' })
```

## `useNewDoc`

`useNewDoc(doctype, initialValues?, options?)` holds a reactive draft of a document that does not exist yet. `options` are the `useCall` options minus `url`, `method`, `params` and `immediate` — all four are fixed.

**Returns:** everything `useCall` returns for the insert request, plus `doc` (the reactive draft; bind form fields straight to it) and `submit()`, which inserts `doc` and resolves with the created document, including its server-assigned `name`.

```vue
<script setup>
import { useRouter } from 'vue-router'
import { useNewDoc, FormControl, Button } from 'frappe-ui'
const router = useRouter()
const todo = useNewDoc('ToDo', { status: 'Open' })
const save = async () => router.push(`/todos/${(await todo.submit()).name}`)
</script>

<template>
  <FormControl v-model="todo.doc.description" label="Description" required />
  <Button variant="solid" :loading="todo.loading" @click="save">Create</Button>
</template>
```

## Resources

Keep these factories in existing resource-based code. They use `auto` and
`cache`, not the composables’ `immediate` and `cacheKey`. For Frappe method
names, configure `setConfig('resourceFetcher', frappeRequest)` once before
creating resources. For a standalone HTTP API, `createResource` can use its
plain HTTP fetcher instead.

```js
import { createResource, createListResource, createDocumentResource } from 'frappe-ui'

const count = createResource({
  url: 'frappe.client.get_count', params: { doctype: 'ToDo' }, auto: true,
})
const todos = createListResource({ doctype: 'ToDo', fields: ['name', 'status'], auto: true })
const todo = createDocumentResource({ doctype: 'ToDo', name: 'TODO-0001' })
```

| Factory | Read data and state | Fetch or update |
|---|---|---|
| [`createResource`](references/data-fetching/resource.md) | `count.data`, `count.loading`, `count.error` | `await count.fetch()`; for mutations, use `await resource.submit(params)` on a resource configured for the write endpoint. Defaults to `auto: false`. |
| [`createListResource`](references/data-fetching/list-resource.md) | `todos.data`, `todos.list.loading`, `todos.list.error`, `todos.hasNextPage` | `await todos.next()` to append a page. For new filters, call `todos.update({ filters: { status: 'Open' } })` then `await todos.reload()`. To write, use `await todos.setValue.submit({ name, status: 'Closed' })` with the selected row’s `name`. Defaults to `auto: false`. |
| [`createDocumentResource`](references/data-fetching/document-resource.md) | `todo.doc`, `todo.get.loading`, `todo.get.error`, `todo.isDirty` | Fetches automatically by default. `await todo.setValue.submit({ status: 'Closed' })` updates fields; `await todo.save.submit()` submits changes made to `todo.doc`. |

## A full list page

Reads, writes, pagination and the three states in one shape. `useList` refetches
after every write by default, so the table updates itself; `data` is `null`
until the first response, which is why the template guards it.

```vue
<script setup lang="ts">
import { reactive } from 'vue'
import { Badge, Button, ErrorMessage, FormControl, Skeleton } from 'frappe-ui'
import { List, ListHeader, ListHeaderCell, ListRows, ListRow, ListCell } from 'frappe-ui/list'
import { useList } from 'frappe-ui'

type Ticket = { name: string; subject: string; status: string; modified: string }

const tickets = useList<Ticket>({
  doctype: 'Ticket',
  fields: ['name', 'subject', 'status', 'modified'],
  filters: { status: 'Open' },
  orderBy: 'modified desc',
  limit: 30,
})

const columns = ['minmax(0,1fr)', '8rem', '10rem']
const draft = reactive({ subject: '' })

async function create() {
  await tickets.insert.submit({ subject: draft.subject, status: 'Open' })
  draft.subject = ''
}
</script>

<template>
  <div class="p-5">
    <div class="mb-4 flex items-end gap-2">
      <FormControl v-model="draft.subject" label="New ticket" class="flex-1" />
      <Button variant="solid" :loading="tickets.insert.loading" @click="create">Add</Button>
    </div>

    <Skeleton v-if="tickets.loading && !tickets.data" class="h-40" />
    <ErrorMessage v-else-if="tickets.error" :message="tickets.error" />
    <p v-else-if="!tickets.data?.length" class="py-16 text-center text-ink-gray-5">
      No open tickets.
    </p>
    <List v-else :columns="columns" :row-height="44" divider="full">
      <ListHeader>
        <ListHeaderCell>Subject</ListHeaderCell>
        <ListHeaderCell>Status</ListHeaderCell>
        <ListHeaderCell class="justify-end">Modified</ListHeaderCell>
      </ListHeader>
      <ListRows :items="tickets.data" v-slot="{ item }">
        <ListRow :value="item.name">
          <ListCell>{{ item.subject }}</ListCell>
          <ListCell><Badge :label="item.status" /></ListCell>
          <ListCell class="justify-end">{{ item.modified }}</ListCell>
        </ListRow>
      </ListRows>
    </List>

    <div class="mt-3 flex justify-end gap-2">
      <Button :disabled="!tickets.hasPreviousPage" @click="tickets.previous()">Previous</Button>
      <Button :disabled="!tickets.hasNextPage" @click="tickets.next()">Next</Button>
    </div>
  </div>
</template>
```

Notes that matter here:

- `columns` is the CSS grid template shared by the header and rows, not a list
  of column definitions. The cell count and the track count must match.
- `ListHeaderCell` takes its label in the default slot and has no `align`
  prop; right-align with a class on the cell.
- Pagination is `next()` / `previous()`; `start` and `limit` are readonly.
- The row key `:value` is what selection and `v-model:active` report. Leave it
  off only when the list uses neither.
- `filters` and `orderBy` also accept a `ref` or getter; swap the static filter
  for one and the list refetches whenever its value changes.
- A `Link` / `User` field arrives as the stored `name`, not a display label;
  resolve the label yourself if the row needs one.
