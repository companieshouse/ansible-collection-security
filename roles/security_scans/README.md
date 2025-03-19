# Ansible Role: Security Scans (OpenSCAP & Lynis)

An [Ansible Galaxy](https://galaxy.ansible.com/) role to install OpenSCAP and Lynis, then run the scans. The results can be seen in the stdout during provisioning, and the full CIS Level 1 report is stored in ~ with a datetime.

## Role Variables

The role defaults (see [defaults/main.yml](defaults/main.yml)) specify the:
- list of required packages
- oscap configuration
- lynis configurations

To use this on a distribution other than the default (`rhel9`), override the `security_scans_oscap_target_os` role variable in your playbook e.g.

```yaml
security_scans_oscap_target_os: rhel7
```

If you're struggling to target your os, enable debugging to list the available streams:
```yaml
security_scans_oscap_list_available_datastreams: true
```

You can also change the cis_profile and level:

```yaml
security_scans_oscap_profile_type: cis_server  # Options: cis_server, cis_workstation, stig
security_scans_oscap_cis_level: 1  # For CIS profiles: 1 or 2, ignored for STIG
```

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

- name: Install required packages for oscap and lynis then run both scans
  hosts: all
  roles:
    - role: companieshouse.security.security_scans
```

## License

This project is subject to the terms of the [MIT License](LICENSE).
