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
| 1    | `/phaser:plan`      | Fable 5                | Interview -> append Phase N to `implementation-plan.md` |
| 2    | `/phaser:propose`   | Fable 5                | `/opsx:propose` with ALL architectural decisions made up front |
| 3    | `/phaser:scrutinize`| Opus (latest)          | (after `/clear`) question the spec from multiple angles, item by item |
| 4    | `/phaser:apply`     | Sonnet + Opus advisor  | `/opsx:apply` — faithful execution; spec conflicts go to the `spec-advisor` subagent |
| 5    | `/phaser:review`    | Sonnet (latest)        | (after `/clear`) senior review of staged+unstaged changes, item by item |
| 6    | `/phaser:archive`   | Fable 5                | `/opsx:archive` + mark phase Complete |

Model pins use `claude-fable-5` explicitly and the `opus` / `sonnet` aliases,
which resolve to the newest model of each tier — so when new Opus/Sonnet
versions ship, the workflow upgrades automatically without editing pins.

`implementation-plan.md` (repo root of the app you're building) is the
append-only memory of the project: one numbered section per phase, with a
status line the commands keep updated (Planned -> Proposed -> Scrutinized ->
Implemented -> Reviewed -> Complete).

Prerequisite: OpenSpec's `/opsx` commands must be installed in the target
project.

## Install (on any machine)

```
/plugin marketplace add <your-github-username>/phased-iteration-skills
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
│       │   ├── review.md         # /phaser:review     (model: sonnet)
│       │   └── archive.md        # /phaser:archive    (model: claude-fable-5)
│       └── agents/
│           └── spec-advisor.md   # Opus advisor consulted by /phaser:apply
└── README.md
```

Additional plugins (skills, other command sets) get their own folder under
`plugins/` and an entry in `marketplace.json`.
