# Base PRP Template

A PRP (Product Requirements Prompt) is an implementation blueprint written for an AI implementer. Copy this structure into `PRPs/{feature-name}.md` and fill every section with real content from your research. Empty sections are the failure mode.

The code blocks below use Python for illustration. Substitute the target project's language and tooling.

---

## Purpose

Give the implementing agent enough context and enough self-validation to reach working code through iterative refinement rather than a lucky first draft.

## Structure

```markdown
name: "[Feature name]"

## Goal
[What needs to be built. Be specific about the end state.]

## Why
- [Business value and user impact]
- [How it fits with existing features]
- [What problem this solves, and for whom]

## What
[User-visible behavior and technical requirements]

### Success Criteria
- [ ] [Specific, measurable outcomes]

## All Needed Context

### Documentation & References
```yaml
# MUST READ - include these in the context window
- url: [Official API docs URL]
  why: [The specific sections or methods needed]

- file: [path/to/example.py]
  why: [Pattern to follow, gotchas to avoid]

- doc: [Library documentation URL]
  section: [Section covering common pitfalls]
  critical: [The insight that prevents a common error]

- docfile: [PRPs/ai_docs/file.md]
  why: [Docs pasted into the project by the user]
```

### Current Codebase Tree
[Output of `tree` at the repo root, trimmed to what matters]

### Desired Codebase Tree
[Files to be added, each with its responsibility]

### Known Gotchas & Library Quirks
```python
# CRITICAL: [Library] requires [specific setup]
# Example: FastAPI requires async functions for endpoints
# Example: This ORM does not support batch inserts over 1000 records
```

## Implementation Blueprint

### Data Models and Structure
[Core models first, so types stay consistent: ORM models, schemas, validators]

### Task List (in execution order)
```yaml
Task 1:
MODIFY src/existing_module.py:
  - FIND pattern: "class OldImplementation"
  - INJECT after the line containing "def __init__"
  - PRESERVE existing method signatures

CREATE src/new_feature.py:
  - MIRROR pattern from: src/similar_feature.py
  - MODIFY class name and core logic
  - KEEP the error handling pattern identical

Task N:
  ...
```

### Per-Task Pseudocode
```python
# Task 1 - pseudocode with the critical details only, not the whole implementation
async def new_feature(param: str) -> Result:
    # PATTERN: validate input first (see src/validators.py)
    validated = validate_input(param)  # raises ValidationError

    # GOTCHA: this library requires connection pooling
    async with get_connection() as conn:  # see src/db/pool.py
        # PATTERN: use the existing retry decorator
        @retry(attempts=3, backoff=exponential)
        async def _inner():
            # CRITICAL: API returns 429 above 10 req/sec
            await rate_limiter.acquire()
            return await external_api.call(validated)

        result = await _inner()

    # PATTERN: standardized response format
    return format_response(result)  # see src/utils/responses.py
```

### Integration Points
```yaml
DATABASE:
  - migration: "Add column 'feature_enabled' to users table"
  - index: "CREATE INDEX idx_feature_lookup ON users(feature_id)"

CONFIG:
  - add to: config/settings.py
  - pattern: "FEATURE_TIMEOUT = int(os.getenv('FEATURE_TIMEOUT', '30'))"

ROUTES:
  - add to: src/api/routes.py
  - pattern: "router.include_router(feature_router, prefix='/feature')"
```

## Validation Loop

### Level 1: Syntax & Style
```bash
ruff check src/new_feature.py --fix
mypy src/new_feature.py
# Expected: no errors. If there are errors, read them and fix the cause.
```

### Level 2: Unit Tests
```python
def test_happy_path():
    """Basic functionality works"""
    result = new_feature("valid_input")
    assert result.status == "success"

def test_validation_error():
    """Invalid input raises ValidationError"""
    with pytest.raises(ValidationError):
        new_feature("")

def test_external_api_timeout():
    """Handles timeouts gracefully"""
    with mock.patch('external_api.call', side_effect=TimeoutError):
        result = new_feature("valid")
        assert result.status == "error"
```

```bash
uv run pytest test_new_feature.py -v
# If failing: read the error, find the root cause, fix the code, re-run.
# Never mock something out just to make a test pass.
```

### Level 3: Integration Test
```bash
uv run python -m src.main --dev

curl -X POST http://localhost:8000/feature \
  -H "Content-Type: application/json" \
  -d '{"param": "test_value"}'

# Expected: {"status": "success", "data": {...}}
# On error: check logs/app.log for the stack trace
```

## Final Validation Checklist
- [ ] All tests pass
- [ ] No linting errors
- [ ] No type errors
- [ ] Manual test successful: [specific command]
- [ ] Error cases handled gracefully
- [ ] Logs are informative but not noisy
- [ ] Documentation updated if needed
```

---

## Anti-Patterns to Avoid

- Do not create new patterns when existing ones work.
- Do not skip validation because the code should work.
- Do not ignore a failing test. Fix it.
- Do not use sync functions in an async context.
- Do not hardcode values that belong in config.
- Do not catch every exception. Be specific.
