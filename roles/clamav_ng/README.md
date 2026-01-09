clamav_ng
=========

A one-for-all ClamAV role that installs and configures ClamAV for use within Companies House. This role will also attempt to utilise and, where necessary, clean-up legacy ClamAV configuration baked in to launch AMIs.

The role is written to support the major operating systems in use:
- RHEL
- CentOS
- Amazon Linux
- Ubuntu

As well as the various versions of ClamAV available from the respective vendor repository:
- 0.100.x 
- 0.103.x
- 1.4.x

Note: RHEL 6 support necessitates the use of either Ansible 2.9 or 2.10 due to the version of Python installed on the hosts. As a general guide, the following Ansible versions should work:
- RHEL 6, < Ansible 2.11
- RHEL 8 & 9, >= Ansible 2.10
- CentOS 7, >= Ansible 2.10
- Ubuntu 20.04 (Focal), >= Ansible 2.10
- Ubuntu 24.04 (Noble), >= Ansible 2.15


Requirements
------------

N/A

Role Variables
--------------

| Variable                               | Description                                                                   | Default                                   |
| -------------------------------------- | ----------------------------------------------------------------------------- | ----------------------------------------- |
| clamav_ng_install_packages             | A list of ClamAV packages to be installed via the package manager             | `["clamav", "clamav-freshclam", "clamd"]` |
| clamav_ng_crontab_spool_path           | The OS specific path in `/var/spool` to the user crontab files                | `/var/spool/cron`                         |
| clamav_ng_selinux_enabled              | Whether or not the target host will have SELinux enabled                      | `true`                                    |
| clamav_ng_selinux_booleans             | A list of SELinux booleans to be enabled if SELinux is Enforcing              | `["antivirus_can_scan_system"]`           |
| clamav_ng_db_directory                 | The directory in which the ClamAV database will be stored                     | `/var/lib/clamav`                         |
| clamav_ng_db_group                     | The system group that will own the database directory                         | `virusgroup`                              |
| clamav_ng_db_user                      | The system user that will own the database directory                          | `clamupdate`                              |
| clamav_ng_log_directory                | The directory in which ClamAV log files will be stored                        | `/var/log/clamav`                         |
| clamav_ng_log_file                     | Log file that `clamd` output will be written to                               | `clamd.log`                               |
| clamav_ng_log_group                    | The system group that will own the log directory                              | `virusgroup`                              |
| clamav_ng_log_user                     | The system user that will own the log directory                               | `"{{ clamav_ng_clamav_user }}"`           |
| clamav_ng_run_directory                | The `/var/run` directory in which ClamAV files will be stored                 | `/var/run/clamd.scan`                     |
| clamav_ng_run_socket_file              | Specific name of the socket file to be used for local connections             | `clamd.sock`                              |
| clamav_ng_run_group                    | System group that will own the runtime directory                              | `virusgroup`                              |
| clamav_ng_run_user                     | System user that will own the runtime directory                               | `"{{ clamav_ng_clamav_user }}"`           |
| clamav_ng_rhel_epel_install_from_url   | Whether the EPEL repository package will be installed from a provided URL     | `false`                                   |
| clamav_ng_rhel_epel_repo_file          | The full path to the on-disk EPEL repository filei                            | `/etc/yum.repos.d/epel.repo`              |
| clamav_ng_rhel_epel_repo_url           | Full URL to the appropriate `epel-release` install package                    | `"https://dl.fedoraproject.org/pub/epel/epel-release-latest-{{ ansible_facts.distribution_major_version }}.noarch.rpm"` |
| clamav_ng_rhel_epel_repo_gpg_key_url   | Full URL to the related GPG key used to sign the `epel-release` package       | `"https://dl.fedoraproject.org/pub/epel/RPM-GPG-KEY-EPEL-{{ ansible_facts.distribution_major_version }}"` |
| clamav_ng_clamav_config_file           | The name of the ClamAV `clamd` configuration file                             | `/etc/clamd.d/scan.conf`                  |
| clamav_ng_clamav_service_name          | The ClamAV service name                                                       | `"clamd@scan"`                            |
| clamav_ng_clamav_group                 | The system group that the ClamAV service will run as                          | `clamscan`                                |
| clamav_ng_clamav_user                  | The system user that the ClamAV service will run as                           | `clamscan`                                |
| clamav_ng_clamav_localsocketgroup      | The system user group that will own the ClamAV socket file                    | `virusgroup`                              |
| clamav_ng_clamav_logfilemaxsize        | The maximum log file size permitted before ClamAV will rotate the file        | `5M`                                      |
| clamav_ng_clamav_maxthreads            | The maximum number of threads ClamAV will spawn during scanning               | `"{{ ansible_processor_nproc + 1 }}"`     |
| clamav_ng_clamav_maxdirectoryrecursion | The maximum depth ClamAV will recursively scan in to directories              | `30`                                      |
| clamav_ng_clamav_exclude_paths         | System paths to be excluded from automated scanning                           | `["/dev/", "/proc/", "/sys/", "/run/", "/etc/", "/var/log/audit/", "/var/lib/selinux/", "/var/lib/amazon/ssm/ipc/"]` |
| clamav_ng_clamonacc_enabled            | Whether or not the ClamAV On-Access Scanner is enabled                        | `false`                                   |
| clamav_ng_clamonacc_service            | Whether ClamAV on-accerss scanner runs as a service                           | `true`                                    |
| clamav_ng_clamonacc_service_name       | The on-access scanner's service name                                          | `clamav-clamonacc`                        |
| clamav_ng_clamonacc_distro_options     | Distribution-specific configuration options for on-access scanning            | `[]`                                      |
| clamav_ng_clamonacc_include_paths      | System paths that are to be explicitly included in on-access scanning         | `[]`                                      |
| clamav_ng_freshclam_cron               | Whether or not a cron job will be added for ClamAV Freshclam                  | `false`                                   |
| clamav_ng_freshclam_service            | Whether or not ClamAV Freshclam will run as a service                         | `true`                                    |
| clamav_ng_freshclam_config_file        | The name of the Freshclam configuration file                                  | `/etc/freshclam.conf`                     |
| clamav_ng_freshclam_service_name       | The Freshclam service name                                                    | `clamav-freshclam`                        |
| clamav_ng_freshclam_group              | System group that will own Freshclam files                                    | `clamupdate`                              |
| clamav_ng_freshclam_user               | System user that will own Freshclam files                                     | `clamupdate`                              |
| clamav_ng_freshclam_logfilemaxsize     | The maximum log file size permitted before Freshclam will rotate the file     | `5M`                                      |
| clamav_ng_freshclam_privatemirror      | The URI to the private ClamAV database mirror                                 | `_None_`                                  |
| clamav_ng_clamdscan_enabled            | Whether or not the ClamD system scan job is enabled                           | `true`                                    |
| clamav_ng_clamdscan_log_file           | The log file that `clamdscan` output will be written to                       | `scan.log`                                |
| clamav_ng_clamdscan_scan_path          | The path from which to begin scanning                                         | `"/*"`                                    |
| clamav_ng_clamdscan_window_start_hour  | Defines the start of a window during which the system scan could be triggered | `1`                                       |
| clamav_ng_clamsdcan_window_end_hour    | Defines the end of a window during which the system scan could be triggered   | `4`                                       |

Dependencies
------------

- ansible.posix
- community.general

Example Playbook
----------------

```yaml
  - hosts: all
    roles:
       - { role: clamav_ng, clamav_ng_freshclam_privatemirror: "my-clamav-mirror.example.com" }
```

License
-------

MIT

