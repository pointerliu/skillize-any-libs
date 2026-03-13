---
name: skillize-any-lib
description: Generate SKILL.md for any library by analyzing its source and real-world usage. Supports Rust, Python, JavaScript, Go, C/C++, and more.
metadata: {"opencode":{"emoji":"📦"}}
---

# Skillize Any Lib

Generate a comprehensive SKILL.md for any library to teach LLMs how to use it properly. The skill analyzes the library source and its dependents to extract real usage patterns.

## When to Use This Skill

Use this skill when:
- You need to use a library that LLMs may not know well
- You want to create a skill for a specific library
- You encounter a library with poor documentation
- User says: "skillize the lib X", "create a skill for crate X", etc.

## Workflow

Follow these steps to generate a SKILL.md for any library:

### Step 1: Parse Target Library

Extract library name from user prompt. Patterns:
- `skillize the lib {name}`
- `skillize {lang} lib {name}`
- `skillize crate {name}`
- `create skill for {name}`

Optional: User can specify language with `--lang rust|python|js|go|cpp`

### Step 2: Detect Language & Find Registry

Query package registries to detect language and get library info:

| Language | Registry API |
|----------|-------------|
| Rust | `https://crates.io/api/v1/crates/{lib}` |
| Python | `https://pypi.org/pypi/{lib}/json` |
| JavaScript | `https://registry.npmjs.org/{lib}` |
| Go | `https://pkg.go.dev/{lib}` |
| C/C++ | GitHub search only |

Fetch from registry:
- **Description**: One-line description
- **Version**: Latest version
- **Repository**: Source code URL

### Step 3: Find Reference Projects

Search GitHub for projects using this library:

| Language | Search Query |
|----------|-------------|
| Rust | `depends:{lib}` in Cargo.toml |
| Python | `import {lib}` or `from {lib}` |
| JavaScript | `require("{lib}")` or `from "{lib}"` |
| Go | `"{lib}"` in go.mod |
| C/C++ | `#include <{lib}>` |

Sort results by **stars** (descending), take top 5-10.

### Step 4: Ask User About Reference Projects

Present to user:

> "I found {N} popular projects on GitHub that use this library. Choose:
> 1. Use these GitHub projects
> 2. Add your own reference projects (provide URLs)
> 3. Use both
> 4. Skip - use only the library's examples/tests"

If user adds custom repos, collect the URLs.

### Step 5: Clone Repositories

Clone to temp directory (`/tmp/skillize-{lib}/`):
- The main library source
- Selected reference projects (shallow clone, depth=1)

### Step 6: Analyze

Extract and analyze:

| What | How |
|------|-----|
| README | Find use cases, features |
| Examples | `examples/` directory |
| Tests | `tests/` directory |
| API usage | Find `use`/`import`/`require` statements |
| Configuration | Cargo.toml, package.json, etc. |
| Feature flags | `[features]` in Cargo.toml |

For each reference project:
- How do they import the library?
- What are the main use cases?
- Any common patterns or gotchas?

### Step 7: Generate SKILL.md

Write to `~/.claude/skills/{lib}-usage/SKILL.md`

Use this structure (adapt as needed):

```markdown
---
name: {lib}-usage
description: {description from registry}
---

# {lib}

{one-line description}

## Quick Reference

| Need | Solution |
|------|----------|
| {common_need_1} | `{solution_1}` |
| {common_need_2} | `{solution_2}` |

## Installation

```{lang}
{package_manager_install_command}
```

## Core Types

```{lang}
{important_types}
```

## Basic Usage

```{lang}
{minimal_example}
```

## Common Usage Patterns

### Pattern 1: {name}
```{lang}
{code}
```

### Pattern 2: {name}
```{lang}
{code}
```

## Advanced Examples

```{lang}
{complex_example}
```

## Gotchas / Anti-patterns

- **{gotcha}**: {explanation}

## Limitations

- {limitation}

## Further Reading

- Official docs: {repo_url}
- Package registry: {pkg_url}
```

### Step 8: Cleanup

Remove temporary cloned repositories from `/tmp/skillize-{lib}/`

## Tips

- If no GitHub dependents found, use the library's own examples/tests
- Extract actual code snippets from real projects, not generated examples
- Include version-specific notes if relevant
- Add "When NOT to use" section if applicable

## Output Location

Generated SKILL.md: `~/.claude/skills/{lib_name}-usage/SKILL.md`
