# Test Strategy — [Project Name]

> Quality baseline for this project. The `speckit.acps.test` command reads this to understand what "quality" means here before running any suite.

## Test Framework

| Layer | Framework | Runner | Config File |
|-------|-----------|--------|-------------|
| Unit | | | |
| Integration | | | |
| E2E | | | |

## Coverage Expectations

| Layer | Minimum | Threshold Type | Notes |
|-------|---------|---------------|-------|
| Unit | | line / branch / function | |
| Integration | | | |
| E2E | | | |

## Quality Gates

What must pass before any ACPS gateway (`GW_TestsOk`, `GW_UATOk`) can succeed:

- [ ] All unit tests pass
- [ ] Coverage at or above minimums above
- [ ] Build command exits 0
- [ ] Linter exits 0 on changed files
- [ ] [Add project-specific gates here]

## Forbidden Patterns

[Patterns that always constitute a test failure in this project — e.g. skipped tests in main, `.only` left in suite, `console.error` in production paths]

## Test Data & Environment

[How test data is seeded, required environment variables for test runs, mocking conventions]

## Change History

| Version | Date | Summary |
|---------|------|---------|
| Initial | [date] | Created by speckit.acps.setup |
