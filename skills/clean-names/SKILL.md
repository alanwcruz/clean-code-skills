---
name: clean-names
description: Use when naming, renaming, or fixing names of variables, functions, classes, or modules in TypeScript. Enforces Clean Code principles—descriptive names, appropriate length, no encodings.
---

# Clean Names

## N1: Choose Descriptive Names

Names should reveal intent. If a name requires a comment, it doesn't reveal its intent.

```typescript
// Bad - what is d?
const d = 86400;

// Good - obvious meaning
const SECONDS_PER_DAY = 86400;

// Bad - what does this function do?
function proc(lst: number[]): number[] {
  return lst.filter((x) => x > 0);
}

// Good - intent is clear
function filterPositiveNumbers(numbers: number[]): number[] {
  return numbers.filter((n) => n > 0);
}
```

## N2: Choose Names at the Appropriate Level of Abstraction

Don't pick names that communicate implementation; choose names that reflect the level of abstraction of the class or function.

```typescript
// Bad - too implementation-specific
function getDictOfUserIdsToNames(): Map<string, string> {
  // ...
}

// Good - abstracts the data structure
function getUserDirectory(): Map<string, string> {
  // ...
}
```

## N3: Use Standard Nomenclature Where Possible

Use terms from the domain, design patterns, or well-known conventions.

```typescript
// Good - uses pattern name
class UserFactory {
  create(data: UserInput): User {
    // ...
  }
}

// Good - uses domain term
function calculateAmortization(
  principal: number,
  rate: number,
  term: number
): number {
  // ...
}
```

## N4: Unambiguous Names

Choose names that make the workings of a function or variable unambiguous.

```typescript
// Bad - ambiguous
function rename(a: string, b: string): void {
  // ...
}

// Good - clear what's being renamed
function renameFile(oldPath: string, newPath: string): void {
  // ...
}
```

## N5: Use Longer Names for Longer Scopes

Short names are fine for tiny scopes. Longer scopes need longer, more descriptive names.

```typescript
// Good - short name for tiny scope
const total = numbers.reduce((x, n) => x + n, 0);

// Good - longer name for module-level constant
const MAX_RETRY_ATTEMPTS_BEFORE_FAILURE = 5;

// Bad - short name at module level
const MAX = 5;
```

## N6: Avoid Encodings

Don't encode type or scope information into names. Modern editors make this unnecessary.

```typescript
// Bad - Hungarian-style encodings
const strName = 'Alice';
const arrUsers: User[] = [];
let iCount = 0;

// Good - clean names
const name = 'Alice';
const users: User[] = [];
let count = 0;

// Bad - interface prefix (avoid in TypeScript style)
interface IUserRepository {
  // ...
}

// Good - just name it
interface UserRepository {
  // ...
}
```

## N7: Names Should Describe Side Effects

If a function does something beyond what its name suggests, the name is misleading.

```typescript
interface ConfigPathHandle {
  exists(): boolean;
  writeText(content: string): void;
  readText(): string;
}

// Bad - name doesn't mention file creation
function getConfig(configPath: ConfigPathHandle): Config {
  if (!configPath.exists()) {
    configPath.writeText('{}'); // Hidden side effect!
  }
  return JSON.parse(configPath.readText()) as Config;
}

// Good - name reveals behavior
function getOrCreateConfig(configPath: ConfigPathHandle): Config {
  if (!configPath.exists()) {
    configPath.writeText('{}');
  }
  return JSON.parse(configPath.readText()) as Config;
}
```

## Quick Reference

| Rule | Principle | Example |
|------|-----------|---------|
| N1 | Descriptive names | `SECONDS_PER_DAY` not `d` |
| N2 | Right abstraction level | `getUserDirectory()` not `getDictOf...` |
| N3 | Standard nomenclature | `UserFactory`, `calculateAmortization` |
| N4 | Unambiguous | `renameFile(oldPath, newPath)` |
| N5 | Length matches scope | Short for loops, long for globals |
| N6 | No encodings | `users` not `arrUsers` |
| N7 | Describe side effects | `getOrCreateConfig()` |
