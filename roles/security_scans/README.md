# Ansible Role: Security Scans (Lynis & OpenSCAP)

An [Ansible Galaxy](https://galaxy.ansible.com/) role to run Lynis and OpenSCAP scans. The results can be seen in the stdout during provisioning and the full reports are stored in /var/log/security-scans inline with the retention policy.

## Role Variables

The role defaults (see [defaults/main.yml](defaults/main.yml)) specify the:
- list of required packages
- base directory (default `/var/log/security-scans`)
- oscap configuration
- lynis configurations
- retention policy (default, 12 complete scans, 3 incomplete scans from the past week)

A full list of available OpenSCAP OS targets and profiles can be viewed here:
[https://github.com/ComplianceAsCode/content/tree/master/products](https://github.com/ComplianceAsCode/content/tree/master/products)

`security_scans_oscap_target_os` can be used to target an alterate OS (e.g. rhel8, rhel10, alinux2 (Amazon), al2023 (Amazon)).

`security_scans_oscap_profile` is used to choose a different profile (e.g. cis (CIS Server Level 2), stig (DISA STIG)).

## Example Requirements File

```yml
---

collections:
  - name: companieshouse.security
    version: "1.0.0"
```

## Example Playbook

```yml
---

- name: Run Lynis and OpenSCAP scans
  hosts: all
  roles:
    - role: companieshouse.security.security_scans
```

## License

This project is subject to the terms of the [MIT License](LICENSE).
