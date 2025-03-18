# Ansible Role: OpenSCAP & Lynis Scans

An [Ansible Galaxy](https://galaxy.ansible.com/) role to install OpenSCAP and Lynis, then run the scans. The results can be seen in the stdout during provisioning, and the full CIS Level 1 report is stored in ~ with a datetime.

## Role Variables

The role defaults (see [defaults/main.yml](defaults/main.yml)) specify the:
- list of required packages
- oscap configuration for the relevant linux distribution (rhel 9 as default)
- the lynis configurations, inlcuding repo: https://github.com/CISOfy/lynis.git

You can override these defaults. You'll most likely need to use this on a distribution other than rhel9, in which case override the `oscap_content_path` role variable in your playbook e.g.

```yaml
oscap_content_path: /usr/share/xml/scap/ssg/content/ssg-rhel7-ds.xml
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

- name: Provision AWS CLI tool
  hosts: all
  roles:
    - role: companieshouse.security.oscap_lynis_scans
```

## License

This project is subject to the terms of the [MIT License](LICENSE).
