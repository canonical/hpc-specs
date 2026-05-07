---
index: UHPC013
title: SSH access for login nodes
---

# SSH access for login nodes

## Abstract

This spec surveys our current set of options to enable publically exposing Charmed HPC's
login nodes using the SSH protocol, such that users can interact with the cluster
remotely without relying on Juju's internal SSH key management.

## Rationale

Charmed HPC requires a method to properly expose login nodes publically, since
cluster administrators would need to manually implement SSH access and authentication
otherwise.

However, we don't currently have a preferred way to enable this feature, so
this first step entails collecting all the required information about enabling
SSH access for a Juju-managed machine.

## Specification

Three methods of enabling SSH access were investigated. This is not an exhaustive
list, since there are thousands of ways to configure SSHD, so we can assume that
an [OpenLDAP]-compliant server is acting as the source of truth for user management
in the cluster, which bounds the amount of options we have. Another assumption we
can make is having an [OIDC]-compliant server in place for Single Sign-On (SSO)
workflows, which expands a bit the amount of ways we can set up SSH access.

[OpenLDAP]: https://www.openldap.org/
[OIDC]: https://openid.net/developers/how-connect-works/

### Preliminary work

No preliminary work seems to exist in the Juju ecosystem aside from the automatic
[SSH key management](https://documentation.ubuntu.com/juju/3.6/howto/manage-ssh-keys/)
Juju does.

### Manual SSH management

The naive approach would be to make cluster administrators manually manage SSH keys
on the login nodes. This can be done by modifying the [`authorized_keys`] file
for every user's home directory with the public SSH keys of that user. However,
this is very prone to error and a chore if hundreds or thousands of users need
to be managed.

We might be able to facilitate this process with some custom Charm actions on
the login node, or even with a custom `ssh-keys` subordinate charm that does all
the required actions for the administrator, but it might still only be useful if
the cluster only needs to manage a handful of users.

[`authorized_keys`]: https://manpages.debian.org/experimental/openssh-server/authorized_keys.5.en.html#AUTHORIZED_KEYS_FILE_FORMAT

### LDAP-based SSH authentication

This method entails storing every user's public SSH keys on a centralized LDAP server,
and configuring SSH to query SSSD on every SSH connection
to check if the provided private SSH key is valid for that request.

[Many][lpk1] [sources][lpk2] [use][lpk3] the "openssh-lpk" schema on the LDAP server to allow
storing public SSH keys on the database's entries:

```ldif
 attributetype: ( 1.3.6.1.4.1.24552.500.1.1.1.13
      NAME 'sshPublicKey'
      DESC 'MANDATORY: OpenSSH Public key'
      EQUALITY octetStringMatch
      SYNTAX 1.3.6.1.4.1.1466.115.121.1.40
      )
    objectClass: ( 1.3.6.1.4.1.24552.500.1.1.2.0
      NAME 'ldapPublicKey'
      SUP top AUXILIARY
      DESC 'MANDATORY: OpenSSH LPK objectclass'
      MAY ( sshPublicKey $ uid )
      )
```

This is only necessary to include on the LDAP server side, because the default LDAP schema
doesn't understand the `sshPublicKey` and `ldapPublicKey` object classes.
Having this in place and given the database is populated with all the public keys of
every user, we can leverage SSH's configuration to query SSSD every time
a user tries to connect to the server:

```
# /etc/ssh/sshd_config
AuthorizedKeysCommand /usr/bin/sss_ssh_authorizedkeys
AuthorizedKeysCommandUser nobody
```

(taken from <https://manpages.debian.org/testing/sssd-common/sss_ssh_authorizedkeys.1.en.html>)

Automatic home directory creation can also be enabled using `pam-auth-update`:

```shell
sudo pam-auth-update --enable mkhomedir
```

However note that if the home directory is part of a remote filesystem such as
NFS, creating the directory automatically might or might not be difficult depending
on the specific permissions and security features set on the
NFS side (`no_root_squash` is one example of this). In that case, we would have to default
to script user creation with some other detection methods based on timers or
LDAP server data changes.

OnePAM's ["LDAP Authentication with OpenSSH][lpk3] pretty much covers the full
LDAP + SSHD + SSSD setup.

We would ideally create a new configuration flag on the `SSSD` charm to enable this
type of authentication, since not all cluster admins will want to use this.

[lpk1]: https://kb.symas.com/en_US/reference/using-ldap-as-an-ssh-public-key-store-create-a-custom-schema
[lpk2]: https://github.com/AndriiGrytsenko/openssh-ldap-publickey
[lpk3]: https://onepam.com/tools/ldap-openssh-guide

### OIDC-based SSH authentication

Given that there is an OpenID Connect server available on the cluster, we can leverage that
to offer a secure way to access login nodes with ephemeral SSH keys. This is enabled
by [`opkssh`](https://github.com/openpubkey/opkssh), which does all the heavylifting
of authenticating using the OIDC protocol and generating an ephemeral SSH keypair
to connect to the server.

The login flow is as follows:

1. User logs in against the OpenID Connect provider

   ```shell
   opkssh login --provider="https://authentik.local/application/o/opkssh/,ClientID123"
   ```

   This opens up a browser to authenticate against the OpenID provider (Authentik,
   Authelia, etc.)

   Alternatively, we could provide the user with the required configuration
   to authenticate against the provider such that they can add it to their
   `~/.opk/config.yml` file:

   ```yaml
   - alias: hello
     issuer: https://issuer.hello.coop
     client_id: app_xejobTKEsDNSRd5vofKB2iay_2rN
     scopes: openid email
     access_type: offline
     prompt: consent
     redirect_uris:
       - http://localhost:3000/login-callback
       - http://localhost:10001/login-callback
       - http://localhost:11110/login-callback
   ```

2. User ssh's to the login node

   ```shell
   ssh user@server.com
   ```

A very notable downside of this method is that the user needs to install the
`opkssh` binary to be able to login.

All the configuration options are described on the project's [README].

[README]: https://github.com/openpubkey/opkssh#server-configuration

### Alternatives

- [Authentik]-based SSH access

  Authentik itself can be used to authorize ssh access to machines running
  the [Authentik agent]. This allows users to use login nodes by running

  ```shell
  ak ssh <hostname>
  ```

  The biggest downside of this method is that it ties our SSH authentication method
  to Authentik, which might be undesirable if we want to make user management
  more "modular".

[Authentik]: https://docs.goauthentik.io
[Authentik agent]: https://docs.goauthentik.io/endpoint-devices/authentik-agent/agent-deployment/

- [`motley-cue`](https://github.com/dianagudu/motley_cue)

  An alternative way to do OIDC autentication for SSH access. It's kind of the same
  method that `opkssh` uses, but it additionally wraps everything with the [`mccli`]
  tool to offer a slightly easier login workflow. Downside is that it's pretty barebones
  documentation-wise.

[`mccli`]: https://mccli.readthedocs.io/
