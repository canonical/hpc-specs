# Contributing to Charmed HPC's specifications

Do you want to contribute a specification to [Charmed HPC](https://github.com/canonical/charmed-hpc)?
You've come to the right place then! __Here is how you can get involved.__

Please take a moment to review this document so that the contribution
process will be easy and effective for everyone. Following these guidelines helps you communicate that you respect the maintainers managing Charmed HPC's specifications. In return, they will reciprocate that respect while addressing your issue or assessing your submitted specification and/or changes.

Have any questions? Feel free to ask them in the [Ubuntu High-Performance Computing Matrix chat](https://matrix.to/#/#hpc:ubuntu.com) or the [High-Performance Computing category on the Ubuntu Discourse](https://discourse.ubuntu.com/c/project/hpc/151).

### Table of Contents

* [Specification guidelines](#specification-guidelines)
* [Proposing a new specification](#proposing-a-new-specification)
* [Revising an existing specification](#revising-an-existing-specification)
* [Pull Requests and Contributing Process](#pull-requests-and-contributing-process)
* [Using the issue tracker](#using-the-issue-tracker)
* [Issues and Labels](#issues-and-labels)
* [Licensing](#licensing)


## Specification guidelines

A specification describes a design decision for Charmed HPC: what is being proposed, why it is
needed, and how it is expected to work. Specifications are design documents, not user-facing
documentation. Once a specification is implemented, any user-facing material belongs in
[canonical/charmed-hpc-docs](https://github.com/canonical/charmed-hpc-docs) instead.

Before writing a specification, please read through a couple of the existing specifications under
[`specs/`](./specs) to get a feel for the expected tone, structure, and level of detail. The
[Canonical Documentation Style Guide](https://docs.ubuntu.com/styleguide/en/index) is a good
reference for writing style.

Each specification lives in its own directory and is assigned a sequential `UHPC` index:

```
specs/UHPC 014 - Short descriptive title/
├── static/         # Optional. Images and other assets referenced by the specification.
└── uhpc014.md
```

* Specification directories are named `UHPC <index> - <title>`, where `<index>` is the next
available three-digit number and `<title>` matches the title of the specification.

* The specification itself is a single Markdown file named `uhpc<index>.md`. Some early
specifications use a `uhpc-<index>.md` filename; new specifications should not.

* Images and other assets belong in a `static/` subdirectory alongside the specification.

Every specification starts with front matter declaring its index and title, followed by a top-level
heading that repeats the title:

```markdown
---
index: UHPC014
title: Short descriptive title
---

# Short descriptive title

## Abstract

## Rationale

## Specification

## Further information
```

* __Abstract__ &mdash; a short summary of what the specification covers.

* __Rationale__ &mdash; why this change is needed, and what problem it solves.

* __Specification__ &mdash; the proposed design in detail. Use subsections, code blocks, and
diagrams as needed. If you considered other approaches, describe them and explain why they were
not chosen.

* __Further information__ &mdash; a numbered list of references, prior art, and related discussions.
Some specifications use a __References__ heading for this instead; either is fine.

Please wrap prose at around 100 characters to keep diffs readable.

## Proposing a new specification

New specifications are proposed as pull requests, where they are reviewed and either accepted or
declined.

1. Check the existing specifications and open pull requests to make sure the topic isn't already
covered or in flight.

2. Claim the next `UHPC` number not already used under [`specs/`](./specs) or claimed by an open
pull request. Indices are never reused, even if a proposal is declined.

3. Write the specification following the [specification guidelines](#specification-guidelines), then
open a pull request. Drafts are welcome while it is still taking shape.

For a substantial proposal, consider raising the idea in the [Matrix chat](https://matrix.to/#/#hpc:ubuntu.com)
or on [Discourse](https://discourse.ubuntu.com/c/project/hpc/151) first, to avoid writing up a
design the maintainers have already ruled out.

Not all proposals will be accepted, and spamming the maintainers will not improve a proposal's
chances; it may result in a temporary ban from the repository.

## Revising an existing specification

Revisions are also pull requests, and spelling corrections, clarifications, and fixes to details
that no longer reflect reality are a huge help.

Bear in mind that a specification records a decision that may already be implemented. Corrections
that leave the decision intact can be made in place, but changes that reverse or materially alter it
usually belong in a new specification that supersedes the old one. If you are unsure which applies,
say so in your pull request description.

## Pull Requests and Contributing Process

Pull requests should remain focused and not contain unrelated commits. A new specification should be
its own pull request, separate from revisions to other specifications.

1. [Fork](https://help.github.com/articles/fork-a-repo/) the project, clone your fork,
   and configure the remotes:

   ```shell
   # Clone your fork of the repo into the current directory
   $ git clone git@github.com:<your-username>/hpc-specs.git

   # Navigate to the newly cloned directory
   $ cd hpc-specs

   # Assign the original repo to a remote called "upstream"
   $ git remote add upstream git@github.com:canonical/hpc-specs.git
   ```

2. If you cloned the repository a while ago, pull the latest changes from the
upstream Charmed HPC specifications repository:

   ```bash
   $ git checkout main
   $ git pull upstream main
   ```

3. Create a new topic branch off the main development branch to
   contain your specification, change, or fix:

   ```bash
   $ git checkout -b <topic-branch-name>
   ```

4. Check that the front matter index, directory name, filename, and title all agree, and that links
and images render when previewing the Markdown on GitHub.

5. Commit your changes in logical chunks to your topic branch.

   Our project follows the
   [Conventional Commits specification, version 1.0.0](https://www.conventionalcommits.org/en/v1.0.0/),
   scoping commits that touch a single specification with its index, such as
   `docs(uhpc014): add short descriptive title spec`. You can use Git's
   [interactive rebase](https://help.github.com/articles/about-git-rebase/) feature to
   tidy up your commits before pushing them to your origin branch.

6. Locally merge or rebase the upstream development branch into your topic branch:

   ```shell
   git pull [--rebase] upstream main
   ```

7. Push your topic branch up to your fork:

   ```shell
   git push origin <topic-branch-name>
   ```

8. [Open a Pull Request](https://help.github.com/articles/about-pull-requests/) against the `main`
branch, following the prompts in the pull request template.

## Using the issue tracker

The issue tracker is the preferred way to raise gaps and inconsistencies in the specifications, and
to ask questions or start a discussion about a particular specification. For example:

* An area of Charmed HPC that no specification covers, but should.

* Two specifications that contradict each other, or a specification that contradicts itself.

* A specification that no longer reflects how Charmed HPC actually behaves.

* A specification whose intent is unclear, or that leaves a case unaddressed.

An issue does not need to come with a solution. Describing the gap clearly is useful on its own, and
the resolution &mdash; a new specification, a revision to an existing one, or no change at all
&mdash; can be worked out in the discussion. If you already know the fix, you are welcome to skip
straight to a pull request; see [proposing a new specification](#proposing-a-new-specification) and
[revising an existing specification](#revising-an-existing-specification).

Please also follow these guidelines for the issue tracker:

* Please __do not__ use the issue tracker for personal issues and/or support requests.
The [Ubuntu High-Performance Computing Matrix chat](https://matrix.to/#/#hpc:ubuntu.com) or the [High-Performance Computing category on the Ubuntu Discourse](https://discourse.ubuntu.com/c/project/hpc/151) are better places to get help for personal support requests.

* Please __do not__ use the issue tracker for bugs in Charmed HPC itself. Report those in the
repository of the affected component, such as
[canonical/slurm-charms](https://github.com/canonical/slurm-charms), rather than here. Issues
here should be about the specifications themselves.

* Please __do not__ derail or troll issues. Keep the discussion on track and have respect for the other
users/contributors.

* Please __do not__ post comments consisting solely of "+1", ":thumbsup:", or something similar.
Use [GitHub's "reactions" feature](https://blog.github.com/2016-03-10-add-reactions-to-pull-requests-issues-and-comments/)
instead.
  * The maintainers of Charmed HPC's specifications reserve the right to delete comments
  that violate this rule.

* Please __do not__ repost or reopen issues that have been closed. Please either
submit a new issue or browse through previous issues.
  * The maintainers of Charmed HPC's specifications reserve the right to delete issues
  that violate this rule.

## Issues and Labels

The Charmed HPC specifications issue tracker uses a variety of labels to help organize
and identify issues, such as `help wanted` for issues where we need help from the HPC community to
solve, and `good first issue` for issues that the maintainers have determined to be suitable for
first time contributors.

For a complete look at the labels used in this repository, see the
[project labels page](https://github.com/canonical/hpc-specs/labels).

## Licensing

By contributing your changes to Charmed HPC's specifications, you agree to license
your contribution under the [Creative Commons Attribution-Share Alike International license,
version 4.0](./LICENSE).
