# xunit-v3-migration — skill development repository

This repository is the **development home of a single Claude Code skill**: `xunit-v3-migration`, which migrates .NET test projects to xUnit v3 — from xUnit v2 or from NUnit. Work here is about improving the skill itself, not migrating anything in this repo.

## Layout

```
skills/                     # the skill content — what gets installed
├── SKILL.md                # entry point: frontmatter (name, description) + workflow
└── references/
    ├── api-changes.md      # v2→v3 namespace/type relocation tables, signatures
    ├── extensibility.md    # porting custom orderers, data attributes, frameworks
    └── nunit-migration.md  # NUnit→xUnit v3 attribute/lifecycle/assertion mapping
README.md                   # human-facing description
INSTALL.md                  # how to install globally or per-project
```

Installed copies (e.g. `<project>/.claude/skills/xunit-v3-migration/`) are snapshots of `skills/`. When a migration run in some project surfaces a fix or lesson, apply it **here** and re-install; never let an installed copy drift ahead silently.

## Editing rules for the skill

- **The skill must be self-contained.** Running it must never require ad-hoc web searches. Every "check/verify X" instruction must come with a concrete local procedure: `curl` against the NuGet flat-container API for nuspec dependencies, grepping installed package DLLs under `~/.nuget/packages`, or a runtime smoke test (e.g. the deliberate-failure assertion check). If a fact can't be checked locally, embed the fact itself in `references/` at authoring time.
- **SKILL.md is the workflow; references hold the detail.** Keep SKILL.md lean (target well under 200 lines): the per-project loop (inventory → baseline → packages/csproj → code fixes → verify), the package table, and pointers into `references/` for signatures and deep API detail. New API tables and code examples go in references, not SKILL.md.
- **The frontmatter `description` controls triggering.** It deliberately lists many phrasings ("migrate", "upgrade xunit", "move to the new xunit", effort assessment). If you change it, keep it pushy — skills tend to under-trigger.
- **Every claim should be traceable** to the official guides (https://xunit.net/docs/getting-started/v3/migration and .../migration-extensibility) or to a verified migration run. Don't add speculative API signatures; when unsure, instruct the skill user to let the compiler verify (that's the existing pattern in `references/extensibility.md`).
- **Preserve the safety invariants.** Whatever else changes, the skill must keep: the pre-migration baseline test count, the count comparison after migration (silent test loss is the #1 failure mode), the deliberate-failure check for runtime-binding assertion libraries (with rebuild after revert), and the environmental-vs-migration failure triage (TRX classification + port probing).

## Testing changes

There is no test harness in this repo. To validate a change:

1. Install the edited skill into a real .NET solution that still uses xUnit v2 (see INSTALL.md), or re-run it against a scratch clone of one.
2. Run the skill end-to-end and watch for steps where the agent has to improvise — improvisation means a gap in the skill; fix the skill, not just the run.
3. Fold lessons from the run back into SKILL.md or references (this is how the `parameters:`→`arguments:` rename and the stale-binary trap got in).

The `skill-creator` skill (if available in your session) can run benchmark evals against test prompts for larger revisions.

## History

Authored June 2026 from the official xUnit migration guides; validated on a real production solution (2 test projects, 2,864 tests, custom `ITestCaseOrderer`, AwesomeAssertions) with zero test loss. NUnit→xUnit v3 support added June 2026 from a verified migration of a 36-test NUnit 4 project (notes in `test/NUNit.Migration.md`), with assertion mappings targeting native xUnit `Assert` rather than the AwesomeAssertions path that run used.
