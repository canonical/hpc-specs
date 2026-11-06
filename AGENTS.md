## Overview

This repository hosts the development specs for [Charmed HPC](https://github.com/canonical/charmed-hpc-docs). Each spec is a self-contained Markdown document under `specs/` that proposes, defines, or documents a design decision for the Charmed HPC ecosystem.

The source layout is:

```text
.
└── specs/  # Specs written in Markdown
```

Walk the contents of the `specs/` directory to see each spec defined in this repository.

## Discovering existing specs

Before making edits or analyzing the project, map the repository structure yourself:

1. Execute `git ls-files` to inspect the directory layout.
2. Search for relevant specs using `grep` / `ripgrep` or file-search tools.
3. Do **not** expect an explicit list of files in this documentation; walk the directory tree dynamically based on the task requirements.

## How to use this repo

- **Specs are authoritative.** When a spec and a downstream charm disagree, the spec wins unless the spec has been superseded by a later one.
- **Specs are not summaries.** They contain full rationale, evaluated alternatives, and code samples. Do not paraphrase them into a shorter document — read them directly.
- **Implementation status is not tracked here.** This repo records *what was decided*, not *what has been built*. To check whether a spec has been implemented, consult the relevant charm repository (e.g. `slurm-charms`, `charmed-hpc-libs`).
- **Index numbers are stable.** A spec's `UHPCNNN` index is permanent. Gaps in the sequence (e.g. UHPC007, UHPC014, UHPC015) are reserved or withdrawn numbers — do not reuse them.

## Spec structure

Every spec under `specs/` follows this structure. The headings and order make specs predictable to read and write:

1. **YAML front matter** — `index` (for example, `UHPC017`) and `title`.
2. **`## Abstract`** — a concise statement of the proposal or decision.
3. **`## Rationale`** — the problem, users, and constraints that motivate the change.
4. **`## Specification`** — the normative design. Describe the intended behavior, interfaces, responsibilities, and relevant migration or compatibility requirements. Include code samples when they clarify the design.
5. **`## Further information`** — references, related specs, and relevant external discussions. Some specs use a `## References` heading for this section instead; either is fine.

Add sections such as `## Scope`, `## Risks`, `## Future work`, or `### Considerations` when they make the design clearer. Keep the spec itself authoritative: distinguish requirements from examples, implementation notes, and open questions.

## Conventions for writing a new spec

When drafting a spec:

- **Check whether a new spec is needed.** Walk `specs/` and read related specs first. Amend an existing spec when the proposal changes or clarifies an existing decision; do not create a second spec for the same decision.
- **Start from the problem and desired outcome.** Define who needs the change, what problem it solves, the scope and non-goals, and the constraints that shape the design.
- **Use existing specs as design constraints.** Follow the cross-links between specs; they record which decisions build on which. A downstream repository may provide implementation context, but its current behavior does not override an existing spec or define a new design by itself.
- **Pick the next free index.** Claim the next `UHPCNNN` number not already used under `specs/` or claimed by an open pull request. Indices are never reused, even if a proposal is declined; do not fill gaps in the sequence.
- **Create one folder per spec.** Folder name format: `UHPC NNN - <Sentence-style title>`. Capitalize only the first word and any proper nouns or acronyms. Example: `UHPC 017 - My new thing`.
- **Use the current filename convention for new specs.** Name the file `uhpcNNN.md` with a three-digit index and no hyphen, as described in `CONTRIBUTING.md`. The earliest specs (UHPC001–UHPC006) use the legacy `uhpc-NNN.md` form; do not rename them just to make them consistent.
- **Include the required front matter.** Every spec starts with:
  ```yaml
  ---
  index: UHPC017
  title: My new thing
  ---
  ```
- **Put assets in a `static/` subdirectory.** Images and other files referenced by the spec belong alongside it, in `specs/UHPC NNN - <title>/static/`.
- **Wrap prose at around 100 characters** to keep diffs readable.
- **Document important alternatives.** Explain non-obvious alternatives that were considered and why they were rejected. Record risks, trade-offs, open questions, and future work when they affect how the decision should be understood or implemented.
- **Cross-link related specs.** Use relative Markdown links to other specs in `specs/`. URL-encode spaces and special characters in folder names (for example, `UHPC%20008`). Cross-links are the primary way specs find each other — this repo maintains no central index.
- **Follow Conventional Commits.** Scope commits that touch a single spec with its index, for example `docs(uhpc017): add <title> spec`.

Write the spec as a standalone artifact. The agent that implements it will not have access to the drafting conversation, this file, or any other context; the spec itself must carry enough information to implement the decision without inferring the design from a downstream repository. If the design depends on a decision that has not been made, record that as an open question instead of silently choosing an implementation-specific answer.

## When you're handed a spec to implement

You will usually receive the spec as a standalone artifact, with no other context from the agent or conversation that produced it. Treat the spec as the design authority, and the implementation repositories as downstream consumers of that design.

1. **Read the design first.** Read the target spec end-to-end, including evaluated alternatives, risks, future work, and further information. Then read every spec it cross-links, and any specs those link to that constrain the design.
2. **Extract an implementation plan.** Identify the normative requirements, affected components and repositories, public interfaces, compatibility or migration requirements, and the tests needed to demonstrate the behavior.
3. **Inspect downstream repositories for context only.** Use their current code to locate integration points, understand existing behavior, and identify what needs to change. Do not treat existing implementation as the source of truth or silently preserve behavior that conflicts with the spec.
4. **Resolve conflicts explicitly.** If the spec is ambiguous or the existing repositories expose a constraint the spec does not address, do not invent a design from the code. Record the issue, ask for clarification when it affects the decision, or make the smallest conservative interpretation and document it. If the decision itself needs to change, update or supersede the spec before implementing the new behavior.
5. **Implement and validate in the downstream repository or repositories.** Keep implementation changes in those repositories; do not rewrite the design spec merely to match the current code. Add or update tests and documentation as required by the spec.
6. **Report deviations and follow-up work.** Clearly identify any requirement that could not be implemented, any intentional deviation, and any spec amendment or cross-repository work that is still needed.
