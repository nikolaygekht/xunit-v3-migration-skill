# xunit-v3-migration

A [Claude Code skill](https://code.claude.com/docs/en/skills) that migrates .NET test projects to **xUnit v3** (`xunit.v3`) — from **xUnit v2** (`xunit` 2.x) or from **NUnit**.

## What it does

When invoked (explicitly via `/xunit-v3-migration`, or automatically when you ask Claude Code to "migrate to xUnit v3" / "upgrade xunit" / "move from NUnit to xUnit"), the skill walks the agent through a safe, verifiable migration:

1. **Inventory** — finds every test project and scans for the code patterns that v3 breaks (`Xunit.Abstractions`, `IAsyncLifetime`, `async void` tests, custom orderers/data attributes, string-based attribute type references, `xunit.runner.json`).
2. **Baseline** — records the pre-migration test count so nothing can be silently lost.
3. **Packages & csproj** — applies the full v2→v3 package mapping (`xunit` → `xunit.v3`, removal of `xunit.abstractions`, merged extensibility/runner packages) and sets `<OutputType>Exe</OutputType>` (v3 test projects are stand-alone executables). Includes local, offline-friendly procedures for checking third-party package compatibility (nuspec inspection, DLL grep) — no web searching required.
4. **Code fixes** — ordered, mechanical edits: namespace moves, `IAsyncLifetime` → `ValueTask`/`IAsyncDisposable`, `typeof()` in attributes, `[MemberData(..., parameters:)]` → `arguments:`, custom extensibility porting.
5. **Verify** — build, run, compare test count against the baseline, deliberately fail one assertion to prove the assertion library detects xUnit v3 at runtime, and classify any failures as environmental vs. migration-caused before blaming the migration.

For **NUnit** projects the same workflow applies with a dedicated mapping reference: attribute conversion (`[Test]`→`[Fact]`, `[TestCase]`→`[Theory]`+`[InlineData]`, `[SetUp]`→constructor, `[OneTimeSetUp]`→`IClassFixture<T>`), assertion rewrites from NUnit's classic and constraint (`Assert.That`) models to xUnit's native `Assert`, `TestContext` mapping, and the two semantic traps (per-test class instances, parallel-by-default execution).

## What's in the box

```
skills/
├── SKILL.md                    # the workflow (entry point Claude loads)
└── references/
    ├── api-changes.md          # complete v2→v3 namespace/type relocation tables and signatures
    ├── extensibility.md        # porting custom orderers, data attributes, test frameworks
    └── nunit-migration.md      # NUnit→xUnit v3 attribute, lifecycle, and assertion mapping
```

## Provenance

Built from the official migration guides ([migration](https://xunit.net/docs/getting-started/v3/migration), [extensibility](https://xunit.net/docs/getting-started/v3/migration-extensibility)) and battle-tested on a real production solution: two test projects, 2,864 tests, a custom `ITestCaseOrderer`, AwesomeAssertions — migrated with zero test loss and a fully green run. Lessons from that run (the `parameters:`→`arguments:` rename, stale-binary traps, environmental-failure triage) are folded back into the skill. The NUnit path is built from a verified NUnit 4 → xunit.v3 migration of a 36-test project and the official [framework comparison](https://xunit.net/docs/comparisons).

## Installing

See [INSTALL.md](INSTALL.md).

## License

See [LICENSE](LICENSE).
