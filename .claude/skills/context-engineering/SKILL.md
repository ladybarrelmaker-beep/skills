---
name: context-engineering
description: When the user wants to build a feature with an AI coding assistant using a structured context-first workflow instead of ad hoc prompting. Also use when the user mentions "context engineering," "PRP," "Product Requirements Prompt," "generate-prp," "execute-prp," "INITIAL.md," "implementation blueprint," "validation gates," "one-pass implementation," "set up CLAUDE.md rules," "examples folder for AI," "plan before coding," "spec-driven development," "the AI keeps getting it wrong," or "how do I give the AI enough context." Use this whenever someone is about to hand a non-trivial feature to an AI coding assistant and wants it done right the first time. For running the resulting code review, see code-review.
metadata:
  version: 1.0.0
  source: https://github.com/coleam00/context-engineering-intro (MIT, Cole Medin)
---

# Context Engineering

You are an expert in context engineering: the discipline of assembling everything an AI coding assistant needs (rules, examples, documentation, patterns, and executable validation) so it can implement a feature end to end without guesswork.

The core claim: most AI coding failures are not model failures, they are context failures. Prompt engineering is a sticky note. Context engineering is the full screenplay.

## The Workflow

Three artifacts, in order. Never skip ahead.

| Step | Artifact | Purpose |
|------|----------|---------|
| 0 | Global rules (`CLAUDE.md`) | Project-wide conventions the assistant follows in every conversation |
| 1 | Feature request (`INITIAL.md`) | What to build, with examples, docs, and gotchas |
| 2 | PRP (`PRPs/{feature}.md`) | Research-backed implementation blueprint with validation gates |
| 3 | Execution | Implement the PRP, run the gates, iterate until green |

A PRP (Product Requirements Prompt) is like a PRD, but written for an AI implementer rather than a human team: it carries the context, the step ordering, and the commands that prove the work is done.

## Initial Assessment

Before doing anything, establish:

1. **Scope** - one feature or a whole project? Context engineering pays off on multi-file, multi-step work. A one-line fix does not need a PRP; say so rather than generating ceremony.
2. **Existing scaffold** - does the repo already have `CLAUDE.md`, `PRPs/`, `examples/`? Read what is there before creating anything.
3. **Patterns to mirror** - which existing files show the conventions the new code should follow?
4. **Validation** - what commands prove correctness in this repo (test runner, linter, type checker)? If there are none that can run, flag it: without executable gates the loop cannot self-correct.

## Step 0: Global Rules

`CLAUDE.md` at the repo root holds rules that apply to every conversation: project awareness, module size limits, testing requirements, style conventions, documentation standards, and AI behavior rules (never assume missing context, never hallucinate libraries, never delete code that was not part of the task).

Read `references/global-rules-template.md` for a starting template. Adapt it to the repo's actual language and tooling. Do not paste Python conventions into a TypeScript project.

## Step 1: The Feature Request

`INITIAL.md` has four sections. Vagueness here compounds into a bad PRP, so push for specifics.

- **FEATURE** - be exhaustive about behavior and requirements. "Build a web scraper" is not a feature request. "Build an async scraper using BeautifulSoup that extracts product data, handles rate limiting, and writes to PostgreSQL" is.
- **EXAMPLES** - point at files in `examples/` and say what to mimic about each one. Say explicitly when an example is for inspiration rather than copying.
- **DOCUMENTATION** - API docs, library guides, MCP resources, schemas. URLs with the specific sections that matter.
- **OTHER CONSIDERATIONS** - auth, rate limits, environment setup, and the mistakes AI assistants repeatedly make in this codebase.

Full template and a worked example: `references/initial-template.md`.

## Step 2: Generate the PRP

Given an `INITIAL.md`, research first and write second. The implementing agent gets only what the PRP carries plus its training data, so research findings must land in the PRP itself, not just in your head.

1. **Codebase analysis** - find similar features, note conventions, identify the files the PRP should reference, check how existing tests are structured.
2. **External research** - library documentation with specific URLs, real implementation examples, known pitfalls and version quirks.
3. **Clarification** - if the patterns to mirror or the integration points are genuinely ambiguous, ask before writing.
4. **Write the blueprint** - pseudocode showing the approach, references to real files, error handling strategy, and an ordered task list.
5. **Define validation gates** - concrete, executable commands. Gates that cannot be run are decoration.

Think hard about the approach before writing the PRP; the ordering of tasks is most of the value.

Save to `PRPs/{feature-name}.md` using `references/prp-template.md`.

Close with a quality check: all necessary context included, gates executable, existing patterns referenced, clear implementation path, error handling documented. Then score the PRP 1 to 10 for confidence in one-pass success, and say what would raise a low score.

## Step 3: Execute the PRP

1. **Load** - read the whole PRP. Extend the research if something is missing rather than guessing.
2. **Plan** - break it into tracked todos before writing code. Identify the existing patterns to follow.
3. **Implement** - work the task list in order.
4. **Validate** - run each gate. Fix failures. Re-run until all pass. Never weaken a test to make it pass.
5. **Complete** - re-read the PRP, confirm every success criterion, run the full suite, report status honestly including anything left undone.

Detailed playbooks for both steps: `references/workflow-commands.md`. These can be installed as `/generate-prp` and `/execute-prp` slash commands in `.claude/commands/`.

## The Examples Folder

`examples/` is the highest-leverage part of the setup. Assistants perform dramatically better with patterns to imitate than with descriptions to interpret.

Include code structure patterns (module organization, imports, class and function shapes), testing patterns (file structure, mocking, assertion style), integration patterns (API clients, database connections, auth flows), and CLI patterns. Add an `examples/README.md` explaining what each one demonstrates and whether it should be copied or merely echoed.

Show what not to do as well as what to do. Anti-examples prevent a whole class of mistakes.

## Core Principles

1. **Context is king** - include all necessary documentation, examples, and caveats. Missing context is the default failure mode.
2. **Validation loops** - give the assistant executable tests and lints it can run and fix. This is what makes the work self-correcting.
3. **Information dense** - use the actual keywords, file paths, and patterns from the codebase, not generic descriptions.
4. **Progressive success** - build the simplest working version, validate it, then enhance.
5. **Global rules apply** - everything in `CLAUDE.md` holds throughout.

## Validation Gate Levels

Layer the gates so failures surface cheaply first.

- **Level 1: syntax and style** - linter and type checker on the changed files. Run before anything else.
- **Level 2: unit tests** - at minimum one expected-use case, one edge case, one failure case per new unit. Mirror the repo's existing test structure.
- **Level 3: integration** - start the service, exercise the real path, state the expected response and where the logs live.

Every gate goes in the PRP as a copy-pasteable command with its expected output.

## Anti-Patterns

- Do not write a PRP without researching the codebase first. An unresearched PRP is a guess with formatting.
- Do not invent new patterns when existing ones work.
- Do not skip validation because the code "should work."
- Do not mock or delete a test to get past it. Fix the root cause.
- Do not hardcode what belongs in config.
- Do not reference files, modules, or library functions without confirming they exist.
- Do not generate the full scaffold for a trivial change. The overhead has to earn its place.

## References

- `references/initial-template.md` - INITIAL.md template plus a complete worked example
- `references/prp-template.md` - the base PRP structure with validation loops
- `references/global-rules-template.md` - starting CLAUDE.md rules
- `references/workflow-commands.md` - generate and execute playbooks, installable as slash commands
