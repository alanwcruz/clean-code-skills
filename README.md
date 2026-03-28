# Clean Code Skills for AI Agents

[![Agent Skills](https://img.shields.io/badge/Agent%20Skills-Compatible-blue)](https://agentskills.io)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

**Teach your AI to write code that doesn't suck.**

This repository contains [Agent Skills](https://agentskills.io) that enforce Robert C. Martin's *Clean Code* principles for **TypeScript**. They work with **Cursor**, Google Antigravity, Anthropic's Claude Code, and any agent that supports the Agent Skills standard.

This copy is maintained on GitHub as **[alanwcruz/clean-code-skills](https://github.com/alanwcruz/clean-code-skills)**, a fork of **[ertugrul-dmr/clean-code-skills](https://github.com/ertugrul-dmr/clean-code-skills)**. For local `git` remotes and syncing from the original repo, see [Fork and remotes](#fork-and-remotes).

## Why?

AI generates code fast, but research shows it also generates technical debt fast:

- **GitClear**: 4x increase in code duplication with AI adoption
- **Carnegie Mellon**: +30% static analysis warnings, +41% code complexity after Cursor adoption
- **Google DORA**: Negative relationship between AI adoption and software delivery stability

These skills encode battle-tested solutions to exactly these problems—directly into your AI workflow.

## What's Included

| Skill | Description | Rules |
|-------|-------------|-------|
| `boy-scout` | **Orchestrator**—always leave code cleaner than you found it | Coordinates all skills |
| `typescript-clean-code` | **Master skill** with all 66 rules | C1-C5, E1-E2, F1-F4, G1-G36, N1-N7, P1-P3, T1-T9 |
| `clean-comments` | Minimal, accurate commenting | C1-C5 |
| `clean-functions` | Small, focused, obvious functions | F1-F4 |
| `clean-general` | Core principles (DRY, single responsibility) | G5, G16, G23, G25, G30, G36 |
| `clean-names` | Descriptive, unambiguous naming | N1-N7 |
| `clean-tests` | Fast, thorough, boundary-aware tests | T1-T9 |

Use the master skill for comprehensive coverage, or individual skills for targeted enforcement.

### The Boy Scout Rule

The `boy-scout` skill embodies Clean Code's core philosophy:

> "Always check a module in cleaner than when you checked it out."

You don't have to make code perfect—just **a little bit better** with every touch. The `boy-scout` skill orchestrates the others, ensuring every code interaction leaves a trail of small improvements.

---

## Installation

### Google Antigravity

**Project-specific** (applies to one project):

```bash
# From your project root
mkdir -p .agent/skills
cp -r skills/* .agent/skills/
```

**Global** (applies to all projects):

```bash
mkdir -p ~/.gemini/antigravity/skills
cp -r skills/* ~/.gemini/antigravity/skills/
```

**Quick install** (global, one command):

```bash
git clone https://github.com/ertugrul-dmr/clean-code-skills.git /tmp/clean-code-skills && \
mkdir -p ~/.gemini/antigravity/skills && \
cp -r /tmp/clean-code-skills/skills/* ~/.gemini/antigravity/skills/ && \
rm -rf /tmp/clean-code-skills
```

### Anthropic Claude Code

**Project-specific**:

```bash
# From your project root
mkdir -p .claude/skills
cp -r skills/* .claude/skills/
```

**Global**:

```bash
mkdir -p ~/.claude/skills
cp -r skills/* ~/.claude/skills/
```

**Quick install** (global, one command):

```bash
git clone https://github.com/ertugrul-dmr/clean-code-skills.git /tmp/clean-code-skills && \
mkdir -p ~/.claude/skills && \
cp -r /tmp/clean-code-skills/skills/* ~/.claude/skills/ && \
rm -rf /tmp/clean-code-skills
```

### Cursor

Cursor discovers skills automatically from [skill directories](https://cursor.com/docs/context/skills). Each skill is a folder (name must match the `name` field in `SKILL.md`) containing `SKILL.md`.

**Project-specific** (applies to one workspace):

```bash
# From this repository's root (copies into .cursor/skills here or adjust paths for your workspace)
mkdir -p .cursor/skills
cp -r skills/* .cursor/skills/
```

**Global** (applies to all projects):

```bash
mkdir -p ~/.cursor/skills
cp -r skills/* ~/.cursor/skills/
```

**Quick install** (global, one command):

```bash
git clone https://github.com/ertugrul-dmr/clean-code-skills.git /tmp/clean-code-skills && \
mkdir -p ~/.cursor/skills && \
cp -r /tmp/clean-code-skills/skills/* ~/.cursor/skills/ && \
rm -rf /tmp/clean-code-skills
```

Cursor also loads project skills from `.agents/skills/` and, for compatibility, from `.claude/skills/` and `.codex/skills/` (and the matching directories under `~`).

**Using skills in Cursor**: Open **Settings → Rules** to see discovered skills under the Agent section. In Agent chat, type **`/`** and search for a skill name (e.g. `typescript-clean-code`) to attach it explicitly. By default, Cursor includes skills when their `description` matches your task.

**Skills vs project rules**: These packages live under `.cursor/skills/`. [Cursor Rules](https://cursor.com/docs/context/rules) (`.cursor/rules/*.mdc`) are separate—use rules for always-on or glob-scoped policies; use skills for progressive, task-matched guidance like this set.

### Other Agent Skills-Compatible Tools

The skills follow the [Agent Skills](https://agentskills.io) open standard. Check your tool's documentation for the skills directory location, then copy the `skills/` folder contents there.

---

## Usage

Once installed, skills activate automatically based on context. Ask your agent to:

- **Write code**: "Create a user authentication module" → Skills enforce clean patterns
- **Review code**: "Review this function for issues" → Agent identifies violations by rule number
- **Refactor**: "Refactor this to be cleaner" → Agent applies all relevant rules

### Example

**Before** (10 violations):

```typescript
import * as utils from './utils'; // P1

// Author: John, Modified: 2024-01-15  // C1
export function proc(
  d: { type: string; val: number }[],
  t: number[],
  flag = false // N1, F1, F3
): number[] {
  // Process the data  // C3
  const x: number[] = []; // N1
  for (const i of d) {
    if (flag) {
      // F3
      if (i.type === 'A') {
        // G23
        x.push(i.val * 1.0825); // G25
      } else if (i.type === 'B') {
        x.push(i.val * 1.05); // G25
      }
    } else {
      x.push(i.val);
    }
  }
  return x;
}
```

**After** (with skills active):

```typescript
const TAX_RATE_CA = 0.0825;
const TAX_RATE_NY = 0.05;

type TransactionType = 'CA' | 'NY';

interface Transaction {
  value: number;
  type: TransactionType;
}

/** Apply state-specific tax to transaction value. */
export function applyTax(transaction: Transaction): number {
  const taxRates: Record<TransactionType, number> = {
    CA: TAX_RATE_CA,
    NY: TAX_RATE_NY,
  };
  return transaction.value * (1 + taxRates[transaction.type]);
}

export function processTransactionsWithTax(
  transactions: Transaction[]
): number[] {
  return transactions.map((t) => applyTax(t));
}

export function processTransactionsWithoutTax(
  transactions: Transaction[]
): number[] {
  return transactions.map((t) => t.value);
}
```

---

## Rule Reference

### Comments (C1-C5)
| Rule | Principle |
|------|-----------|
| C1 | No metadata in comments (use Git) |
| C2 | Delete obsolete comments immediately |
| C3 | No redundant comments |
| C4 | Write comments well if you must |
| C5 | Never commit commented-out code |

### Functions (F1-F4)
| Rule | Principle |
|------|-----------|
| F1 | Maximum 3 arguments |
| F2 | No output arguments |
| F3 | No flag arguments |
| F4 | Delete dead functions |

### General (G1-G36)
| Rule | Principle |
|------|-----------|
| G1 | One language per file |
| G2 | Implement expected behavior |
| G3 | Handle boundary conditions |
| G4 | Don't override safeties |
| G5 | DRY—no duplication |
| G6 | Consistent abstraction levels |
| G7 | Base classes don't know children |
| G8 | Minimize public interface |
| G9 | Delete dead code |
| G10 | Variables near usage |
| G11 | Be consistent |
| G12 | Remove clutter |
| G13 | No artificial coupling |
| G14 | No feature envy |
| G15 | No selector arguments |
| G16 | No obscured intent |
| G17 | Code where expected |
| G18 | Prefer instance methods |
| G19 | Use explanatory variables |
| G20 | Function names say what they do |
| G21 | Understand the algorithm |
| G22 | Make dependencies physical |
| G23 | Polymorphism over if/else |
| G24 | Follow conventions (ESLint, Prettier, strict `tsconfig`) |
| G25 | Named constants, not magic numbers |
| G26 | Be precise |
| G27 | Structure over convention |
| G28 | Encapsulate conditionals |
| G29 | Avoid negative conditionals |
| G30 | Functions do one thing |
| G31 | Make temporal coupling explicit |
| G32 | Don't be arbitrary |
| G33 | Encapsulate boundary conditions |
| G34 | One abstraction level per function |
| G35 | Config at high levels |
| G36 | Law of Demeter (one dot) |

### Names (N1-N7)
| Rule | Principle |
|------|-----------|
| N1 | Choose descriptive names |
| N2 | Names at appropriate abstraction level |
| N3 | Use standard nomenclature |
| N4 | Unambiguous names |
| N5 | Name length matches scope |
| N6 | No encodings (no Hungarian notation) |
| N7 | Names describe side effects |

### TypeScript-Specific (P1-P3)
| Rule | Principle |
|------|-----------|
| P1 | Explicit module boundaries (named imports; avoid careless `import *` / `export *`) |
| P2 | No magic domain constants (`as const`, unions, or enums per team style) |
| P3 | Typed public surface (no implicit `any` on exports; `unknown` at boundaries) |

### Tests (T1-T9)
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

---

## Customization

### Using Individual Skills

Don't need all 66 rules? Copy only the skills you want:

```bash
# Just function rules
cp -r skills/clean-functions ~/.gemini/antigravity/skills/

# Just comment rules  
cp -r skills/clean-comments ~/.claude/skills/
```

### Extending Skills

Add your own rules by editing the `SKILL.md` files or creating new skill folders:

```
skills/
├── typescript-clean-code/
│   └── SKILL.md
├── clean-comments/
│   └── SKILL.md
└── my-team-standards/      # Your custom skill
    └── SKILL.md
```

### Adding Enforcement Scripts

For stricter enforcement, add a `scripts/` folder with linters the agent can run:

```
skills/typescript-clean-code/
├── SKILL.md
└── scripts/
    └── check.sh            # e.g. npx eslint . && npx tsc --noEmit
```

---

## How Skills Work

Skills use **Progressive Disclosure**:

1. **Discovery**: Agent sees only skill names and descriptions
2. **Activation**: When your request matches a description, full instructions load
3. **Execution**: Scripts and templates load only when needed

This keeps the agent fast—it's not thinking about database migrations when you're writing a React component.

---

## Contributing

### Fork and remotes

Use two remotes so you can pull from the original project and push to this fork:

| Remote | Repository |
|--------|------------|
| `origin` | [github.com/alanwcruz/clean-code-skills](https://github.com/alanwcruz/clean-code-skills) (`https://github.com/alanwcruz/clean-code-skills.git`) |
| `upstream` | [github.com/ertugrul-dmr/clean-code-skills](https://github.com/ertugrul-dmr/clean-code-skills) (`https://github.com/ertugrul-dmr/clean-code-skills.git`) |

If you cloned this fork and only have `origin`, add the original:

```bash
git remote add upstream https://github.com/ertugrul-dmr/clean-code-skills.git
```

If you cloned the original first and want `origin` to point at this fork instead:

```bash
git remote rename origin upstream
git remote add origin https://github.com/alanwcruz/clean-code-skills.git
git branch --set-upstream-to=origin/main main
```

Sync changes from the original, then update the fork:

```bash
git fetch upstream
git merge upstream/main   # or: git rebase upstream/main
git push origin main
```

PRs welcome! Some ideas:

- [ ] Additional language support (Python, Go, Rust)
- [ ] Integration tests
- [ ] Pre-commit hooks
- [ ] IDE extensions

---

## Resources

- [*Clean Code*](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882) by Robert C. Martin
- [Agent Skills Standard](https://agentskills.io)
- [Cursor Agent Skills](https://cursor.com/docs/context/skills)
- [Antigravity Documentation](https://developers.google.com/antigravity)
- [Claude Code Documentation](https://docs.anthropic.com/claude-code)

---

## License

MIT License. See [LICENSE](LICENSE) for details.

---

*The future of programming is human intent translated by AI. Make sure the translation preserves quality, not just speed.*
