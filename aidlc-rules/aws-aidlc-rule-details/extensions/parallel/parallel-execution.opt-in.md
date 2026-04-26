# Parallel Execution — Opt-In

**Extension**: Parallel Unit Development

## Opt-In Prompt

The following question is automatically included in the Requirements Analysis clarifying questions when this extension is loaded:

```markdown
## Question: Parallel Development
Will multiple developers or AI agents work on units simultaneously?

A) Yes — enable parallel execution with Interface Contracts, Mock servers, branch-based isolation, and PO orchestration (recommended for teams of 2+)
B) No — sequential unit execution, one unit at a time (choose this for single-developer projects OR when only 1 unit exists)
X) Other (please describe after [Answer]: tag below)

[Answer]:
```
