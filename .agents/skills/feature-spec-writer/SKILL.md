---
name: feature-spec-writer
description: Create or update Motimate feature requirement and policy documents from a product draft, following the repository's Goal document conventions and keeping the feature index in sync. Use when a user asks to add, organize, or revise feature requirements or product policies; do not use for an API specification alone.
---

# Feature Spec Writer

Turn a user's rough feature draft into the repository's shared product specification. Support both new features and careful updates to existing feature documents.

## Establish the Current Contract

Resolve all paths below from the repository root, even when Codex was launched from a nested directory. Before drafting or editing:

1. Read `features/README.md` and the complete contents of:
   - `features/goal/goal_requirement.md`
   - `features/goal/goal_policy.md`
2. If the target feature already exists, read both of its documents completely.
3. Inspect directly referenced repository documents when they affect terminology or an established decision.

Treat the current Goal documents as the formatting baseline and `features/README.md` as the source of truth for document states and index structure. Do not copy Goal-specific product decisions into another feature.

## Resolve Material Ambiguity

Derive the feature display name, lowercase kebab-case slug, and concrete rules from the draft and repository when possible. Ask the user before editing only when a missing choice would materially change the specification, such as:

- conflicting new and existing decisions;
- a required limit, default, permission, lifecycle transition, or destructive behavior with no stated value;
- multiple plausible feature identities or slugs;
- unclear inclusion or exclusion of a substantial behavior.

Group related questions and explain the consequence of each choice. Do not ask about wording, section ordering, or other decisions that can safely follow repository conventions. Do not invent a policy value merely to complete the document, and do not leave `TBD` placeholders unless the user explicitly requests them.

## Write the Feature Documents

Create or update these files:

- `features/<slug>/<slug>_requirement.md`
- `features/<slug>/<slug>_policy.md`

Write in Korean unless the user explicitly requests another language. Use the feature display name in titles and keep domain terminology consistent across both files.

### Requirement Document

Use this overall shape:

```markdown
# <Feature> 요구사항

상태: `Proposed`

## 사용자 요구사항

- ...

## 제외 범위

- ...
```

Requirements describe observable user or system behavior and acceptance criteria. Keep each bullet independently testable where practical. Include an exclusion only when it is stated by the user, already established in the repository, or necessary to prevent a clear scope misunderstanding. Add links to related documents only when those files exist or the user explicitly asks to create them.

### Policy Document

Use this overall shape:

```markdown
# <Feature> 정책

상태: `Proposed`

## <정책 주제>

- ...
```

Group rules by domain topic. Policies define allowed ranges, defaults, validation, permissions, exceptions, boundary conditions, deletion or retention behavior, and lifecycle rules. Use a table when it makes a mapping or state-dependent result clearer. Add `## 후속 검토` only for explicitly deferred decisions.

Avoid needless duplication: requirements state what behavior must be provided; policies state the rules governing that behavior. Repeat a rule only when the requirement would otherwise be ambiguous or untestable.

## Preserve Existing Decisions

For a new feature, set both documents to `Proposed`. For an existing feature:

- preserve its current status unless the user explicitly changes it;
- retain unrelated requirements, policies, links, and deliberate wording;
- merge additions into the relevant sections instead of replacing whole documents;
- remove or reverse an existing decision only when the user's intent is explicit;
- stop and resolve contradictions rather than silently choosing one version.

Do not define API payloads, endpoints, database schemas, implementation architecture, or UI components unless the user explicitly includes them in scope. A reference to a future technical specification is not authorization to create it.

## Synchronize the Feature Index

Add or update exactly one row in the table in `features/README.md`:

```markdown
| <Feature> | [요구사항](<slug>/<slug>_requirement.md) | [정책](<slug>/<slug>_policy.md) | `<Status>` |
```

Preserve the table's existing ordering unless the repository establishes a different convention. Make the index status match the two feature documents. If existing document statuses disagree, ask the user before reconciling them.

## Verify the Result

Before finishing:

- check that the requirement and policy documents do not contradict each other;
- check that every relative link resolves and the index contains no duplicate feature row;
- confirm that no unstated API, schema, or implementation decision was introduced;
- review the diff to ensure an update preserved unrelated existing content;
- run any repository Markdown validation that already exists; otherwise perform the checks above directly.

Summarize the created or updated documents and call out any explicit assumptions or deferred decisions.
