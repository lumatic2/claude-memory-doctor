> **⚠ DEPRECATED (2026-07-03)**: source of truth 는 `~/projects/custom-skills/memory-doctor/` 로 이관. 배포는 `bash setup.sh --skill memory-doctor`. 이 repo 는 삭제 가능(이력은 custom-skills 커밋에 보존).

# claude-memory-doctor

A [Claude Code](https://claude.ai/code) skill that audits and cleans up your auto memory (`MEMORY.md` + topic files).

## What it does

Claude Code auto memory accumulates over time. This skill checks for:

| Check | Description |
|-------|-------------|
| Line count | Warns before you hit the 200-line MEMORY.md limit |
| Dangling pointers | Links in MEMORY.md that point to deleted files |
| Duplicate links | Same file linked twice in the index |
| Orphan files | Topic files not registered in the index |
| Stale items | Project files untouched for 30+ days |
| CLAUDE.md overlap | Memory content that duplicates your rules files |
| Type misclassification | Instructions/rules stored in memory instead of CLAUDE.md *(Full mode only)* |

## Install

1. Copy `SKILL.md` into your Claude Code skills directory:

   **macOS / Linux**
   ```bash
   mkdir -p ~/.claude/skills/memory-doctor
   cp SKILL.md ~/.claude/skills/memory-doctor/
   ```

   **Windows**
   ```powershell
   New-Item -ItemType Directory -Force "$env:USERPROFILE\.claude\skills\memory-doctor"
   Copy-Item SKILL.md "$env:USERPROFILE\.claude\skills\memory-doctor\"
   ```

2. Restart Claude Code (or reload skills).

## Usage

```
/memory-doctor           # Quick audit (default)
/memory-doctor full      # Full audit including type misclassification check
memory 점검해줘           # Korean trigger also works
stale memory 있어?        # Stale-focused check
```

## Output format

```
── Global memory (~/.claude/projects/<id>/memory/) ──

PRI  Check                  Status
───  ────────────────────── ───────────────────────────────────────
P0   MEMORY.md line count   ⚠ 193/200 lines (97%)
P1   Orphan files           debugging.md — not in index
P2   Stale items            project_xyz.md — 2026-03-01 (62d) check status
✓    Dangling pointers      none
✓    CLAUDE.md overlap      none
```

After showing results, the skill proposes fixes and waits for your approval before making any changes.

## Modes

- **Quick** (default): Checks 1–6. Structural issues + CLAUDE.md overlap.
- **Full**: Quick + type misclassification detection (Check 7).

## Design notes

- Read/Glob only — no shell commands, no external calls
- Prompt injection resistant: file contents are treated as data only
- Per-item approval before any edit or delete
- Handles missing memory gracefully (explains auto memory isn't populated yet)
- Works across Windows, macOS, Linux — no hardcoded paths

## License

MIT
