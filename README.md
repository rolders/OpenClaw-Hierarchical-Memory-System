# OpenClaw-Hierarchical-Memory-System

This is an updated & simplified version of https://github.com/ucsandman/OpenClaw-Hierarchical-Memory-System.


## Concise Summary

### Architecture

Replace a single large MEMORY.md with a hierarchical memory system:

MEMORY.md            ← lightweight index (always loaded)

memory/
  people/            ← per-person files
  projects/          ← per-project files
  decisions/         ← monthly decision logs
  context/           ← active priorities

daily/               ← chronological logs
scratchpads/         ← temporary notes

How It Works
	1.	Load MEMORY.md (~1–1.5k tokens)
	2.	Use it as a routing table
	3.	Drill down into specific files only when needed

Example flow:

User question
↓
Check MEMORY.md index
↓
Load relevant project/person file
↓
Answer with correct context

Key Design Features
	•	Active Context
2–3 files containing current priorities.
	•	Entity-based memory
Separate files for people, projects, and decisions.
	•	Metadata in each file
Enables reliable retrieval and linking.
	•	Explicit drill-down rules
Load relevant files before making assumptions.

Why This Change
	1.	Token efficiency
Startup load drops from ~5–10k tokens → ~1–1.5k.
	2.	Better context retrieval
Only load relevant memory instead of everything.
	3.	Prevents memory bloat
Information is organized by entity rather than dumped into one file.
	4.	Improves reasoning
Decisions, projects, and people are tracked separately.
	5.	Scales much better
Works even when the memory base becomes large.

### Concept

Think of it like a library:
	•	MEMORY.md → catalog
	•	memory/projects/ → bookshelves
	•	detail files → books

You read the catalog first, then open the specific book you need.

---
