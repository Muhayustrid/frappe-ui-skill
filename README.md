# frappe-ui skill

An agent skill for building UIs with [frappe-ui](https://ui.frappe.io) — the
Vue 3 component library behind Frappe Cloud, CRM, Helpdesk, HR, Learning,
Insights, Builder and Gameplan.

It exists because an agent that knows "use frappe-ui" still gets the details
wrong: `Menu` groups nest under `options` not `items`, `Slider` models
`number[]`, `PageHeader` has no `#suffix` slot, `dialog.alert` does not exist,
`List` columns are CSS grid tracks, and half the v0 names moved subpaths in
1.0. The skill carries the props, slots, tokens and page patterns, plus a
complete component index, so the first draft is already correct.

## Layout

```
skills/frappe-ui/
  SKILL.md   entry point: triggers, constants, where-to-look table
  INDEX.md   every exported name, grouped by export path
  CORE.md    tokens, page anatomy, per-component props/slots, charts, editor
  DATA.md    useCall / useDoc / useList / useDoctype / useNewDoc
  SETUP.md   Tailwind v3 + preset, subpaths, Vite plugin, CodeMirror peers
  evals/     24-case regression harness (invocation + API correctness)
```

## Install

One directory of Markdown, so any harness that reads agent skills can use it.

```bash
git clone https://github.com/Muhayustrid/frappe-ui-skill.git ~/frappe-ui-skill

ln -s ~/frappe-ui-skill/skills/frappe-ui ~/.agents/skills/frappe-ui          # DeepSeek Harness, Claude Code, Codex
ln -s ~/frappe-ui-skill/skills/frappe-ui ~/.claude/skills/frappe-ui          # Claude Code
ln -s ~/frappe-ui-skill/skills/frappe-ui ~/.codex/skills/frappe-ui           # Codex
ln -s ~/frappe-ui-skill/skills/frappe-ui ~/.config/opencode/skills/frappe-ui # opencode
```

DeepSeek Harness also discovers a project's `.agents/skills` root, and Claude
Code a project's `.claude/skills`, so a per-repo symlink works too.

## Version

Written against frappe-ui **1.x** (verified against `main` at 1.2.0). Apps still
pinning `frappe-ui@0.1.x` — many ERPNext apps do — do not have most of these
names; check `package.json` first.

## Licence

MIT, same as frappe-ui. Component APIs documented here belong to the
[frappe/frappe-ui](https://github.com/frappe/frappe-ui) project.
