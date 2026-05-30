# Operator Samples — HAL-9001 Plugin Marketplace

A two-plugin Claude Code marketplace for the Operator Platform. Use it as
a hands-on tour of plugin installation and as a starting point for
building your own.

When you point the Operator Web App at this repository, the **Explore**
step finds the marketplace and shows both plugins in the picker:

- **HAL-9001** — minimal sample, one agent, one skill. **Start here.**
- **Discovery One** — advanced sample with schedules, sub-agents, and
  platform tools.

Useful primers from Anthropic:

- Plugins overview — https://code.claude.com/docs/en/plugins
- Plugin marketplaces — https://code.claude.com/docs/en/plugin-marketplaces

## Install

In the Operator Web App, open **Plugins → Install plugin**, paste the
repository URL, and click **Explore**. Pick a plugin from the picker, then
choose **Install as plugin (stay in sync)** to keep it linked to upstream.

```
https://github.com/optr-ai/samples-optr-plugin-hal-9001
```

## What's inside

### `plugins/hal-9001/` — minimal sample

The "hello world" of Operator plugins. One agent (`hal-9001`) wired to
one skill (`pod-bay-protocol`). Demonstrates the standard Claude Code
shape with no Operator-specific extensions.

```
.claude-plugin/plugin.json
agents/hal-9001.md
skills/pod-bay-protocol/SKILL.md
```

Read this one if you're new to Claude Code plugins — every file is short
and self-explanatory.

### `plugins/discovery-one/` — advanced sample

HAL-9001, but with operational capabilities the Operator Platform layers
on top of the spec:

```
.claude-plugin/plugin.json           manifest + plugin-wide operator defaults
agents/hal-9001.md                   coordinator with schedule + sub-agent + tool
agents/ae-35-diagnostics.md          sub-agent — returns clean JSON only
skills/pod-bay-protocol/SKILL.md     greeting + pod-bay Easter egg
skills/ae-35-protocol/SKILL.md       formats diagnostic JSON in HAL's voice
```

The coordinator's frontmatter shows the `operator:` block in action:

```yaml
operator:
  enableCaching: true
  enableAui: true
  enableAskUser: true
  subAgents: [ae-35-diagnostics]
  tools: [create_activity]
  schedules:
    - name: ae-35-pulse
      cron: "*/5 * * * *"
      timezone: UTC
      prompt: "Run AE-35 Unit Diagnostics."
```

After install, head to **Agents → hal-9001** to see the schedule, sub-
agent, and `create_activity` tool attached. Five minutes later, check
the Activity view for the first AE-35 report.

## Marketplace anatomy

```
samples-optr-plugin-hal-9001/
├── .claude-plugin/
│   └── marketplace.json     ← lists both plugins
├── plugins/
│   ├── hal-9001/            ← the minimal sample
│   └── discovery-one/       ← the advanced sample
├── README.md
└── LICENSE
```

`marketplace.json` is the entry point the Operator Platform reads when
exploring this repo. Each entry under `plugins[]` points at a subdir
containing a self-contained Claude Code plugin.

## The `operator:` namespace

The `discovery-one` plugin uses a single namespace key — `operator:` — at
the top of agent frontmatter and `plugin.json` to declare anything the
standard Claude Code spec doesn't model: capability flags, sub-agents,
schedules, platform tools, event subscriptions. Vanilla Claude Code
ignores unrecognised keys, so the same plugin runs in both contexts.

The full convention is documented in this repo's `README.md` and the
Operator docs. The short version:

| Where `operator:` lives | What goes in it |
|---|---|
| `.claude-plugin/plugin.json` | Plugin-wide defaults (e.g. `agentDefaults`) |
| `agents/<name>.md` frontmatter | Capability flags, sub-agents, schedules, tools, subscriptions |
| `skills/<name>/SKILL.md` frontmatter | Skill-scoped metadata (rare today) |

## Use this as a template

Fork or clone this repo and trim or extend whichever plugin matches your
needs. Common edits:

- Rename a plugin in `marketplace.json` and its `plugin.json`.
- Add or replace skills under each plugin's `skills/` directory.
- Add or replace agents under each plugin's `agents/` directory.
- For Discovery One: swap `ae-35-diagnostics` for another subsystem
  (Navigation, Life Support, Probability Projection, Monolith Analysis).
- Adjust the schedule cadence — `*/15 * * * *` for fifteen minutes, or
  `0 9 * * *` for a single daily briefing.
- Replace `create_activity` with another platform tool, or add more.

## License

MIT. See [LICENSE](./LICENSE).
