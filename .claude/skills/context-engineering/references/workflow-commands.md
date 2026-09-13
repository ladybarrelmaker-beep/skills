# Workflow Playbooks

The two halves of the PRP workflow. Each can be installed as a slash command by saving it to `.claude/commands/generate-prp.md` and `.claude/commands/execute-prp.md`, where `$ARGUMENTS` receives whatever follows the command name.

---

## Generate PRP

**Input:** a feature file, usually `INITIAL.md`.

Generate a complete PRP with thorough research. The goal is a document that lets the implementing agent self-validate and refine iteratively. Read the feature file first to understand what is being built, how the provided examples help, and what else was flagged.

The implementing agent gets only the context in the PRP plus its training data. Assume it has codebase access and the same knowledge cutoff as you, so research findings must be written into the PRP or referenced by URL. It can search the web, so pass links to documentation and examples.

### Research Process

1. **Codebase analysis**
   - Search for similar features and patterns already in the codebase.
   - Identify the files the PRP should reference.
   - Note the conventions to follow.
   - Check the existing test patterns to shape the validation approach.

2. **External research**
   - Similar implementations in the wild.
   - Library documentation, with specific URLs.
   - Examples from GitHub, Stack Overflow, and technical blogs.
   - Best practices and common pitfalls for the libraries involved.

3. **User clarification** (only when genuinely blocked)
   - Which patterns should be mirrored, and where do they live?
   - What are the integration requirements?

### PRP Generation

Use the base PRP template as the structure.

Critical context to carry into the PRP:
- **Documentation** - URLs with the specific sections that matter.
- **Code examples** - real snippets from this codebase, not invented ones.
- **Gotchas** - library quirks, version constraints, rate limits.
- **Patterns** - the existing approaches the new code should follow.

Implementation blueprint:
- Start with pseudocode showing the approach.
- Reference real files for each pattern.
- Include the error handling strategy.
- List the tasks in the order they should be completed.

Validation gates must be executable. For example:

```bash
# Syntax and style
ruff check --fix && mypy .

# Unit tests
uv run pytest tests/ -v
```

**Before writing: finish researching and exploring the codebase, then think hard about the approach and plan it out. Only then start writing the PRP.**

### Output

Save as `PRPs/{feature-name}.md`.

### Quality Checklist

- [ ] All necessary context included
- [ ] Validation gates are executable by the agent
- [ ] Existing patterns referenced
- [ ] Clear implementation path
- [ ] Error handling documented

Score the PRP from 1 to 10 for confidence in one-pass implementation success, and name what would raise the score if it is low.

The goal is one-pass implementation success through comprehensive context.

---

## Execute PRP

**Input:** a PRP file, for example `PRPs/my-feature.md`.

### Execution Process

1. **Load the PRP**
   - Read the specified file in full.
   - Understand all context and requirements.
   - Follow every instruction in it, and extend the research if something is missing.
   - Do additional web searches and codebase exploration as needed rather than guessing.

2. **Think hard, then plan**
   - Create a comprehensive plan addressing all requirements before touching code.
   - Break complex tasks into smaller steps using the todo tooling.
   - Identify the implementation patterns in existing code to follow.

3. **Execute**
   - Work the task list in order.
   - Implement all the code.

4. **Validate**
   - Run each validation command.
   - Fix failures at the root cause.
   - Re-run until everything passes.

5. **Complete**
   - Confirm every checklist item is done.
   - Run the final validation suite.
   - Re-read the PRP to verify nothing was missed.
   - Report status honestly, including anything left undone and why.

If validation fails, use the error patterns documented in the PRP to diagnose and retry. The PRP stays available as a reference throughout.
