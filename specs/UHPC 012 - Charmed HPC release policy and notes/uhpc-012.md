---
index: UHPC012
title: Release policy for Charmed HPC
---

# Release policy for Charmed HPC

## Abstract

This spec defines the release policy for Charmed HPC, a single product underpinned by a portfolio of charms and supporting artifacts.

The policy covers the versioning scheme (`<major>.<minor>`), the compatibility model (forward compatibility between minor releases of the same major release), the release schedule (two scheduled slots per six-month cycle: one major release batching breaking changes and new upstream releases, and one minor feature release), how unscheduled minor patch releases for bug and security fixes are instead gated on criticality, how these relate to upstream release cadences (e.g. Slurm), the Ubuntu base a release is built against, the soft-freeze / hard-freeze / release-day points that gate promotion between risk statuses, and the format and sections of the published release notes. The risk statuses themselves (`edge`, `beta`, `candidate`, `stable`) and the testing required to reach each are defined in the companion [UHPC 017](../UHPC%20017%20-%20Charm%20and%20solution%20promotion%20criteria%20for%20Charmed%20HPC/uhpc017.md). A companion [Release Notes Template](release-notes-template.md) accompanies this spec.

## Rationale

A consistent release policy is necessary to keep our community aware of upcoming major changes, bug fixes, and security updates, while ensuring that the community has some expected degree of stability.

Charmed HPC is a composition of multiple charms and supporting artifacts, some of which (e.g. the Slurm charms) operate upstream projects that follow their own release cadences. There must be a well-defined, Charmed-HPC-wide release policy that developers and users can reference to know:

* When new features can be expected (the one scheduled minor feature release per cycle) as distinct from when bug fixes and security updates can be expected (unscheduled minor patch releases, as needed).
* Which charm versions have been verified to work together as a single Charmed HPC release.
* What compatibility guarantees apply to a `Stable` channel (no breaking changes to integrations, configuration options, or actions).
* How components from different minor releases of the same major release behave when deployed together, and what is tested.
* How long a given Charmed HPC release is supported, and what "end of support" means.
* What information is published in release notes, and in what format.

Without such a policy, users cannot reliably schedule upgrades, security patching, or feature adoption across a Charmed HPC deployment, and the maintainers lack a shared reference for release planning, freeze dates, and channel promotion criteria.

## Specification

### Artifacts

Charmed HPC artifacts:

<!-- Update this list as the Charmed HPC portfolio evolves -->

- slurm-charms:
  - slurmctld
  - slurmd
  - slurmdbd
  - sackd
  - slurmrestd
- apptainer-operator
- filesystem-charms:
  - cephfs-server-proxy
  - filesystem-client
  - lustre-server-proxy
  - nfs-server-proxy
- lustre-server
- sssd-operator
- openssh-operator

An artifact is listed above if it is maintained by the Charmed HPC team and is deployed by the user as part of a Charmed HPC deployment. On this basis:

* Dependencies that are not user-facing are **not** listed as release artifacts and are not versioned in the release notes. For example, `charmed-hpc-libs` (see [UHPC 008](../UHPC%20008%20-%20%60charmed-hpc-libs%60%20for%20HPC%20charm%20development/uhpc008.md)) and the Slurm interface packages (see [UHPC 009](../UHPC%20009%20-%20Distributing%20Slurm%20interfaces%20as%20Python%20packages/uhpc009.md)) are internal development dependencies that a user does not interact with directly; versions are pinned within charm releases to account for updates in these dependencies.

#### Compatible third-party charms

Each Charmed HPC release records the third-party charm versions it has been tested against. These charms (e.g. MySQL, `smtp-integrator`) are not Charmed HPC artifacts and are not released by the Charmed HPC team, but a release is only supported in combination with the versions listed. The channel and revision of each are recorded in the release notes (see the [Release Notes Template](release-notes-template.md)).

Third-party charms:

<!-- Update this list as Charmed HPC dependencies evolve -->

- mysql - required by `slurmdbd`
- cos-lite - required by `slurmctld` for observability via `cos-agent`; Kubernetes, cross-model
- authentik-server - required by `sssd` for identity; Kubernetes, cross-model; optional
- smtp-integrator - required by `slurmctld` for email notifications (see [UHPC 006](../UHPC%20006%20-%20User%20email%20notifications%20in%20Charmed%20Slurm/uhpc-006.md)); optional

### Versioning scheme

Version format: `<major>.<minor>`.

Charmed HPC has **three kinds of release**. One is a major release; the other two are **minor releases**, distinguished as the **minor feature release** and the **minor patch release**:

| Kind | Version change | Timing | Contains |
|------|----------------|--------|----------|
| **Major release** | `X.Y` → `X+1.0` | Scheduled, once per six-month cycle | Breaking changes (integrations, configuration options, actions, Ubuntu base) and/or new upstream releases (e.g. Slurm, Lustre) |
| **Minor feature release** | `X.Y` → `X.Y+1` | Scheduled, once per six-month cycle | New features only; no breaking changes, no new upstream releases |
| **Minor patch release** | `X.Y` → `X.Y+1` | Unscheduled, as needed; timing gated on criticality | Bug fixes and/or security fixes only; no new features, no breaking changes, no new upstream releases |

The two kinds of minor release differ in **what they may contain** and in **how they are scheduled**, but not in how they are numbered:

* A **minor feature release** is a planned release slot. It is the *only* way new features reach users within a major release, it happens exactly once per cycle, and it is subject to the freeze points in [Release cycle and feature freezes](#release-cycle-and-feature-freezes).
* A **minor patch release** is a response to a defect. There is no fixed number of minor patch releases per cycle - there may be none, or several - and they are not subject to freeze points. A minor patch release never adds a feature, so adopting one never changes the feature set of a deployment.

Both increment the same minor component, so the version number alone does not distinguish a minor feature release from a minor patch release. The release notes state which kind a release is (see [Release notes sections](#release-notes-sections)).

Example sequence of Charmed HPC release numbers:

- `1.0` - major release (initial)
- `1.1` - minor patch release (security fix, issued three weeks later)
- `1.2` - minor feature release (the cycle's scheduled feature slot)
- `1.3` - minor patch release (bug fix)
- `2.0` - major release (next six-monthly slot; new upstream releases and/or breaking changes)
- `2.1` - minor feature release

#### Supported versions

The release notes for each Charmed HPC release list the supported Ubuntu base, the supported Juju version range, and the versions of each Charmed HPC artifact and third-party charm included in or tested with the release. Only the listed versions are tested together as a release.

#### Compatibility between minor releases

Charmed HPC components from different minor releases of the **same major release** are designed to be **forward compatible**: a component from an older minor release can interact with, and accept data and components from, a newer minor release of the same major release, although features introduced in the newer minor release are unlikely to be available to it.

Forward compatibility within a major release is achieved by holding the following stable for the lifetime of that major release:

* **Component interfaces** - the integrations exchanged between Charmed HPC charms. Interfaces may gain optional additions, but existing fields and semantics do not change.
* **Ubuntu base** - see [Ubuntu base support](#ubuntu-base-support).
* **Upstream bases** - the major upstream versions the release is built and tested against. For example, all Charmed HPC `1.X` releases are built and tested against Slurm 25.11.

Changes to any of the above are breaking changes and are therefore only made in a new major release. There is no compatibility promise between components from **different major releases** (e.g. mixing `1.X` and `2.X` components); such combinations are not supported.

#### Feature availability and deprecation within a major release

Following from the forward compatibility statement above, a feature introduced in a minor release is **not** made available to previous minor releases. To use a new feature, all components that participate in it must be refreshed to the minor release that introduced it, or later.

A new feature may deprecate an existing feature within the same major release. When this happens:

* The deprecation is announced in the release notes of the release that introduces the replacement, together with the recommended alternative.
* The deprecated feature continues to be supported for an announced period of time or number of releases, which is stated at the point of deprecation.
* Removal of the deprecated feature is a breaking change and therefore only occurs in a new major release, at or after the end of the announced support period.

#### Mixed-version deployments

The number of possible combinations of Charmed HPC component versions makes exhaustive cross-component version testing impractical, so **mismatched component versions are not officially tested**. Testing is performed against the set of versions listed in a single release's release notes.

Mismatched versions within the same major release are intended to work by virtue of the forward compatibility design described above, but are not guaranteed. Issues encountered with mismatched component versions are accepted on GitHub (or Discourse) and are used to improve compatibility.

#### Ubuntu base support

Each Charmed HPC major release is built against the **latest Ubuntu LTS** release at the time it is cut, and all minor releases within that major release use the same base. A given Charmed HPC release supports a **single** Ubuntu base; running a release on any other base, including an older LTS, is not supported. The supported base is listed in the release notes for each release.

Moving to a new Ubuntu base is a breaking change, so a new base is only introduced in a new major release.

### Release cadence

Charmed HPC has **two scheduled release slots per six-month cycle** (four per year): **one major release and one minor feature release**, alternating approximately every three months. Cycles are late May to early October and late October to early May.

* **Major releases** (`X+1.0`) occupy **one scheduled slot per cycle**. A major release batches together breaking changes and/or new upstream releases of the underlying software (e.g. Slurm, Lustre); for example, a major release cut in October 2027 would include Slurm 27.05. Breaking changes and new upstream releases are held back from minor releases until the next major release. If there are no breaking changes or new upstream releases to batch, that slot is a minor feature release instead.
* **Minor feature releases** (`X.Y+1`) occupy the **other scheduled slot per cycle**, intended to be between major releases. Each contains new features that introduce no breaking changes.

Minor patch releases are **not** part of this schedule and do not consume a slot - see [Minor patch releases](#minor-patch-releases) below.

#### Minor patch releases

Minor patch releases (bug and security fixes) are **unscheduled**. They are cut as needed, outside the two scheduled slots, and their timing is gated on the criticality of the issue being fixed rather than on the cycle calendar. A cycle may contain any number of minor patch releases, including none.

#### Release channels and branches

Since Charmed HPC is a set of charms rather than a single charm, release channels apply to each constituent charm individually. The channel and branch model - tracks, the `edge`/`beta`/`candidate`/`stable` risk ladder, and the track-to-branch mapping - is defined in [UHPC 017](../UHPC%20017%20-%20Charm%20and%20solution%20promotion%20criteria%20for%20Charmed%20HPC/uhpc017.md).

* No breaking changes will be made to integrations, configuration options, or actions in a stable channel of a charm.

#### Release cycle and feature freezes

Two distinct concepts drive the release cycle: the **risk status** a charm can be published at, and the **freeze points** in time that gate promotion between them. The risk statuses (`edge`, `beta`, `candidate`, `stable`) and the testing required to reach each are defined in [UHPC 017](../UHPC%20017%20-%20Charm%20and%20solution%20promotion%20criteria%20for%20Charmed%20HPC/uhpc017.md).

Freeze points apply **only to the two scheduled release slots**: major releases and minor feature releases. Minor patch releases are not subject to freeze points; their timing is set by criticality, as described in [Minor patch releases](#minor-patch-releases).

##### Freeze points

Freeze points are the dates by which the **final** round of testing for a risk status must be complete. Testing is not confined to these dates: charms are tested against the rest of the Charmed HPC set throughout the cycle, and a charm may complete Beta- or Candidate-level testing well before the corresponding freeze. The freeze is the point at which the last such round must have finished for a charm to be included in the release at that risk status.

* **Soft freeze** — the date by which final Beta-level testing must be complete. New feature work targeting this release stops, and each charm that has passed testing is promoted from Edge to **Beta**. Development of features targeting the *next* release continues.
* **Hard freeze** — the date by which final Candidate-level testing must be complete. Each charm that has passed testing is promoted from Beta to **Candidate**.
* **Release day** — the date by which final Stable-level testing must be complete. All charms that have passed testing are promoted from Candidate to **Stable**.

Given the variety of charms, the Candidate/Stable for a given charm may be the same as for the prior release.

Freeze dates are set by the Charmed HPC team during cycle planning. The soft freeze is set **two months before release day**, so that Candidate-level testing has time to reveal issues before the release is cut. Because scheduled releases are three months apart, development overlaps: work targeting the next release begins at the previous release's soft freeze.

```mermaid
gantt
  title Example Charmed HPC release schedule
  dateFormat YYYY-MM
  todayMarker off

  section X.0 major (Slurm 26.05)
      Main dev work/Beta-level testing                        :f1, 2026-05, 2026-08
      Soft freeze/Beta                                        :crit, milestone, v1, 2026-08, 0d
      Candidate-level testing                                 :f2, 2026-08, 2026-09
      Hard freeze/Candidate                                   :crit, milestone, v2, 2026-09, 0d
      Stable-level testing                                    :f3, 2026-09, 2026-10
      Release day X.0/Stable                                  :crit, milestone, r1, 2026-10, 0d
  section X.1 minor feature release
      Main dev work/Beta-level testing                        :f4, 2026-08, 2026-11
      Soft freeze/Beta                                        :crit, milestone, v3, 2026-11, 0d
      Candidate-level testing                                 :f5, 2026-11, 2026-12
      Hard freeze/Candidate                                   :crit, milestone, v4, 2026-12, 0d
      Stable-level testing                                    :f6, 2026-12, 2027-01
      Release day X.1/Stable                                  :crit, milestone, r2, 2027-01, 0d
  section Ubuntu
      Resolute Raccoon 26.04 LTS                              :2026-04, 2027-08
  section Upstream
      Slurm 26.05 released by SchedMD                       :milestone, a1, 2026-05, 0d
      Slurm 26.11 released by SchedMD                       :milestone, a2, 2026-11, 0d
```

<!--  
section X+1.0 major (Slurm 26.11)
    Main dev work/Beta-level testing                        :f7, 2026-11, 2027-02
    Soft freeze/Beta                                        :crit, milestone, v5, 2027-02, 0d
    Candidate-level testing                                 :f8, 2027-02, 2027-03
    Hard freeze/Candidate                                   :crit, milestone, v6, 2027-03, 0d
    Stable-level testing                                    :f9, 2027-03, 2027-04
    Release day X+1.0/Stable                                :crit, milestone, r3, 2027-04, 0d
section X+1.1 minor feature release
    Main dev work/Beta-level testing                        :f10, 2027-02, 2027-05
    Soft freeze/Beta                                        :crit, milestone, v7, 2027-05, 0d
    Candidate-level testing                                 :f11, 2027-05, 2027-06
    Hard freeze/Candidate                                   :crit, milestone, v8, 2027-06, 0d
    Stable-level testing                                    :f12, 2027-06, 2027-07
    Release day X+1.1/Stable                                :crit, milestone, r4, 2027-07, 0d
-->


<!---
### Support life-cycle

Bug and security fix support for each Charmed HPC release is tied to the **Ubuntu LTS** release it is built against, and is provided under the terms of **Ubuntu Pro** support.

> **[DECISION NEEDED]** With a major release every six months, tying support to the Ubuntu LTS would leave several major releases in support at once. How many major releases are supported concurrently, and does each remain supported for the full lifetime of its Ubuntu LTS?

> **[DECISION NEEDED]** Confirm that Charmed HPC is in scope for Ubuntu Pro, and at which tier, and link to the authoritative statement of those terms. The support commitment published in the release notes should cite it directly.

> **[DECISION NEEDED]** SchedMD supports each Slurm release for 18 months. Tying Charmed HPC support to the Ubuntu LTS cycle is a significantly longer commitment, so a Charmed HPC release would remain in support after upstream support for the Slurm version it contains has ended. This spec needs to state how bug and security fixes are provided for the Slurm charms within a supported Charmed HPC release once upstream support has ended.
-->

### Documentation and Release Notes

Warnings/limitations that will be included in the published documentation alongside the release notes:

* Charmed HPC components from different minor releases of the same major release are designed to be forward compatible: older components can interact with newer ones, but features introduced in a newer minor release are not available to components from an older minor release
* Only the set of versions listed in a single release's release notes is tested together. 
  * Issues encountered when running mismatched component versions should be opened on GitHub (or Discourse); they are accepted and used to improve compatibility.
* There is no compatibility promise between components from different major releases; such combinations are not supported
* Deprecated features remain supported for the period of time or number of releases announced at the point of deprecation, and are only removed in a new major release

#### Release Notes Template

See the [Release Notes Template](release-notes-template.md) for the template used when drafting a new release's notes.

#### Release notes sections

General release notes sections for Charmed HPC:

* Release summary, stating the **release type**: major release, minor feature release (scheduled), or minor patch release (unscheduled)
* Artifacts and versions included in the release
* What's new (features and improvements) - omitted for minor patch releases, which add no features
* Bug fixes and security fixes
* Requirements and compatibility (Ubuntu base, Juju version range, compatible third-party charm versions)
* Backwards incompatible changes
* Deprecated features
* Known issues
<!--* Upgrade notes (including `juju refresh` instructions)-->
<!--* Support lifecycle-->
* Acknowledgements

<!--#### Upgrades

Only upgrades between **minor versions** within the same major release are supported. There is no in-place upgrade path between major releases.

> **[DECISION NEEDED]** Define the migration or replacement path between major releases, given that in-place major upgrades are not supported. Is the expectation that users deploy a new cluster alongside the existing one and migrate, and what guidance and tooling is provided?

> **[DECISION NEEDED]** Are minor upgrades required to be sequential (e.g. `.1` to `.2` to `.3`), or may versions be skipped? Review proposed supporting only sequential upgrades initially.

Refreshing all charms in a deployment to the same Charmed HPC release is recommended, since only that combination is tested. Refreshing a subset of charms to a newer minor release within the same major release is expected to work by virtue of forward compatibility, but is not officially tested, and features introduced in the newer minor release are unavailable until all participating charms are refreshed.

> **[DECISION NEEDED]** A refresh necessarily passes through a state where some charms are at the new release and others are not. Is this transient state supported during an upgrade, and if so, what refresh ordering is required?

> **[DECISION NEEDED]** Are downgrades supported, and if so between which versions?

> **[DECISION NEEDED]** How are database upgrades handled during a refresh, for example the `slurmdbd` schema and its MySQL backend? Define the required ordering of charm refreshes and any backup steps a user must take beforehand.
-->

## References
* [Canonical product release cycles](https://ubuntu.com/about/release-cycle#ubuntu)
