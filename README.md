# phased-iteration-skills

A personal Claude Code plugin marketplace. Currently ships one plugin,
**phaser**, which implements a phased, spec-driven development workflow on top
of [OpenSpec](https://github.com/Fission-AI/OpenSpec) (`/opsx` commands).

## The workflow

Each phase of application development moves through six commands. Fresh-eyes
steps (3 and 5) are run right after `/clear` so scrutiny and review are cold
reads of the artifacts on disk, not echoes of the conversation that produced
them.

| Step | Command            | Model                  | What it does |
|------|--------------------|------------------------|--------------|
| 1    | `/phaser:plan`      | Fable 5                | Interview -> append Phase N to the plan file |
| 2    | `/phaser:propose`   | Fable 5                | `/opsx:propose` with ALL architectural decisions made up front |
| 3    | `/phaser:scrutinize`| Opus (latest)          | (after `/clear`) question the spec from multiple angles, item by item |
| 4    | `/phaser:apply`     | Sonnet + Opus advisor  | `/opsx:apply` — faithful execution; spec conflicts go to the `spec-advisor` subagent |
| 5    | `/phaser:review`    | Opus (latest)          | (after `/clear`) senior review of staged+unstaged changes, item by item |
| 6    | `/phaser:archive`   | Sonnet (latest)        | `/opsx:archive` + mark phase Complete |

Model pins use `claude-fable-5` explicitly and the `opus` / `sonnet` aliases,
which resolve to the newest model of each tier — so when new Opus/Sonnet
versions ship, the workflow upgrades automatically without editing pins.

The plan file (repo root of the app you're building) is the append-only
memory of the project: one numbered section per phase, with a status line the
commands keep updated (Planned -> Proposed -> Scrutinized -> Implemented ->
Reviewed -> Complete).

Plans can be **scoped**: `/phaser:plan ats 10` records Phase 10 in
`implementation-plan-ats.md` instead of the default `implementation-plan.md`,
so one repo can carry several independent phase sequences. Downstream
commands take the same optional scope, or find the right plan file by which
one references the OpenSpec change id; with a single plan file nothing
changes.

Prerequisite: OpenSpec's `/opsx` commands must be installed in the target
project.

## Install (on any machine)

```
/plugin marketplace add davekruse/phased-iteration-skills
/plugin install phaser@phased-iteration-skills
```

Restart Claude Code; the six `/phaser:*` commands should appear in the command
menu.

## Update

Push changes to this repo, then on each machine:

```
/plugin marketplace update phased-iteration-skills
/plugin update phaser
```

## Layout

```
phased-iteration-skills/
├── .claude-plugin/
│   └── marketplace.json          # the catalog Claude Code reads
├── plugins/
│   └── phaser/
│       ├── .claude-plugin/
│       │   └── plugin.json
│       ├── commands/
│       │   ├── plan.md           # /phaser:plan       (model: claude-fable-5)
│       │   ├── propose.md        # /phaser:propose    (model: claude-fable-5)
│       │   ├── scrutinize.md     # /phaser:scrutinize (model: opus)
│       │   ├── apply.md          # /phaser:apply      (model: sonnet)
│       │   ├── review.md         # /phaser:review     (model: opus)
│       │   └── archive.md        # /phaser:archive    (model: sonnet)
│       └── agents/
│           └── spec-advisor.md   # Opus advisor consulted by /phaser:apply
└── README.md
```

Additional plugins (skills, other command sets) get their own folder under
`plugins/` and an entry in `marketplace.json`.
