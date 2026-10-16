# Charmed HPC <major>.<minor> release notes

Release date: YYYY-MM-DD

## Summary

Release type: Major release / Minor feature release (scheduled) / Minor patch release (unscheduled)

<Brief overview of this release, including the primary focus (for example, new Ubuntu base,
new upstream releases such as Slurm or Lustre, new features, bug fixes, security updates).>

## Artifacts in this release

| Artifact | Track | Revision | Notes |
|----------|-------|----------|-------|
| sackd | <track> | <revision> | |
| slurmctld | <track> | <revision> | |
| slurmd | <track> | <revision> | |
| slurmdbd | <track> | <revision> | |
| slurmrestd | <track> | <revision> | |
| lustre-server | <track> | <revision> | |
| cephfs-server-proxy | <track> | <revision> | |
| filesystem-client | <track> | <revision> | |
| lustre-server-proxy | <track> | <revision> | |
| nfs-server-proxy | <track> | <revision> | |
| apptainer-operator | <track> | <revision> | |
| openssh-operator | <track> | <revision> | |
| sssd-operator | <track> | <revision> | |

## What's new

Omit this section in a minor **patch** release; minor patch releases do not add features or
improvements.

### New features

- Feature description and the artifact(s) it affects.

### Improvements

- Improvement description.

## Bug fixes

Always include this section. Any kind of release may contain bug fixes; if this release
contains none, state so explicitly.

- Bug description, the artifact(s) affected, and a link to the issue.

## Security fixes

Always include this section. Any kind of release may contain security fixes; if this
release contains none, state so explicitly.

- CVE or advisory reference, the artifact(s) affected, and the severity.

## Requirements and compatibility

This release is supported only on the Ubuntu base and Juju versions listed below, and
only with the artifact and third-party charm versions listed in these release notes.
The set of versions listed here is the combination that has been tested together.

Components from different minor releases of the same major release (<major>.x) are
designed to be forward compatible: older components can interact with newer ones, but
features introduced in a newer minor release are not available to components from an
older minor release. To use a new feature, all components that participate in it must be
refreshed to the minor release that introduced it, or later.

Mismatched component versions are not officially tested. Issues encountered with newer
and/or unvalidated component versions can be opened on GitHub, and community
contributions to address compatibility bugs are welcome. For issues involving older
and/or unvalidated component versions, the common resolution will be to update to a
supported component version set.

There is no compatibility promise between components from different major releases.

### Upstream versions

All <major>.x releases are built and tested against the upstream versions below.
The upstream major version does not change within a major release. Minor versions of upstream projects may change across minor releases due to patches.

| Upstream project | Version |
|------------------|---------|
| Slurm | <version> |
| Lustre | <version> |
| OpenSSH | <version> |
| SSSD | <version> |
| Apptainer | <version> |

### Supported Ubuntu base

- Ubuntu <version> LTS (<codename>)

### Juju version

- Minimum Juju version: <version>
- Maximum Juju version: <version>

### Compatible third-party charm versions

These charms are not released by the Charmed HPC team. This release is supported only
in combination with the versions listed below. The notes describe what each charm is
used for; whether a given charm is needed depends on the deployment configuration.

#### Third-party machine charms

| Charm | Channel | Revision | Use |
|-------|---------|----------|-------|
| cephfs | <channel> | <revision> | For filesystems |
| ldap-integrator | <channel> | <revision> | For identity; to integrate with an existing LDAP deployment |
| microceph | <channel> | <revision> | For filesystems |
| mysql | <channel> | <revision> | Backing database for Slurm |
| opentelemetry-collector | <channel> | <revision> | For observability; metric collection |
| smtp-integrator | <channel> | <revision> | For email notifications |

#### Third-party Kubernetes charms

These charms are deployed on Kubernetes and integrated cross-model.

| Charm | Channel | Revision | Use |
|-------|---------|----------|-------|
| authentik-server | <channel> | <revision> | For identity |
| authentik-worker | <channel> | <revision> | For identity |
| authentik-ldap-outpost | <channel> | <revision> | For identity |
| postgresql-k8s | <channel> | <revision> | For identity; backing database for Authentik |
| traefik-k8s | <channel> | <revision> | Enables ingress for COS and Authentik deployments |

#### Canonical Observability Stack (COS)

This release is supported with COS track <track>. The charm
channels and revisions that make up that track are published by the Observability team:

- [COS <track> release notes](https://documentation.ubuntu.com/observability/track-<track>/release-notes/)
- [COS <track> components reference](https://documentation.ubuntu.com/observability/track-<track>/reference/cos-components/)

## Breaking changes

Always include this section. Breaking changes are only made in a major release; in a
minor release, state explicitly that none are included.

- Change description and required user action, if any.

## Deprecated features

- Feature or option that is deprecated, with recommended alternative and anticipated removal date.
- Explicitly state if no features are deprecated in the release.

## Known issues

Always include this section. If there are no known issues, state so explicitly.

- Issue description and any available workaround.

<!--## Upgrade notes

Only upgrades between minor versions within the same major release are supported.
Refreshing all charms to this release is recommended, since that is the combination
that has been tested; features introduced in this release are unavailable until all
participating charms have been refreshed.

## Support lifecycle

| Release | Release date | End of support |
|---------|--------------|----------------|
| <major>.<minor> | YYYY-MM-DD | YYYY-MM-DD |

Bug and security fix support is tied to the Ubuntu LTS release this version is built against, and is provided under the terms of Ubuntu Pro support.
-->
## Acknowledgements

<Acknowledge upstream contributions to artifacts (Slurm, Lustre, and others)>
