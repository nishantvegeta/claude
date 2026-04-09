---
name: gitlab-get-issue
description: Fetches GitLab issues and saves them as Markdown FRS documents under raw_sources/. Annotates source_type and monolith signals for downstream INGEST processing.
mcp_servers:
  - mcp__gitlab
---

## Usage
`/get issue 123 from project 3` | `/show issues 123, 456, 789` | `/fetch issue 123 with links`

---

## Steps

### 1. Get project ID
From CLAUDE.md `gitlab_project_id`, or ask user.

### 2. Fetch issue
```
mcp__gitlab__get_issue(project_id, issue_iid)
```
Extract from JSON (never write raw JSON to disk):
`iid`, `title`, `description`, `state`, `labels[]`, `assignees[0].username` (prefix `@`), `milestone.title`, `created_at`, `updated_at`, `web_url`, `project_id` — format dates as `YYYY-MM-DD`.

### 3. Classify source type

| source_type | Signals |
|---|---|
| `frs` | Labels: `FRS`, `use-case`, `spec`, `requirements`; or description has Actors/Preconditions/Flow/Outcomes sections |
| `transcript` | Labels/title: `meeting`, `transcript`, `sync`, `standup`, `retro` |
| `contract` | Labels: `contract`, `SLA`, `integration`, `external`; or endpoint/SLA content |
| `architecture_doc` | Labels: `architecture`, `ADR`, `infra`; or topology/constraints content |

Default: `frs`

Derive `intended_nodes` from source type:

| source_type | intended_nodes |
|---|---|
| `frs` | ACT-, ENT-, CMD-, FLOW-, STATE-, DEC-, INT-, VM-, CAP- |
| `transcript` | SYN-, DEC- |
| `contract` | INT-, DEC- |
| `architecture_doc` | ARCH-, DEC- |

### 4. Monolith check

Count signals present in description:
- **Multiple actor roles** — more than 2 named actors with distinct goals
- **Multiple command triggers** — more than 4 distinct triggering events without shared precondition
- **Independent outcome sets** — success outcomes across sections with no mutual dependency
- **Disjoint state machines** — lifecycle rules for more than one entity type

**2+ signals → halt.** Do not write file. Output:
```
⚠️ MONOLITH DETECTED — Issue #{iid}: {title}
Signals: {list with evidence}
Suggested splits: {list}
File NOT written. Split issues before running /compiler ingest FRS-{ID}.
```

### 5. Build file content

**Case A — description already has valid FRS frontmatter** (`---` with `id:`, `milestone:`, `module:`):
Use description verbatim. Inject into existing frontmatter:
```yaml
source_type: {source_type}
intended_nodes: [ACT-, ENT-, ...]
monolith_risk: false
fetched_at: {YYYY-MM-DD}
```

**Case B — no FRS frontmatter (default):**
```markdown
---
id: FRS-UC-{zero-padded iid}
milestone: {milestone_title}
actor: {first actor in description, or blank}
goal: "{title}"
preconditions: []
success_outcomes: []
failure_outcomes: []
source_issue: {web_url}
source_type: {source_type}
intended_nodes: [ACT-, ENT-, ...]
monolith_risk: false
fetched_at: {YYYY-MM-DD}
---

{description verbatim}
```

### 6. Storage

| `len(file_content)` | Mode | Path |
|---|---|---|
| ≤ 6000 chars | Single file | `raw_sources/FRS-{ID}.md` |
| > 6000 chars | Partitioned | `raw_sources/entries/FRS-{ID}/` |

**Partitioned:** split by domain module (FRS) or method (source code). Always write `index.md` first with a partition table. Name files after modules (`approval-workflow.md`) or methods (`approveRequest.md`) — never generic `part-01-` names.

Every partition file and index carries the same `source_type`, `intended_nodes`, `monolith_risk`, `fetched_at` frontmatter keys.

### 7. Verify
```bash
head -5 raw_sources/FRS-{ID}.md   # or index.md for partitioned
```
Must start with `---`. If starts with `{` — delete and rewrite from Step 5.

### 8. Output summary
```
# Issue #{iid}: {title}
State: {state} | Labels: {labels} | Assignee: {assignee}
Milestone: {milestone_title} | Created: {created_at} | Updated: {updated_at}
Source type: {source_type} | Intended nodes: {intended_nodes}
Saved: raw_sources/FRS-{ID}.md  ← or entries/FRS-{ID}/ ({N} parts)

{description rendered as Markdown, or first 500 chars if partitioned}
```

### 9. Linked issues (optional)
If user says "with links": call `mcp__gitlab__list_issue_links(project_id, issue_iid)`, repeat Steps 2–8 for each, append `## Linked Issues` to summary.

---

## Anti-patterns

| ❌ | ✅ |
|---|---|
| Write raw JSON to `.md` | Build from template in Step 5 |
| Wrap existing FRS frontmatter in a second block | Inject keys into existing frontmatter (Case A) |
| Partition short content | Only partition when `len(file_content) > 6000` |
| Generic `part-01-` filenames | Module or method names |
| Skip verification | Always `head -5` after write |
| Write file when monolith signals ≥ 2 | Halt and output breakdown |
| Omit `source_type` / `intended_nodes` | Include in every file written |