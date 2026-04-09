---
title: Module and Milestone Registry
---

# Module & Milestone Registry

**Last Updated:** 2026-04-09  
**Status:** Greenfield (no modules defined yet)

## Active Modules

(None yet — created on first COMPILE operation)

## Milestone Roadmap

(None yet — milestones created as FRS documents are ingested)

## Module Template

When creating a new module via COMPILE:

```yaml
module_name: "Task Management"
description: "Core task lifecycle and scheduling"
bounded_context: ["task", "workflow", "assignment"]
dependencies: []
status: "design"  # design → review → approved → implemented → deprecated/closed
owner_team: "Backend"
created: "2026-04-09"
features: []  # FEAT- IDs linked here
```

## Milestone Template

When creating a new milestone:

```yaml
milestone: "M1"
description: "MVP core functionality"
target_date: "2026-06-01"
status: "open"  # open → review → closed
modules:
  - "Task Management"
  - "User Permissions"
features: []  # FEAT- IDs in this milestone
constraints: []
risks: []
```

---

## Related Files

- `home.md` — Full node catalog by milestone/module
- `snapshot.md` — Current system state
- `glossary.md` — Cross-role glossary index

