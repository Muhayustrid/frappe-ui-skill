# frappe-ui skill

An agent skill for building UIs with [frappe-ui](https://ui.frappe.io) — the
Vue 3 component library behind Frappe Cloud, CRM, Helpdesk, HR, Learning,
Insights, Builder and Gameplan.

It exists because an agent that knows "use frappe-ui" still gets the details
wrong: `Dropdown` groups nest under `options` not `items`, `Slider` models
`number[]`, `PageHeader` has no `#suffix` slot, `dialog.alert` does not exist,
`List` columns are CSS grid tracks, and half the v0 names moved subpaths in
1.0. The skill carries the props, slots, tokens and page patterns, plus a
complete component index, so the first draft is already correct.

## Layout

```
skills/frappe-ui/
  SKILL.md   entry point: version gate, job router, cross-component rules, checks
  INDEX.md   every exported name, grouped by export path, with import examples
  CORE.md    tokens, page anatomy, per-component props/slots, charts, editor
  DATA.md    data composables, resource factories, and a full list page
  SETUP.md   Tailwind v3 + preset, subpaths, Vite plugin, CodeMirror peers
  CATALOG.md links to offline usage guides and API references by topic
  references/ bundled upstream documentation (no web fetch needed)
  evals/     24-case regression harness (invocation + API correctness)
```

## Install

Link the entire skill directory so every harness can read its offline references.

```bash
git clone https://github.com/Muhayustrid/frappe-ui-skill.git ~/frappe-ui-skill

skill="$HOME/frappe-ui-skill/skills/frappe-ui"
for dir in "$HOME/.agents/skills" "$HOME/.dsh/skills" "$HOME/.claude/skills" \
  "${CODEX_HOME:-$HOME/.codex}/skills" "${XDG_CONFIG_HOME:-$HOME/.config}/opencode/skills"; do
  mkdir -p "$dir" || continue
  target="$dir/frappe-ui"
  if [ -e "$target" ] || [ -L "$target" ]; then
    printf 'Keeping existing installation: %s\n' "$target"
  else
    ln -s "$skill" "$target"
  fi
done
```

DeepSeek Harness (DSH) can also use a project's `.agents/skills` root, and
Claude Code can use a project's `.claude/skills`. For another harness, link the
same directory from its skills root. If a link already points to this checkout,
update the checkout once with `git -C ~/frappe-ui-skill pull --ff-only`, then
start a new session. No per-harness copy is needed.

## Version

Written against frappe-ui **1.x** (verified against `main` at 1.2.0). Apps still
pinning `frappe-ui@0.1.x` — many ERPNext apps do — do not have most of these
component names; SKILL.md step 1 gives the exact check and points at the
installed package's own source for those apps.

## Licence

MIT, same as frappe-ui. Component APIs documented here belong to the
[frappe/frappe-ui](https://github.com/frappe/frappe-ui) project.
