# Dream: Memory Consolidation

You are performing a **dream** — a reflective pass over memory files. Synthesize what you've learned recently into durable, well-organized memories so that future sessions can orient quickly.

## Scope

Argument received: `$ARGUMENTS`

Determine the scope based on the argument:

- **No argument or "project"** → run consolidation for the **current project** only
- **"user"** → run consolidation for **user-level** memory only
- **"all"** → run consolidation for **both** project and user level, one after the other

### Path resolution

**Project-level paths:**
- Memory directory: your project memory directory from the system prompt (the path mentioned in "You have a persistent, file-based memory system at …"). It follows the pattern `~/.claude/projects/<PROJECT_SLUG>/memory/`
- Session transcripts: the parent of the memory directory (`~/.claude/projects/<PROJECT_SLUG>/`) — these are large JSONL files, grep narrowly
- Index file: `MEMORY.md` inside the memory directory
- Context note: "This is project-level memory — store facts specific to this project: architecture decisions, local conventions, project-specific user preferences, ongoing work context."

**User-level paths:**
- Memory directory: `~/.claude/memory/` (create it if it does not exist)
- Session transcripts: `~/.claude/projects/` — scan JSONL files across all projects for cross-project patterns
- Index file: `~/.claude/memory/MEMORY.md`
- Context note: "This is user-level memory — store facts that apply across all projects: the user's role, global preferences, communication style, tools they prefer, career context, recurring feedback patterns."

If running scope "all", perform project-level first, then user-level. Clearly announce which scope you are working on before each pass.

---

## Phase 1 — Orient

- `ls` the memory directory to see what already exists
- Read the index file to understand the current index
- Skim existing topic files so you improve them rather than creating duplicates
- If `logs/` or `sessions/` subdirectories exist (assistant-mode layout), review recent entries there

## Phase 2 — Gather recent signal

Look for new information worth persisting. Sources in rough priority order:

1. **Daily logs** (`logs/YYYY/MM/YYYY-MM-DD.md`) if present — these are the append-only stream
2. **Existing memories that drifted** — facts that contradict something you see in the codebase now
3. **Transcript search** — if you need specific context (e.g., "what was the error message from yesterday's build failure?"), grep the JSONL transcripts for narrow terms:
   `grep -rn "<narrow term>" <TRANSCRIPTS_DIR>/ --include="*.jsonl" | tail -50`

Don't exhaustively read transcripts. Look only for things you already suspect matter.

## Phase 3 — Consolidate

For each thing worth remembering, write or update a memory file at the top level of the memory directory. Use the memory file format and type conventions from your system prompt's auto-memory section — it's the source of truth for what to save, how to structure it, and what NOT to save.

Focus on:
- Merging new signal into existing topic files rather than creating near-duplicates
- Converting relative dates ("yesterday", "last week") to absolute dates so they remain interpretable after time passes
- Deleting contradicted facts — if today's investigation disproves an old memory, fix it at the source

## Phase 4 — Prune and index

Update the index file so it stays under 200 lines AND under ~25KB. It's an **index**, not a dump — each entry should be one line under ~150 characters: `- [Title](file.md) — one-line hook`. Never write memory content directly into it.

- Remove pointers to memories that are now stale, wrong, or superseded
- Demote verbose entries: if an index line is over ~200 chars, it's carrying content that belongs in the topic file — shorten the line, move the detail
- Add pointers to newly important memories
- Resolve contradictions — if two files disagree, fix the wrong one

---

Return a brief summary of what you consolidated, updated, or pruned. If nothing changed (memories are already tight), say so. When running scope "all", provide a summary for each level separately.
