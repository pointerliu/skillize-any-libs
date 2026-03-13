# skillize-any-libs

A meta-skill that generates SKILL.md for any library by analyzing its source code and real-world usage in popular dependent projects.

## Problem

LLMs may not be trained on all libraries (Python packages, Rust crates, npm packages, etc.), causing them to not know how to use them properly. This meta-skill helps generate comprehensive SKILL.md files that teach LLMs how to use any library.

## Solution

The skill guides you through 8 steps:

1. **Parse** - Extract library name from user prompt
2. **Detect** - Find the package registry and detect language
3. **Discover** - Search GitHub for dependent projects
4. **Ask** - Let user choose reference projects
5. **Clone** - Clone library and selected repos
6. **Analyze** - Extract usage patterns, examples, APIs
7. **Generate** - Write SKILL.md
8. **Cleanup** - Remove temp files

## Supported Languages

| Language | Registry | Search Pattern | Tested |
|----------|----------|----------------|--------|
| Rust | crates.io | `depends:{lib}` | ✅ Yes |
| Python | PyPI | `import {lib}` | ❌ Not yet |
| JavaScript | npm | `require("{lib}")` | ❌ Not yet |
| Go | pkg.go.dev | `"{lib}"` in go.mod | ❌ Not yet |
| C/C++ | GitHub only | `#include` | ❌ Not yet |

## Usage

### Install the Skill

Copy this skill to your AI editor's skills directory:

**Claude Code / OpenCode:**
```bash
cp -r . ~/.claude/skills/skillize-any-libs
# or
cp -r . ~/.config/opencode/skills/skillize-any-libs
```

### Use the Skill

When user says "skillize the lib X", load this skill and follow the workflow in SKILL.md.

Example:
- User: "skillize the lib tokio"
- You: Follow the 8-step workflow, generate SKILL.md

## Output

Generated SKILL.md is saved to:
```
~/.claude/skills/<lib_name>-usage/SKILL.md
```

## Requirements

- `git` - cloning repositories
- `gh` CLI (optional) - GitHub search
- `curl` / `jq` (optional) - API calls
- Network access to package registries

## Philosophy

No hardcoded scripts. The meta-skill guides the LLM through the process dynamically, making it adaptable to any language or package manager.
