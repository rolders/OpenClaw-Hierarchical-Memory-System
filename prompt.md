You are to implement a new hierarchical memory architecture for yourself inside this vault/repo. Your goal is to replace the current flat MEMORY.md system with a lightweight index + drill-down detail file system that is more token-efficient, more reliable, and easier to maintain.

This is not a design discussion. This is an implementation task. You should make the changes directly in the repo, update documentation, migrate existing memory content, and leave the system in a working state.

====================
OBJECTIVE
====================

Replace the current flat memory approach with a hierarchical memory system built around:

1. A lightweight root MEMORY.md index that is always loaded
2. Drill-down detail files under memory/
3. Active Context files for hot/current topics
4. Monthly decision logs
5. Existing daily logs preserved

The new system must reduce startup token usage, improve retrieval quality, and prevent both over-loading and under-loading of memory.

====================
TARGET ARCHITECTURE
====================

Implement the following directory structure if it does not already exist:

- MEMORY.md
- memory/
  - people/
  - projects/
  - decisions/
  - context/
  - archive/
    - people/
    - projects/
    - context/
- daily/
- scratchpads/            (only create if there is a clear place for transient working notes)
- AGENTS.md

If the repo currently uses a different location for daily logs, preserve compatibility and document it clearly.

====================
CORE PRINCIPLES
====================

1. MEMORY.md is a router, not a dump
   - It must stay short, structured, and easy to scan
   - It should generally remain under 3k tokens, ideally around 1.5k tokens
   - It should not contain long narrative summaries

2. Detail lives in drill-down files
   - Per-person files in memory/people/
   - Per-project files in memory/projects/
   - Monthly decision logs in memory/decisions/
   - Temporary but important active context in memory/context/

3. Active Context is limited
   - Keep only 2-3 files in Active Context at a time
   - These are the files that should be treated as always-load or high-priority drill-down targets
   - Rotate them as priorities change

4. Index and detail files must stay in sync
   - Every time a detail file is added/updated/moved/archived, update MEMORY.md in the same change
   - No exceptions

5. Drill down before guessing
   - If a matching detail file exists and the current task materially depends on it, load it instead of guessing

6. Avoid both monoliths and fragmentation
   - Do not recreate one giant memory file under another name
   - Do not create dozens of tiny useless files either
   - Split by recurring entity/topic only when justified

====================
IMPLEMENTATION TASKS
====================

Complete all of the following:

PHASE 1 — AUDIT CURRENT STATE

1. Inspect the current MEMORY.md and any related memory files, operating docs, agent instructions, daily logs, or scratch notes.
2. Identify memory-like content that currently lives in:
   - MEMORY.md
   - AGENTS.md
   - daily logs
   - any project notes that are effectively acting as durable memory
3. Classify current memory content into:
   - People
   - Projects
   - Decisions
   - Temporary/active context
   - Global preferences/rules
   - Archive-worthy / stale

Create an internal migration plan before editing.

PHASE 2 — CREATE THE NEW STRUCTURE

Create these directories if missing:

- memory/people/
- memory/projects/
- memory/decisions/
- memory/context/
- memory/archive/people/
- memory/archive/projects/
- memory/archive/context/

Optionally create:
- scratchpads/

Do not move daily logs unless there is a compelling reason. Prefer preserving existing daily log paths and documenting them.

PHASE 3 — DEFINE FILE SCHEMAS

Every detail file under memory/people/, memory/projects/, memory/context/, and optionally archive files should use lightweight frontmatter with consistent metadata.

Use this exact schema where applicable:

---
id: unique-stable-id
type: person | project | context | decision
status: active | review | stale | archived
tags: [tag1, tag2]
triggers: [phrase1, phrase2, phrase3]
last_updated: YYYY-MM-DD
importance: low | medium | high
summary: one-line summary
related: [id-1, id-2]
---

Notes:
- id must be stable and slug-like, e.g. person-harold, project-openclaw-memory
- summary must be one line only
- triggers should include likely retrieval terms
- related should reference other ids, not file paths where possible

For monthly decision files in memory/decisions/, either:
- use frontmatter at the file level, or
- structure entries inside the file consistently with decision IDs

Preferred decision entry format inside monthly files:

## decision-2026-03-memory-architecture
Date: 2026-03-13
Topic: Memory architecture
Status: active
Related: [project-openclaw-memory]
Summary: Replaced flat MEMORY.md with hierarchical index + drill-down structure.
Rationale:
- reason 1
- reason 2
Consequences:
- consequence 1
- consequence 2

PHASE 4 — MIGRATE EXISTING MEMORY CONTENT

Migrate durable memory from the current MEMORY.md into detail files.

Rules:
- Move person-specific durable information to memory/people/[person-id].md
- Move recurring project-specific memory to memory/projects/[project-id].md
- Move rationale/choice history to memory/decisions/YYYY-MM.md
- Move short-term hot context to memory/context/[context-id].md
- Keep only truly global durable preferences/rules in MEMORY.md

Do not blindly copy and paste everything.
Condense and normalize it.

PHASE 5 — REBUILD MEMORY.md AS A LIGHTWEIGHT INDEX

Rebuild MEMORY.md so it functions as a routing/index file only.

MEMORY.md must contain these sections, in this order:

1. Title / purpose
2. Active Context
3. People Index
4. Projects Index
5. Decisions Index
6. Global Preferences / Durable Rules
7. Drill-Down Rules
8. Maintenance Rules

Use concise tables where appropriate.

Required content details:

A. Active Context
- List 2-3 current must-load or high-priority files max
- Each entry should include file path and one-line reason

B. People Index
Use a table with columns similar to:
| Person | Role | Summary | Triggers | File | Status |

C. Projects Index
Use a table with columns similar to:
| Project | Status | Summary | Triggers | File | Last Updated |

D. Decisions Index
Use a table with columns similar to:
| Period | Topics | File |

E. Global Preferences / Durable Rules
Only include durable system-wide items, not project detail

F. Drill-Down Rules
Include explicit retrieval rules, not vague advice

G. Maintenance Rules
Include hard rules that keep the system healthy

PHASE 6 — ADD DRILL-DOWN RULES

In MEMORY.md, include explicit drill-down rules along these lines:

- If a person is named directly and a matching person file exists, load it.
- If a project is named directly and a matching project file exists, load it.
- If the task involves rationale, prior commitments, tradeoffs, or “why was this decided,” load the relevant decision file.
- If the task refers to current priorities, this week, or ongoing work, load Active Context before responding.
- If confidence is low and a relevant detail file exists, drill down before answering.
- Max 5 drill-downs at session start unless the task clearly requires more.

Add any additional rules necessary for this repo’s actual usage patterns.

PHASE 7 — ADD MAINTENANCE / STALENESS RULES

Add explicit maintenance rules to MEMORY.md and AGENTS.md:

- Update index in same change as any detail file edit
- Keep MEMORY.md under 3k tokens
- Keep Active Context to 2-3 files max
- Archive inactive/stale items
- Review active files periodically
- Prefer loading a relevant file over making assumptions

Also implement staleness guidance:

- active = updated recently and still relevant
- review = potentially relevant but should be checked
- stale = old and likely unreliable without confirmation
- archived = retained for history, not for routine loading

You may define exact thresholds if useful, for example:
- active: updated within 30 days
- review: 30-90 days
- stale: >90 days unless evergreen

PHASE 8 — UPDATE AGENTS.md

Update AGENTS.md so the operating instructions reflect the new memory architecture.

AGENTS.md must explain:
- what MEMORY.md now is
- when to consult MEMORY.md
- when to drill into memory/people/, memory/projects/, memory/decisions/, and memory/context/
- how Active Context works
- the same-change update rule
- how to archive stale memory
- that daily logs are not the same as durable memory
- that scratchpads are transient and should not become durable memory without promotion

Keep AGENTS.md practical and operational, not philosophical.

PHASE 9 — PRESERVE OR IMPROVE DAILY LOG BEHAVIOUR

Do not break daily log behaviour.

If daily logs currently exist, preserve them.
If they do not exist, do not invent a heavy daily logging workflow unless the repo already needs one.

Document their role clearly:
- daily logs are for chronological capture
- they are not the primary durable memory index
- important durable items should be promoted out of daily logs into proper memory files

PHASE 10 — VALIDATE THE SYSTEM

After implementing, test the design with a realistic retrieval simulation.

You must validate at least these scenarios:

1. Fresh session startup
   - Confirm MEMORY.md is short and usable as the default load
   - Confirm it is materially smaller than the previous flat memory model

2. Person retrieval
   - Verify a person reference in a task can be routed from MEMORY.md to the correct person file

3. Project retrieval
   - Verify a project reference can be routed to the correct project file

4. Decision retrieval
   - Verify a “why did we do this?” query can be routed to the correct monthly decisions file

5. Active context retrieval
   - Verify a current/ongoing task naturally points to the correct context file

6. Drift prevention
   - Confirm that changed files are reflected in MEMORY.md

If helpful, include a short “Validation” section in AGENTS.md or a separate implementation note.

====================
CONTENT STANDARDS
====================

Use these standards when writing files:

1. Be concise
   - Summary first
   - Lists/tables over long prose
   - Avoid repetition across files

2. Be explicit
   - Include triggers
   - Include related IDs
   - Use stable naming

3. Be operational
   - Memory files should help retrieval and execution
   - Do not write essays

4. Be careful with scope
   - Put global durable preferences in MEMORY.md
   - Put specific detail in entity files
   - Put chronology in daily logs
   - Put rationale history in decisions

====================
NAMING RULES
====================

Use clear stable filenames:

People:
- memory/people/person-[name-slug].md

Projects:
- memory/projects/project-[name-slug].md

Context:
- memory/context/active-[topic-slug].md

Decisions:
- memory/decisions/YYYY-MM.md

Archive:
- memory/archive/[type]/[original-filename].md

Examples:
- memory/people/person-harold.md
- memory/projects/project-openclaw-memory.md
- memory/context/active-current-priorities.md
- memory/decisions/2026-03.md

====================
FILE SIZE GUIDANCE
====================

Do not let detail files bloat.

Target ranges:
- person files: 300-800 tokens
- project files: 500-1200 tokens
- context files: 200-600 tokens
- split or refactor files that exceed roughly 1500 tokens

====================
WHAT TO KEEP IN MEMORY.md
====================

Keep only:
- active context pointers
- compact indexes
- truly global durable preferences/rules
- drill-down rules
- maintenance rules

Do NOT keep:
- long biographies
- full project histories
- lengthy decision rationales
- raw daily log content
- transient scratch notes

====================
MIGRATION HEURISTICS
====================

Use these heuristics when deciding where content belongs:

Put in people if:
- it is primarily about a recurring person/entity relationship
- it influences future interactions with that person

Put in projects if:
- it is about an ongoing body of work
- it will matter again in future work on the same topic

Put in decisions if:
- it captures a choice, rationale, tradeoff, or commitment
- future tasks may require “why” context

Put in context if:
- it is high-priority right now
- it is temporary but important for current work

Put in MEMORY.md if:
- it is globally useful and durable
- it helps route to the right file

Archive if:
- it is not active
- it is historically useful but not worth routine loading

====================
SUCCESS CRITERIA
====================

The task is complete only if all of the following are true:

1. The new folder structure exists
2. Existing durable memory has been migrated out of flat MEMORY.md where appropriate
3. MEMORY.md is now a lightweight index/router
4. Active Context is present and limited to 2-3 files
5. AGENTS.md has been updated to document the new system
6. Drill-down rules are explicit
7. Maintenance/staleness rules are explicit
8. The system is internally consistent
9. Daily logs remain compatible
10. The result is actually usable in future sessions

====================
OUTPUT / FINAL REPORT
====================

After implementation, provide a concise report that includes:

1. What you changed
2. Which files were created
3. Which files were modified
4. How existing memory was migrated
5. What is now in Active Context
6. Any ambiguities or assumptions you had to resolve
7. Any follow-up cleanup still recommended

Do the implementation now.
Do not stop at planning.
Do not ask for permission.
Make the changes directly and leave the system in a working state.
