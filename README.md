# Planning Skills

Agent skills that take a feature from idea to tickets: data model, PRD, phased plan, and sized tickets. They work with Claude Code, Cursor, Codex, and other agents that support `SKILL.md` skills.

[![skills.sh](https://skills.sh/b/MdTehseenKhan/planning-skills)](https://skills.sh/MdTehseenKhan/planning-skills)

## Install

```bash
# Pick skills and agents interactively
npx skills add MdTehseenKhan/planning-skills

# Install everything
npx skills add MdTehseenKhan/planning-skills --all

# Install one skill
npx skills add MdTehseenKhan/planning-skills --skill create-prd

# List skills without installing
npx skills add MdTehseenKhan/planning-skills --list
```

Useful flags: `-g` installs globally instead of into the current project, `-a claude-code` targets one agent, and `-y` skips prompts. To pin a release, use `MdTehseenKhan/planning-skills@v1.0.0`.

## Skills

| Skill | What it does | Output |
| --- | --- | --- |
| [`design-data-model`](skills/design-data-model/SKILL.md) | Studies your existing schema (any ORM or SQL) and proposes tables, constraints, access rules, and a migration plan for a feature. | `data-model.md` |
| [`create-prd`](skills/create-prd/SKILL.md) | Turns a feature idea or brief into a Product Requirements Document with numbered user stories and prioritized requirements. | `prd.md` |
| [`plan-phases`](skills/plan-phases/SKILL.md) | Breaks a PRD into phases of vertical-slice items, each one ticket. | `plan.md` |
| [`plan-tickets`](skills/plan-tickets/SKILL.md) | Turns the plan into sized, risk-rated tickets with dependencies and a parallel build order, ready for Linear, Jira, or GitHub Issues. | `tickets.md` |

### Planning flow

The four skills chain together. Each one reads the files the earlier ones wrote:

```
design-data-model  →  create-prd  →  plan-phases  →  plan-tickets
data-model.md         prd.md         plan.md         tickets.md
```

Each skill works on its own, so install only the ones you want. You can start at any step, and you can skip `design-data-model` if the feature stores no new data. When a skill suggests a next step you haven't installed, it gives you the install command and carries on without it.

Each skill reads the relevant code first, asks only questions that change the output (at most two short rounds), and asks where to save before writing.

### Project knowledge

The skills are project-agnostic. They learn each project's conventions on the first run and save them in the project repo:

```
your-project/
└── .agents/knowledge/
    ├── project.md      # Save location, roles, tenancy, compliance, codebase map, issue tracker
    └── data-model.md   # Schema conventions: naming, keys, tenancy, access control, migrations
```

- **First run:** the skill reads `AGENTS.md` and `CLAUDE.md`. If those already cover a topic, nothing is written for it. For any topic they miss, the skill answers from the code, asks you about what's still unknown (one short round), and writes only those topics.
- **Later runs:** every skill reads these files first, so its output follows your project's conventions.
- **Commit them** so your team and their agents share the same context. Edit them freely; when they disagree with the code, the skill follows the code and points out the stale line.

The files live in your project, not in the skill folder, because `npx skills update` replaces skill folders.

## Usage

Ask in plain language, or run the skill by name:

```
/design-data-model  Users can save filtered views of the orders table and share them with their team
/create-prd         Use the brief in docs/saved-views/brief.md
/plan-phases        docs/saved-views/prd.md
/plan-tickets       docs/saved-views/plan.md
```

## Repository layout

```
skills/
└── <skill-name>/
    ├── SKILL.md        # Instructions and frontmatter (name, description)
    └── references/     # Templates and guides the skill reads
```

## Contributing

Issues and pull requests are welcome. Keep skills project-agnostic: a skill should discover each codebase's conventions instead of assuming a specific stack.

To test a change locally before opening a PR:

```bash
npx skills add ./ --list
mkdir -p /tmp/skills-test && cd /tmp/skills-test
npx skills add ~/path/to/planning-skills --skill <skill-name> -a claude-code -y
```
