package_overrides
=================

Explicitly updates specified packages to latest available, useful in with locked package amis (e.g. al2023), includes optional minimum ver enforcement.

Listed packages are only updated if already present, this isn't intended for installing new packages.

Place this role early in the play, so later roles can benefit from the updated package(s).

Supported (tested) operating systems:
- RHEL 8 & 9
- Amazon Linux 2 & 2023

Requirements
------------

- Ansible `>= 2.15.6`
- Access to the package repositories
- On yum hosts, `yum-utils` is installed when `min_version` is used (for `repoquery`)

Role Variables
--------------

| Variable                 | Description                                                         | Default |
| ------------------------ | ------------------------------------------------------------------- | ------- |
| package_overrides_list   | Packages to update; see the fields below. Does nothing when empty  | `[]`    |

Each entry in `package_overrides_list` accepts:

| Field         | Required | Description                                                                                                         |
| ------------- | -------- | ------------------------------------------------------------------------------------------------------------------- |
| `name`        | yes      | RPM package name, e.g. `ca-certificates`                                                                            |
| `releasever`  | no       | dnf only: release to resolve against, e.g. `latest` on Amazon Linux 2023                                            |
| `min_version` | no       | Minimum installed version, as `version` or `version-release`. Compared using RPM ordering; the role fails if not met |
| `reason`      | no       | CVE or advisory ID; shown in task output and failure messages                                                       |

`min_version` is checked with `repoquery`, so `2025.2.80_v9.0.305-91` is compared the same way dnf and yum compare packages. Use `version-release` where the vendor fix only bumps the release.

`releasever: latest` may also pull dependencies from the newer release.

Example Playbook
----------------

Amazon Linux 2023:

```yaml
- hosts: amzn2023-base
  become: true
  roles:
    - role: companieshouse.security.package_overrides
      vars:
        package_overrides_list:
          - name: ca-certificates
            releasever: latest
            min_version: "2025.2.80"
            reason: CVE-2026-113470
```

RHEL 9:

```yaml
- hosts: rhel9-base
  become: true
  roles:
    - role: companieshouse.security.package_overrides
      vars:
        package_overrides_list:
          - name: ca-certificates
            min_version: "2025.2.80_v9.0.305-91"
            reason: RHSB-2026-012
```
