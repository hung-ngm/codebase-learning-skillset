# Codebase Learning Skillset

A Claude Code skill that acts as a Distinguished Engineer mentor, guiding you through unfamiliar GitHub codebases with interactive exploration and concrete action plans.

## What It Does

Instead of dumping architecture docs at you, this skill mentors you through a codebase in 6 phases:

1. **Architecture Briefing** — System map, entry points, conventions (3 parallel agents)
2. **Guided Vertical Slice** — Interactive walkthrough of the golden path with mentor questions
3. **Risk Map** — Where the landmines are before you touch anything
4. **Action Plan** — Concrete first-win tasks, level-ups, and stretch goals
5. **Self-Assessment** — Checklist to gauge your understanding
6. **Study Guide Export** — Save everything as a personal reference doc

## Installation

Copy or clone the `.claude/skills/codebase-mentor/` directory into your project's `.claude/skills/` folder.

## Usage

In a Claude Code session:

```
/codebase-mentor vercel/ai
/codebase-mentor https://github.com/facebook/react
/codebase-mentor golang/go
```

It will ask your goal (study, contribute, integrate, or interview prep) and tailor the session accordingly.

## Requirements

- [Claude Code](https://claude.ai/claude-code)
- DeepWiki MCP server (for codebase intelligence)
- Exa MCP server (for community/contributor research)

## Project Structure

```
.claude/
└── skills/
    └── codebase-mentor/
        └── SKILL.md    # Skill definition and mentor logic
```

## Philosophy

Based on research from staff+ engineers on how the best developers approach new codebases:

- Architecture first, code last
- Trace one vertical slice, not the whole thing
- Learn by doing — ship a small win early
- Risk before features — know what breaks before you change it
- Consistency over cleverness
