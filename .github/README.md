# AI instructions in itomProject

This folder contains the tooling-specific entry points. The **content** lives in
`AGENTS.md` files next to the code.

```
itomProject/
├─ AGENTS.md                       # single source of truth (tool independent)
├─ CLAUDE.md                       # stub -> AGENTS.md
├─ .github/
│  ├─ copilot-instructions.md      # stub -> AGENTS.md (always loaded by Copilot)
│  ├─ instructions/*.instructions.md   # path scoped rules (applyTo globs)
│  ├─ prompts/*.prompt.md          # reusable, invocable tasks
│  └─ skills/<name>/SKILL.md       # agent skills
├─ itom/AGENTS.md                  # + itom/CLAUDE.md, itom/.github/copilot-instructions.md
├─ plugins/AGENTS.md               # + plugins/CLAUDE.md, plugins/.github/copilot-instructions.md
└─ designerplugins/AGENTS.md       # + .../CLAUDE.md, .../.github/copilot-instructions.md
```

## Why the duplication?

`itom`, `plugins` and `designerplugins` are **independent git repositories**
(submodules). Instruction files are discovered relative to the *workspace root* that the
AI tool opens:

- opening `itomProject` → the top-level `AGENTS.md` / `.github/**` is used, and the
  `*.instructions.md` files add module specific rules via their `applyTo` globs
- opening a submodule alone → only that submodule's own files are found

Therefore the module rules are maintained **inside the submodule** and the files under
`.github/instructions/` only reference them. Keep them short so they do not drift.

## Conventions

- Tool independent content → `AGENTS.md` (supported by Copilot, Codex, Cursor, Jules, ...)
- `CLAUDE.md` and `copilot-instructions.md` are thin pointers, not copies
  (no symlinks — they are fragile on Windows/Git)
- `SKILL.md` uses the same format for Copilot (`.github/skills/`) and Claude
  (`.claude/skills/`); mirror the folder if you use Claude Code locally
- All files: end with a single newline, no trailing whitespace, no BOM (`pre-commit`)
