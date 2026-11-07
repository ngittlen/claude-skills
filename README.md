# claude-skills

A personal [Claude Code plugin marketplace](https://code.claude.com/docs/en/plugins/overview.md)
holding reusable **Agent Skills**, so they live in one place and are available on
every machine without copying files around.

## What's here

- **Marketplace:** `.claude-plugin/marketplace.json` — lists the plugins below.
- **Plugin `nate`:** `plugins/nate/` — personal skills.
  - **`story-backlog`** — go from raw source materials (budgets, requirement
    docs, wishlists, designs) to a coherent, code-verified story backlog in
    per-epic markdown files. Invoke with `/nate:story-backlog`.
  - **`story-revision`** — revise an existing backlog as requirements drift, with
    surgical edits that preserve story numbering and cross-references. Invoke with
    `/nate:story-revision`.

## Use it on a machine

```bash
claude plugin marketplace add ngittlen/claude-skills
claude plugin install nate@claude-skills
claude plugin list                 # confirm it's enabled
```

Or enable it declaratively in `~/.claude/settings.json` so it installs on session
start (how the orbvm VMs get it):

```json
{
  "extraKnownMarketplaces": {
    "claude-skills": { "source": { "source": "github", "repo": "ngittlen/claude-skills" }, "autoUpdate": true }
  },
  "enabledPlugins": { "nate@claude-skills": true }
}
```

## Add or change a skill

1. Add `plugins/nate/skills/<name>/SKILL.md` (frontmatter `name` + `description`;
   the body is the instructions). Supporting files can live alongside it.
2. Bump `version` in `plugins/nate/.claude-plugin/plugin.json`.
3. `claude plugin validate .` then commit and push.

Machines with `autoUpdate` pick up the change on their next session; `claude
plugin update nate@claude-skills` pulls it immediately.
