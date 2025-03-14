
# Ansible Role: RHEL 9 NTP Configuration for Hetzner Cloud

An Ansible Role that configures NTP (Chrony) on Red Hat Enterprise Linux 9 (and derivatives such as AlmaLinux and RockyLinux) servers, specifically optimized for Hetzner Cloud environments.

🇩🇪 **Note:** Made with ❤️ in Germany.

---

## Role Features

- Easy Foreman / Katello integration (Job Template included)
- Dynamic configuration of multiple NTP servers.
- DHCP-based NTP server sourcing.
- Optional hardware timestamping and RTC synchronization.
- Advanced logging capabilities.

## Requirements

- Ansible 2.14 or newer
- Supported Operating System: RHEL 9, AlmaLinux 9, Rocky Linux 9

## Role Variables

| Parameter                                | Required | User Input | Default                                  | Description                                                  |
|------------------------------------------|----------|------------|------------------------------------------|--------------------------------------------------------------|
| `n_hcloud_ntp_servers`                   | Yes      | Yes        | `ntp1.hetzner.de, ntp2.hetzner.com`      | Comma-separated list of NTP servers. Spaces are automatically trimmed (e.g., `server1.example.com, server2.example.com`). |
| `n_hcloud_ntp_allowed_subnets_enabled`   | No       | Yes        | `false`                                 | Enable or disable subnet-based access control.               |
| `n_hcloud_ntp_allowed_subnets`           | No       | Yes        | `10.0.0.0/8`                             | Allowed client subnet list (comma-separated, trimmed spaces). Example: `10.0.0.0/8,192.168.1.0/24`. |
| `n_hcloud_ntp_use_dhcp_enabled`          | No       | Yes        | `true`                                  | Enable use of DHCP provided NTP servers.                     |
| `n_hcloud_ntp_dhcp_sourcedir`            | No       | Yes        | `/run/chrony-dhcp`                       | Directory for DHCP-based NTP server info.                    |
| `n_hcloud_ntp_driftfile_enabled`         | No       | Yes        | `true`                                  | Enable driftfile logging.                                    |
| `n_hcloud_ntp_driftfile`                 | No       | Yes        | `/var/lib/chrony/drift`                  | Driftfile location.                                          |
| `n_hcloud_ntp_makestep_enabled`          | No       | Yes        | `true`                                  | Allow initial large time adjustments.                        |
| `n_hcloud_ntp_makestep_threshold`        | No       | Yes        | `1.0`                                    | Threshold (seconds) for stepping the clock.                  |
| `n_hcloud_ntp_makestep_limit`            | No       | Yes        | `3`                                      | Maximum steps Chrony can perform at boot.                    |
| `n_hcloud_ntp_enable_rtcsync`            | No       | Yes        | `true`                                  | Enable real-time clock synchronization (RTC).                |
| `n_hcloud_ntp_hwtimestamp_enabled`       | No       | Yes        | `false`                                  | Enable hardware timestamping on interfaces.                  |
| `n_hcloud_ntp_hwtimestamp_interfaces`    | No       | Yes        | `*`                                      | Interfaces for hardware timestamping, comma-separated.       |
| `n_hcloud_ntp_minsources_enabled`        | No       | Yes        | `false`                                  | Require minimum NTP sources.                                 |
| `n_hcloud_ntp_minsources`                | No       | Yes        | `2`                                      | Minimum required sources for synchronization.                |
| `n_hcloud_ntp_local_stratum_enabled`     | No       | Yes        | `false`                                  | Serve local stratum even if unsynchronized.                  |
| `n_hcloud_ntp_local_stratum`             | No       | Yes        | `10`                                     | Local stratum level served if unsynchronized.                |
| `n_hcloud_ntp_authselectmode_enabled`    | No       | Yes        | `false`                                  | Require authentication for NTP sources.                      |
| `n_hcloud_ntp_authselectmode`            | No       | Yes        | `require`                                | Authentication mode (`require`, `prefer`).                   |
| `n_hcloud_ntp_keyfile`                   | No       | Yes        | `/etc/chrony.keys`                       | File containing authentication keys.                         |
| `n_hcloud_ntp_ntsdumpdir`                | No       | Yes        | `/var/lib/chrony`                        | Directory to save NTS keys and cookies.                      |
| `n_hcloud_ntp_leapsecmode_enabled`       | No       | Yes        | `false`                                  | Handle leap seconds by slewing instead of stepping.          |
| `n_hcloud_ntp_leapsecmode`               | No       | Yes        | `slew`                                   | Method to handle leap seconds (`step` or `slew`).            |
| `n_hcloud_ntp_leapsectz`                 | No       | Yes        | `right/UTC`                              | Timezone for TAI-UTC offsets and leap seconds.               |
| `n_hcloud_ntp_logging_enabled`           | No       | Yes        | `false`                                  | Enable detailed logging.                                     |
| `n_hcloud_ntp_logging_dir`               | No       | Yes        | `/var/log/chrony`                        | Directory for Chrony logs.                                   |
| `n_hcloud_ntp_logging_selections`        | No       | Yes        | `measurements, statistics, tracking`     | Comma-separated log selections (trimmed spaces).             |


## Usage Ansible Playbook

This role is intended to be used through an Ansible Playbook like the following example:

```yaml
---
- hosts: all
  roles:
    - role: ansible_rhel_9_ntp_hcloud
      n_hcloud_ntp_servers: "{{ general_ntp_servers | split(',') }}"
      n_hcloud_ntp_allowed_subnets_enabled: "{{ general_allowed_subnets_enabled }}"
      n_hcloud_ntp_allowed_subnets: "{{ general_allowed_subnets | split(',') | map('trim') | list }}"
      n_hcloud_ntp_use_dhcp_enabled: "{{ general_ntp_use_dhcp_enabled }}"
      n_hcloud_ntp_dhcp_sourcedir: "{{ general_ntp_dhcp_sourcedir }}"
      n_hcloud_ntp_driftfile_enabled: "{{ general_ntp_driftfile_enabled }}"
      n_hcloud_ntp_driftfile: "{{ general_ntp_driftfile }}"
      n_hcloud_ntp_makestep_enabled: "{{ general_ntp_makestep_enabled }}"
      n_hcloud_ntp_makestep_threshold: "{{ general_ntp_makestep_threshold }}"
      n_hcloud_ntp_makestep_limit: "{{ general_ntp_makestep_limit }}"
      n_hcloud_ntp_enable_rtcsync: "{{ general_enable_sync }}"
      n_hcloud_ntp_hwtimestamp_enabled: "{{ general_ntp_hwtimestamp_enabled }}"
      n_hcloud_ntp_hwtimestamp_interfaces: "{{ general_ntp_hwtimestamp_interfaces }}"
      n_hcloud_ntp_minsources_enabled: "{{ general_ntp_minsources_enabled }}"
      n_hcloud_ntp_minsources: "{{ general_ntp_minsources }}"
      n_hcloud_ntp_local_stratum_enabled: "{{ general_ntp_local_stratum_enabled }}"
      n_hcloud_ntp_local_stratum: "{{ general_ntp_local_stratum }}"
      n_hcloud_ntp_authselectmode_enabled: "{{ general_ntp_authselectmode_enabled }}"
      n_hcloud_ntp_authselectmode: "{{ general_ntp_authselectmode }}"
      n_hcloud_ntp_enable_rtcsync: "{{ general_enable_sync }}"
      n_hcloud_ntp_hwtimestamp_enabled: "{{ general_ntp_hwtimestamp_enabled }}"
      n_hcloud_ntp_hwtimestamp_interfaces: "{{ general_ntp_hwtimestamp_interfaces }}"
      n_hcloud_ntp_keyfile: "{{ general_ntp_keyfile }}"
      n_hcloud_ntp_ntsdumpdir: "{{ general_ntp_ntsdumpdir }}"
      n_hcloud_ntp_leapsecmode_enabled: "{{ general_ntp_leapsecmode_enabled }}"
      n_hcloud_ntp_leapsecmode: "{{ general_ntp_leapsecmode }}"
      n_hcloud_ntp_leapsectz: "{{ general_ntp_leapsectz }}"
      n_hcloud_ntp_logging_enabled: "{{ general_ntp_logging_enabled }}"
      n_hcloud_ntp_logging_dir: "{{ general_ntp_logging_dir }}"
      n_hcloud_ntp_logging_selections: "{{ general_ntp_logging_selections }}"
```


## Foreman / Katello Integration

Included is the template file:

```shell
files/ansible_rhel_9_ntp_hcloud.erb
```

You can import this template into Foreman/Katello:

```
Hosts → Templates → Job Templates
```

## License

Licensed under the **MIT License** - see [LICENSE](LICENSE) file for details.

## Creator

Pierre by NAWKO

