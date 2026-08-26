# coderabbit-standards

Shared engineering standards for the org, in one place. These Markdown documents are the
**single source of truth** for coding guidelines. Other repositories reference them from
their own `.coderabbit.yaml` instead of copying the rules, so a change here updates what
every linked repository enforces on its next review.

## How other repos consume these

In a consuming repo's `.coderabbit.yaml`:

```yaml
knowledge_base:
  code_guidelines:
    enabled: true
    filePatterns:
      - "coderabbit-demo/coderabbit-standards:guidelines/**/*.md"
```

CodeRabbit reads these files at this repo's **default branch**. When a PR in the consuming
repo violates one of these rules, the review cites the guideline under the
**`Coding guidelines`** source.

## Contents

- [`guidelines/money.md`](guidelines/money.md) — money & currency handling
- [`guidelines/typescript.md`](guidelines/typescript.md) — TypeScript service conventions
