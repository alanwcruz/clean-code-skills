---
name: clean-tests
description: Use when writing, fixing, editing, or refactoring TypeScript tests. Enforces Clean Code principles—fast tests, boundary coverage, one assert per test.
---

# Clean Tests

## T1: Insufficient Tests

Test everything that could possibly break. Use coverage tools as a guide, not a goal.

```typescript
import { describe, it, expect } from 'vitest';
import { divide } from './math';

// Bad - only tests happy path
describe('divide', () => {
  it('works', () => {
    expect(divide(10, 2)).toBe(5);
  });
});

// Good - tests edge cases too
describe('divide', () => {
  it('divides normally', () => {
    expect(divide(10, 2)).toBe(5);
  });

  it('throws on divide by zero', () => {
    expect(() => divide(10, 0)).toThrow(/zero/i);
  });

  it('handles negative dividend', () => {
    expect(divide(-10, 2)).toBe(-5);
  });
});
```

## T2: Use a Coverage Tool

Coverage tools report gaps in your testing strategy. Don't ignore them.

```bash
# Examples — use whatever your project configures
vitest run --coverage
# or
npm test -- --coverage
```

## T3: Don't Skip Trivial Tests

Trivial tests document behavior and catch regressions. They're worth more than their cost.

```typescript
// Worth having - documents expected behavior
it('defaults new user role to member', () => {
  const user = new User({ name: 'Alice' });
  expect(user.role).toBe('member');
});
```

## T4: An Ignored Test Is a Question About an Ambiguity

Don't use `it.skip` / `test.skip` to hide problems. Either fix the test or delete it.

```typescript
// Bad - hiding a problem
it.skip('flaky async operation', async () => {
  // ...
});

// Good - either fix it or document why it's skipped
it.skip('requires Redis; see CONTRIBUTING.md for setup', async () => {
  // ...
});
```

## T5: Test Boundary Conditions

Bugs congregate at boundaries. Test them explicitly.

```typescript
describe('paginate', () => {
  const items = Array.from({ length: 100 }, (_, i) => i);

  it('returns first page', () => {
    expect(paginate(items, { page: 1, size: 10 })).toEqual(items.slice(0, 10));
  });

  it('returns last full page', () => {
    expect(paginate(items, { page: 10, size: 10 })).toEqual(items.slice(90, 100));
  });

  it('returns empty beyond last page', () => {
    expect(paginate(items, { page: 11, size: 10 })).toEqual([]);
  });

  it('rejects page zero', () => {
    expect(() => paginate(items, { page: 0, size: 10 })).toThrow(RangeError);
  });

  it('handles empty list', () => {
    expect(paginate([], { page: 1, size: 10 })).toEqual([]);
  });
});
```

## T6: Exhaustively Test Near Bugs

When you find a bug, write tests for all similar cases. Bugs cluster.

```typescript
// Found bug: off-by-one in date calculation
// Now test ALL date boundaries
describe('lastDayOfMonth', () => {
  it('January has 31 days', () => {
    expect(lastDayOfMonth(2024, 1)).toBe(31);
  });
  it('February leap year', () => {
    expect(lastDayOfMonth(2024, 2)).toBe(29);
  });
  it('February non-leap year', () => {
    expect(lastDayOfMonth(2023, 2)).toBe(28);
  });
  it('April has 30 days', () => {
    expect(lastDayOfMonth(2024, 4)).toBe(30);
  });
  it('December has 31 days', () => {
    expect(lastDayOfMonth(2024, 12)).toBe(31);
  });
});
```

## T7: Patterns of Failure Are Revealing

When tests fail, look for patterns. They often point to deeper issues.

```typescript
// If all async tests fail intermittently,
// the problem isn't the tests—it's the async handling
```

## T8: Test Coverage Patterns Can Be Revealing

Look at which code paths are untested. Often they reveal design problems.

```typescript
// If you can't easily test a function, it probably does too much
// Refactor for testability
```

## T9: Tests Should Be Fast

Slow tests don't get run. Keep unit tests under 100ms each.

```typescript
// Bad - hits real database
it('creates user', async () => {
  const db = await connectToDatabase(); // Slow!
  const user = await db.createUser('Alice');
  expect(user.name).toBe('Alice');
});

// Good - uses mock or in-memory
it('creates user', () => {
  const db = new InMemoryDatabase();
  const user = db.createUser('Alice');
  expect(user.name).toBe('Alice');
});
```

## Test Organization

### F.I.R.S.T. Principles

- **Fast**: Tests should run quickly
- **Independent**: Tests shouldn't depend on each other
- **Repeatable**: Same result every time, any environment
- **Self-Validating**: Pass or fail, no manual inspection
- **Timely**: Written before or with the code, not after

### One Concept Per Test

```typescript
// Bad - testing multiple things
it('user', () => {
  const user = new User({ name: 'Alice', email: 'alice@example.com' });
  expect(user.name).toBe('Alice');
  expect(user.email).toBe('alice@example.com');
  expect(user.isValid()).toBe(true);
  user.activate();
  expect(user.isActive).toBe(true);
});

// Good - one concept each
it('stores name', () => {
  const user = new User({ name: 'Alice', email: 'alice@example.com' });
  expect(user.name).toBe('Alice');
});

it('stores email', () => {
  const user = new User({ name: 'Alice', email: 'alice@example.com' });
  expect(user.email).toBe('alice@example.com');
});

it('new user is valid', () => {
  const user = new User({ name: 'Alice', email: 'alice@example.com' });
  expect(user.isValid()).toBe(true);
});

it('can be activated', () => {
  const user = new User({ name: 'Alice', email: 'alice@example.com' });
  user.activate();
  expect(user.isActive).toBe(true);
});
```

## Quick Reference

| Rule | Principle |
|------|-----------|
| T1 | Test everything that could break |
| T2 | Use coverage tools |
| T3 | Don't skip trivial tests |
| T4 | Ignored test = ambiguity question |
| T5 | Test boundary conditions |
| T6 | Exhaustively test near bugs |
| T7 | Look for patterns in failures |
| T8 | Check coverage when debugging |
| T9 | Tests must be fast (<100ms) |
