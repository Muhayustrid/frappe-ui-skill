<!-- Upstream: https://github.com/frappe/frappe-ui/blob/b435c66b51c0b19425a2a049b43e3ef2e308a8ab/src/components/Dialog/Dialog.md -->

# Dialog

A window over the page for a message, a form or a decision. To ask a quick
question from code without placing a `<Dialog>` in the template, use the
`dialog.*` functions.



## Examples

### Share a document

A rich body in `#default` with `Dropdown`s inside it, and an `#actions` row
with a status on the left and the main button on the right.

```vue
<script setup lang="ts">
// Share dialog — invite teammates by email and adjust per-row access.
// Rich `#default` body + a simple `#actions` slot CTA.
import { ref } from 'vue'
import { Avatar, Button, Dialog, Select, TextInput } from 'frappe-ui'

const open = ref(false)

type Role = 'view' | 'comment' | 'edit'
type Member = {
  name: string
  email: string
  initials: string
  image: string
  role: Role | 'owner'
  you?: boolean
}

const inviteEmail = ref('')

const avatarFor = (seed: string) => `https://i.pravatar.cc/80?u=${seed}`

const members = ref<Member[]>([
  {
    name: 'Faris Ansari',
    email: 'faris@example.com',
    initials: 'FA',
    image: avatarFor('faris@example.com'),
    role: 'owner',
    you: true,
  },
  {
    name: 'Ankush Menat',
    email: 'ankush@example.com',
    initials: 'AM',
    image: avatarFor('ankush@example.com'),
    role: 'edit',
  },
  {
    name: 'Shariq Ansari',
    email: 'shariq@example.com',
    initials: 'SA',
    image: avatarFor('shariq@example.com'),
    role: 'comment',
  },
])

const roleOptions = [
  { label: 'Can view', value: 'view' },
  { label: 'Can comment', value: 'comment' },
  { label: 'Can edit', value: 'edit' },
]

function invite() {
  const email = inviteEmail.value.trim()
  if (!email) return
  const local = email.split('@')[0]
  members.value.push({
    name: local.replace(/[._]/g, ' ').replace(/\b\w/g, (c) => c.toUpperCase()),
    email,
    initials: local.slice(0, 2).toUpperCase(),
    image: avatarFor(email),
    role: 'edit',
  })
  inviteEmail.value = ''
}
</script>

<template>
  <Button icon-left="lucide-share-2" @click="open = true">Share…</Button>

  <Dialog v-model:open="open" size="lg" title="Share &quot;Q4 Roadmap&quot;">
    <div class="flex flex-col gap-5">
      <div class="flex gap-2">
        <TextInput
          v-model="inviteEmail"
          type="email"
          placeholder="Add people by email…"
          class="flex-1"
          @keydown.enter="invite"
        />
        <Button variant="solid" :disabled="!inviteEmail" @click="invite">
          Invite
        </Button>
      </div>

      <div class="flex flex-col gap-1">
        <p class="text-p-sm text-ink-gray-5">People with access</p>
        <div class="flex flex-col">
          <div
            v-for="member in members"
            :key="member.email"
            class="flex items-center gap-3 py-1.5"
          >
            <Avatar :image="member.image" :label="member.initials" size="md" />
            <div class="min-w-0 flex-1 leading-tight">
              <div class="text-base text-ink-gray-9">
                {{ member.name }}
                <span
                  v-if="member.you"
                  class="ml-0.5 text-p-xs text-ink-gray-5"
                >
                  (you)
                </span>
              </div>
              <div class="truncate text-p-sm text-ink-gray-5">
                {{ member.email }}
              </div>
            </div>

            <span
              v-if="member.role === 'owner'"
              class="text-p-sm text-ink-gray-5"
            >
              Owner
            </span>
            <Select
              v-else
              v-model="member.role"
              :options="roleOptions"
              class="w-36 shrink-0"
            />
          </div>
        </div>
      </div>
    </div>

    <template #actions="{ close }">
      <div class="flex justify-end">
        <Button variant="solid" @click="close">Done</Button>
      </div>
    </template>
  </Dialog>
</template>
```

### New project setup

One dialog, three steps. The body, `title`, `dismissible` and the main button
change with the step, while the dialog itself opens only once.

```vue
<script setup lang="ts">
// Multi-step wizard inside one `<Dialog>` instance. The body content,
// `title`, primary CTA, and `dismissible` flag all swap with the
// current step — the dialog itself only animates in once.
import { computed, reactive, ref } from 'vue'
import { Button, Dialog, FormControl, TextInput } from 'frappe-ui'

const open = ref(false)
const step = ref(0)
const steps = ['Project', 'Team', 'Done'] as const

const form = reactive({
  template: 'roadmap',
  name: '',
  emails: '',
})

const templateOptions = [
  { label: 'Roadmap', value: 'roadmap' },
  { label: 'Sprint board', value: 'sprint' },
  { label: 'Wiki', value: 'wiki' },
]

const title = computed(() => {
  if (step.value === 0) return 'Set up your workspace'
  if (step.value === 1) return 'Invite your team'
  return 'You\'re all set'
})

const inviteCount = computed(
  () => form.emails.split(/[\s,;]+/).filter(Boolean).length,
)

function reset() {
  step.value = 0
  form.template = 'roadmap'
  form.name = ''
  form.emails = ''
}
</script>

<template>
  <Button variant="solid" @click="(reset(), (open = true))">Get started</Button>

  <Dialog
    v-model:open="open"
    size="lg"
    :title="title"
    :dismissible="step === steps.length - 1"
  >
    <div class="mb-5 flex items-center gap-1.5">
      <span
        v-for="(_, i) in steps"
        :key="i"
        class="h-1 flex-1 rounded-full transition-colors"
        :class="i <= step ? 'bg-surface-gray-10' : 'bg-surface-gray-3'"
      />
    </div>

    <div v-if="step === 0" class="flex flex-col gap-4">
      <FormControl
        label="Project name"
        v-model="form.name"
        placeholder="e.g. Q4 Roadmap"
        autofocus
      />
      <FormControl
        type="select"
        label="Template"
        v-model="form.template"
        :options="templateOptions"
      />
    </div>

    <div v-else-if="step === 1" class="flex flex-col gap-3">
      <p class="text-base text-ink-gray-7">
        Add teammates so they can jump in too. Paste a comma- or
        space-separated list of emails.
      </p>
      <TextInput
        v-model="form.emails"
        placeholder="jane@example.com, marco@example.com"
      />
      <p class="text-p-sm text-ink-gray-5">
        {{ inviteCount }}
        {{ inviteCount === 1 ? 'invite' : 'invites' }} ready.
      </p>
    </div>

    <div v-else class="flex flex-col gap-2">
      <p class="text-base text-ink-gray-8">
        {{ form.name || 'Your workspace' }} is ready to go.
      </p>
      <p class="text-p-sm text-ink-gray-5">
        We'll set up the
        {{ templateOptions.find((t) => t.value === form.template)?.label }}
        template{{
          inviteCount > 0
            ? ` and send ${inviteCount} ${
                inviteCount === 1 ? 'invite' : 'invites'
              }.`
            : '.'
        }}
      </p>
    </div>

    <template #actions="{ close }">
      <div class="flex w-full justify-between gap-2">
        <Button v-if="step > 0 && step < steps.length - 1" @click="step -= 1">
          Back
        </Button>
        <span v-else />

        <div class="flex gap-2">
          <Button v-if="step < steps.length - 1" @click="close">Skip</Button>
          <Button
            v-if="step < steps.length - 1"
            variant="solid"
            :disabled="step === 0 && !form.name.trim()"
            @click="step += 1"
          >
            {{ step === steps.length - 2 ? 'Finish' : 'Continue' }}
          </Button>
          <Button v-else variant="solid" @click="close">Done</Button>
        </div>
      </div>
    </template>
  </Dialog>
</template>
```

### Command palette

`bare` removes the padded card, the header and the action row, so the content
draws its own layout.

```vue
<script setup lang="ts">
// Command palette — the canonical reason to reach for `bare: true`.
// The visual is a floating search panel with input + list + hint footer,
// not a card with padding and a header, so we strip the auto-chrome and
// lay out the body directly. `Dialog.Title` keeps the heading accessible
// without rendering one.
import { computed, ref, watch } from 'vue'
import { Button, Dialog, KeyboardShortcut, TextInput } from 'frappe-ui'

const open = ref(false)
const query = ref('')
const activeIndex = ref(0)

type Command = {
  label: string
  hint: string
  icon: string
  combo?: string
}

const commands: Command[] = [
  {
    label: 'New project',
    hint: 'Create',
    icon: 'lucide-folder-plus',
    combo: 'Mod+N',
  },
  { label: 'New task', hint: 'Create', icon: 'lucide-plus', combo: 'Mod+T' },
  { label: 'Invite teammate', hint: 'People', icon: 'lucide-user-plus' },
  {
    label: 'Search documents',
    hint: 'Find',
    icon: 'lucide-file-search',
    combo: 'Mod+Slash',
  },
  {
    label: 'Open settings',
    hint: 'Workspace',
    icon: 'lucide-settings',
    combo: 'Mod+Comma',
  },
  { label: 'Switch theme', hint: 'Preferences', icon: 'lucide-moon-star' },
  { label: 'Log out', hint: 'Account', icon: 'lucide-log-out' },
]

const filtered = computed(() => {
  const q = query.value.trim().toLowerCase()
  if (!q) return commands
  return commands.filter((c) => c.label.toLowerCase().includes(q))
})

watch([query, open], () => {
  activeIndex.value = 0
})

function run(cmd: Command) {
  // eslint-disable-next-line no-console
  console.log('run:', cmd.label)
  open.value = false
}

function move(delta: number) {
  if (!filtered.value.length) return
  activeIndex.value =
    (activeIndex.value + delta + filtered.value.length) % filtered.value.length
}

function onEnter() {
  const cmd = filtered.value[activeIndex.value]
  if (cmd) run(cmd)
}
</script>

<template>
  <Button @click="open = true">
    <template #prefix>
      <span class="lucide-search size-4" />
    </template>
    Open command palette
  </Button>

  <Dialog v-model:open="open" size="lg" bare>
    <Dialog.Title as-child>
      <h2 class="sr-only">Command palette</h2>
    </Dialog.Title>

    <div class="flex flex-col">
      <ul class="max-h-80 overflow-y-auto p-2">
        <TextInput
          class="mb-2"
          v-model="query"
          size="md"
          placeholder="Type a command…"
          autofocus
          @keydown.down.prevent="move(1)"
          @keydown.up.prevent="move(-1)"
          @keydown.enter.prevent="onEnter"
          @keydown.esc="open = false"
        >
          <template #prefix>
            <span class="lucide-search size-4 text-ink-gray-5" />
          </template>
        </TextInput>

        <li
          v-if="!filtered.length"
          class="px-3 py-8 text-center text-p-sm text-ink-gray-5"
        >
          No commands match "{{ query }}"
        </li>
        <li
          v-for="(cmd, i) in filtered"
          :key="cmd.label"
          class="flex cursor-pointer items-center gap-3 rounded-5 px-3 py-2 text-base"
          :class="
            i === activeIndex
              ? 'bg-surface-gray-2 text-ink-gray-9'
              : 'text-ink-gray-7 hover:bg-surface-gray-2'
          "
          @mouseenter="activeIndex = i"
          @click="run(cmd)"
        >
          <span
            :class="[cmd.icon, 'size-4 text-ink-gray-6']"
            aria-hidden="true"
          />
          <span class="flex-1 truncate">{{ cmd.label }}</span>
          <span class="text-p-xs text-ink-gray-5">{{ cmd.hint }}</span>
          <KeyboardShortcut
            v-if="cmd.combo"
            :combo="cmd.combo"
            bg
          />
        </li>
      </ul>

      <div
        class="flex items-center gap-4 border-t border-outline-elevation-2 px-4 py-2 text-p-xs text-ink-gray-5"
      >
        <span class="flex items-center gap-1.5">
          <KeyboardShortcut combo="ArrowUp" bg />
          <KeyboardShortcut combo="ArrowDown" bg />
          Navigate
        </span>
        <span class="flex items-center gap-1.5">
          <KeyboardShortcut combo="Enter" bg />
          Select
        </span>
        <span class="ml-auto flex items-center gap-1.5">
          <KeyboardShortcut combo="Escape" bg />
          Close
        </span>
      </div>
    </div>
  </Dialog>
</template>
```

### Rename and delete from a list

`dialog.prompt` asks for the new name and checks it. `dialog.danger` confirms
the delete. Each button shows a spinner until its `onConfirm` finishes.

```vue
<script setup lang="ts">
// Rename and delete from a list without mounting a <Dialog> per row.
// `dialog.prompt` collects the new name, `dialog.danger` confirms the delete.
import { ref } from 'vue'
import { Button, dialog } from 'frappe-ui'

const projects = ref(['Website redesign', 'Mobile app', 'Q4 hiring'])

const wait = (ms: number) => new Promise((r) => setTimeout(r, ms))

function rename(index: number) {
  const current = projects.value[index]
  dialog.prompt({
    title: 'Rename project',
    fields: [
      {
        name: 'name',
        label: 'Name',
        type: 'text',
        required: true,
        defaultValue: current,
        validate: (value: string) =>
          projects.value.some((p) => p === value && p !== current)
            ? 'A project with this name already exists.'
            : null,
      },
    ],
    confirmLabel: 'Rename',
    onConfirm: async ({ values }) => {
      await wait(400)
      projects.value[index] = values.name
    },
  })
}

function remove(index: number) {
  dialog.danger({
    title: `Delete ${projects.value[index]}?`,
    message: 'Its tasks and files are deleted too. This cannot be undone.',
    onConfirm: async () => {
      await wait(400)
      projects.value.splice(index, 1)
    },
  })
}
</script>

<template>
  <div class="flex w-full max-w-md flex-col divide-y divide-outline-gray-1">
    <div
      v-for="(project, i) in projects"
      :key="project"
      class="flex items-center gap-2 py-2"
    >
      <span class="lucide-folder size-4 text-ink-gray-5" aria-hidden="true" />
      <span class="flex-1 text-base text-ink-gray-8">{{ project }}</span>
      <Button
        variant="ghost"
        icon="lucide-pencil"
        label="Rename"
        @click="rename(i)"
      />
      <Button
        variant="ghost"
        icon="lucide-trash-2"
        label="Delete"
        @click="remove(i)"
      />
    </div>
    <p
      v-if="!projects.length"
      class="py-4 text-center text-base text-ink-gray-5"
    >
      No projects left.
    </p>
  </div>
</template>
```

## Behavior

### Header

The header renders when there is a `title` or a `#title` slot. `message` is
the short paragraph under it, and screen readers read it out when the dialog
opens. Anything longer than a sentence belongs in `#default`, which replaces
`message`.

`icon` takes a `lucide-*` class name or a component. `theme` colors the badge
behind it: `amber`, `blue`, `red` or `green`, and gray when unset. A `theme`
without an `icon` shows no badge.

```vue
<Dialog title="Delete project" icon="lucide-alert-triangle" theme="red" />
```

### Parts for a bare dialog

`Dialog.Title`, `Dialog.Description` and `Dialog.Close` are the parts the
header and action row are built from. Use them inside `#default` when you draw
your own layout. A `bare` dialog has no header, so it needs `Dialog.Title` for
its accessible name.

```vue
<Dialog v-model:open="open" bare>
  <div class="p-6">
    <Dialog.Title class="text-2xl-semibold">Pick a workspace</Dialog.Title>
    <Dialog.Description>This is where new pages go.</Dialog.Description>
    <Dialog.Close as-child><Button label="Not now" /></Dialog.Close>
  </div>
</Dialog>
```

### Opening a dialog from code

`dialog.confirm`, `dialog.danger` and `dialog.prompt` open a dialog and return
right away. They need `<Dialogs />` somewhere in the app, which
`FrappeUIProvider` already mounts.

- **`dialog.confirm`** shows a message with a confirm and a cancel button.
  Pass `actions` for more than two buttons. Each action takes `Button` props
  and its own `onClick`. While one `onClick` runs, its button shows a spinner
  and the others are disabled. An error thrown from it shows inside the
  dialog.
- **`dialog.danger`** is `dialog.confirm` for actions that cannot be undone.
  It sets `theme: 'red'`, a warning icon and a "Delete" button label, and
  accepts everything `confirm` does.
- **`dialog.prompt`** asks for input through `fields`. Each field renders
  through `FormControl`, so any `FormControl` `type` works. A field's
  `validate` function runs after the `required` check and returns an error
  message, or `null` when the value is fine. Its second argument holds every
  field's value, so one field can check another. The first text or textarea
  field gets focus when the dialog opens, and Enter in a single-line field
  submits.

### Space above the dialog

`paddingTop` sets the space above the dialog. It takes a number of pixels or a
CSS length: `:padding-top="80"` or `padding-top="20vh"`.

## API reference

```ts
const propsData = [
  {
    name: 'open',
    description: 'Controls whether the dialog is open (v-model:open). Canonical.',
    required: false,
    type: 'boolean'
  },
  {
    name: 'modelValue',
    description: 'Controls whether the dialog is open (v-model). Also supported.',
    required: false,
    type: 'boolean'
  },
  {
    name: 'title',
    description: 'Dialog title. Renders the auto-header.',
    required: false,
    type: 'string'
  },
  {
    name: 'message',
    description: 'Description text rendered below the title.',
    required: false,
    type: 'string'
  },
  {
    name: 'icon',
    description: 'Icon shown next to the title in the auto-header. A `lucide-*` class name\nor a Vue component. Its badge takes its tone from `theme`.',
    required: false,
    type: 'string | Component'
  },
  {
    name: 'theme',
    description: 'Tone of the header icon badge: `amber`, `blue`, `red` or `green`.\nUnset renders the neutral gray badge.',
    required: false,
    type: 'DialogTheme'
  },
  {
    name: 'size',
    description: 'Max-width size of the dialog. Default `\'lg\'`.',
    required: false,
    type: 'DialogSize',
    default: '"lg"'
  },
  {
    name: 'position',
    description: 'Vertical placement. Default `\'center\'`.',
    required: false,
    type: 'DialogPosition',
    default: '"center"'
  },
  {
    name: 'paddingTop',
    description: 'Overrides the position-based top padding (escape hatch).',
    required: false,
    type: 'string | number'
  },
  {
    name: 'actions',
    description: 'Footer action buttons.',
    required: false,
    type: 'DialogAction[]'
  },
  {
    name: 'dismissible',
    description: 'Allow outside-click and Escape to close. Default `true`.',
    required: false,
    type: 'boolean',
    default: 'true'
  },
  {
    name: 'showCloseButton',
    description: 'Show the top-right close button. Default `true`.',
    required: false,
    type: 'boolean',
    default: 'true'
  },
  {
    name: 'bare',
    description: 'Drop the chrome: no padded card, no auto-header, no auto-actions. Default `false`.',
    required: false,
    type: 'boolean',
    default: 'false'
  }
]

  const slotsData = [
  {
    name: 'default',
    description: 'Main content rendered inside the padded card. Exposes `{ close }`.',
    type: 'DialogSlotProps'
  },
  {
    name: 'title',
    description: 'Title area; accepts arbitrary content (extra buttons next to title, etc.). Exposes `{ close }`.',
    type: 'DialogSlotProps'
  },
  {
    name: 'actions',
    description: 'Footer override; exposes `{ close, actions }`.',
    type: 'DialogActionsSlotProps'
  }
]

  const emitsData = [
  {
    name: 'update:open',
    description: 'Fired when the dialog open state changes via `v-model:open`.',
    type: '[value: boolean]'
  },
  {
    name: 'after-leave',
    description: 'Fired after the close animation finishes.',
    type: '[]'
  },
  {
    name: 'close',
    description: 'Fired when the dialog transitions to closed.',
    type: '[]'
  },
  {
    name: 'update:modelValue',
    description: 'Fired when the dialog open state changes via `v-model`.',
    type: '[value: boolean]'
  }
]
```
