# INITIAL.md: Feature Request Template

The input to PRP generation. Four sections. The quality of what comes out is capped by the quality of what goes in.

## Template

```markdown
## FEATURE:

[Describe what you want to build. Be specific about functionality, inputs, outputs,
and requirements. Name the libraries and services involved.]

## EXAMPLES:

[List the files in the `examples/` folder that matter here and explain what each one
should be used for. State whether each is a pattern to copy or inspiration only.]

## DOCUMENTATION:

[Links to API docs, library guides, MCP server resources, database schemas. Include
the specific section when the page is long.]

## OTHER CONSIDERATIONS:

[Auth requirements, rate limits and quotas, environment setup, performance targets,
and the gotchas AI assistants repeatedly miss in this project.]
```

## Writing Each Section

### FEATURE

Weak: "Build a web scraper."

Strong: "Build an async web scraper using BeautifulSoup that extracts product data from e-commerce sites, handles rate limiting with exponential backoff, and stores results in PostgreSQL."

The difference is that the second one can be validated. Every requirement you leave out becomes a decision the assistant makes on your behalf.

### EXAMPLES

Reference specific files, not the folder. Say what aspect to mimic: the module layout, the error handling, the test structure. If the example comes from a different project, say so explicitly so it is treated as inspiration rather than a source to copy.

### DOCUMENTATION

Include the URL and the reason it is needed. An assistant with web search can fetch a page, but it cannot guess which page you meant.

### OTHER CONSIDERATIONS

This is where accumulated project knowledge goes. If the assistant has previously forgotten to load environment variables, missed a required `.env.example`, or used a sync call in an async path, write it down here. This section is the cheapest place in the workflow to prevent a repeat failure.

## Worked Example

```markdown
## FEATURE:

- Pydantic AI agent that has another Pydantic AI agent as a tool.
- Research Agent for the primary agent and then an email draft Agent for the subagent.
- CLI to interact with the agent.
- Gmail for the email draft agent, Brave API for the research agent.

## EXAMPLES:

In the `examples/` folder, there is a README explaining what the example covers and
how to structure your own README for this feature.

- `examples/cli.py` - use this as a template to create the CLI
- `examples/agent/` - read through all of these to understand best practices for
  creating Pydantic AI agents that support different providers and LLMs, handling
  agent dependencies, and adding tools to the agent.

Don't copy any of these directly, they are for a different project. Use them as
inspiration and for best practices.

## DOCUMENTATION:

Pydantic AI documentation: https://ai.pydantic.dev/

## OTHER CONSIDERATIONS:

- Include a .env.example and a README with setup instructions covering Gmail and
  Brave configuration.
- Include the project structure in the README.
- The virtual environment is already set up with the necessary dependencies.
- Use python_dotenv and load_env() for environment variables.
```

Note what this example does well: it names the exact files to read, it says plainly that the examples are not to be copied, it states an environment fact that would otherwise waste a step, and it asks for the supporting files (`.env.example`, README) that are easy to forget.
