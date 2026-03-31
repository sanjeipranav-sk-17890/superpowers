---
applyTo: "tests/**,**/*.test.*,**/*.spec.*"
---

# Instructions for Test Files

When creating or modifying test files, enforce strict Test-Driven Development practices.

## The Iron Law

**No production code without a failing test first.**

If you are editing a test file, the corresponding implementation must not yet exist (or the tested behavior must not yet pass). If you are looking at a test file alongside implementation code, the test must have been written first.

## RED-GREEN-REFACTOR

Every test must follow this cycle:

1. **RED** — Write the test. Run it. Confirm it fails for the right reason (feature missing, not a syntax error or typo). If it passes immediately, you are testing existing behavior — the test needs to be fixed or you are in the wrong phase.

2. **GREEN** — Write the minimal implementation to make the test pass. Run it. Confirm it passes. Confirm all other tests still pass.

3. **REFACTOR** — Clean up code without changing behavior. Keep all tests green throughout.

## Test Quality Requirements

### What makes a good test

- **One behavior per test** — if the test name contains "and", split it into two tests.
- **Descriptive name** — the test name should describe the behavior, not the implementation. `test('rejects empty email')` not `test('email validation test 1')`.
- **Tests real behavior** — test what the code should do, not how it does it internally. Avoid testing mock behavior.
- **Minimal setup** — if test setup is huge, the design is too coupled. Simplify the interface.
- **No test-only methods in production code** — do not add methods to production classes solely to make them testable.

### What makes a bad test

- Test written after implementation (not TDD)
- Test that passes immediately without ever failing
- Test that only verifies that mocks were called
- Test with vague name that does not describe behavior
- Test that tests multiple behaviors (should be split)
- Test setup that requires understanding the whole system

## Commit Discipline

After each test passes (green), commit immediately:

```bash
git add tests/path/test.py src/path/implementation.py
git commit -m "feat: add [behavior description]"
```

Do not batch multiple test-implementation cycles into one commit.

## Common Rationalizations to Reject

| Excuse | Reality |
|---|---|
| "Too simple to test" | Simple code breaks. The test takes 30 seconds. |
| "I'll write tests after" | Tests written after implementation pass immediately and prove nothing. |
| "Already manually tested" | Manual testing is not systematic. It cannot be re-run. |
| "Keep code as reference while writing tests" | You will adapt it. That is tests-after. Delete the code. |
| "TDD will slow me down" | TDD is faster than debugging production issues. |

## When Stuck on Testing

| Problem | Solution |
|---|---|
| Do not know how to test it | Write the wished-for API first. Write the assertion first. |
| Test is too complicated | The design is too complicated. Simplify the interface. |
| Must mock everything | The code is too coupled. Use dependency injection. |
| Test setup is huge | Extract helpers. Still complex? Simplify the design. |

## Verification Before Marking Complete

Before considering any implementation done, check:

- [ ] Every new function or method has a test
- [ ] Each test was watched failing before implementing
- [ ] Each test failed for the expected reason (not a typo or import error)
- [ ] Minimal code was written to pass each test
- [ ] All tests pass
- [ ] No errors or warnings in test output
- [ ] Edge cases and error conditions are covered
