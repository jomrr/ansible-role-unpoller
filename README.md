# Ansible Role: unpoller

![GitHub](https://img.shields.io/github/license/jomrr/ansible-role-unpoller)
![GitHub last commit](https://img.shields.io/github/last-commit/jomrr/ansible-role-unpoller)
![GitHub issues](https://img.shields.io/github/issues-raw/jomrr/ansible-role-unpoller)
[![dev](https://img.shields.io/github/actions/workflow/status/jomrr/ansible-role-unpoller/dev.yml?branch=dev&label=dev)](https://github.com/jomrr/ansible-role-unpoller/actions/workflows/dev.yml?query=branch%3Adev)
[![main](https://img.shields.io/github/actions/workflow/status/jomrr/ansible-role-unpoller/main.yml?branch=main&label=main)](https://github.com/jomrr/ansible-role-unpoller/actions/workflows/main.yml?query=branch%3Amain)

Install unpoller and export UniFi network metrics to Prometheus.

## Purpose

Export UniFi client, WLAN, switch port, PoE and device metrics at `/metrics`.
The exporter runs under its own locked system account. It polls the configured
sites and exposes metrics on loopback by default. InfluxDB, Loki and Datadog are
disabled.

## Requirements

- A local UniFi Network API key or read-only local account with access to the
  selected sites.
- When using a private controller CA, install it in the managed host's system
  trust store.
- Managed hosts download the release key from `https://golift.io/gpgkey` and the
  releases from GitHub.

## Dependencies

```yaml
collections:
  - name: community.general
    version: '>=12.0.0'
  - name: ansible.posix
    version: '>=2.0.0'
  - name: containers.podman
    version: '>=1.20.0'
```

## Role Variables

### `unpoller_controller_url`

Type: `str`. Required: `true`.

HTTPS origin of the local UniFi controller, including a port if required.

### `unpoller_verify_ssl`

Type: `bool`. Required: `false`.

Verify the controller certificate with the host's system trust store.

Default:

```yaml
unpoller_verify_ssl: true
```

### `unpoller_sites`

Type: `list`. Required: `false`.

UniFi site names to poll. Use all for every accessible site.

Default:

```yaml
unpoller_sites:
  - all
```

### `unpoller_save_dpi`

Type: `bool`. Required: `false`.

Collect deep packet inspection statistics.

Default:

```yaml
unpoller_save_dpi: false
```

### `unpoller_auth_method`

Type: `str`. Required: `false`.

Use a local controller API key or authenticate with a username and password.

Default:

```yaml
unpoller_auth_method: api_key
```

### `unpoller_credential`

Type: `str`. Required: `true`.

API key or password for the selected authentication method.

### `unpoller_username`

Type: `str`. Required: `false`.

Read-only controller username for password authentication.

Default:

```yaml
unpoller_username: unpoller
```

### `unpoller_listen_address`

Type: `str`. Required: `false`.

Prometheus listener in host:port format. Enclose IPv6 addresses in brackets.

Default:

```yaml
unpoller_listen_address: 127.0.0.1:9130
```

### `unpoller_backup`

Type: `bool`. Required: `false`.

Back up non-secret managed files when they change.

Default:

```yaml
unpoller_backup: true
```

## Check Mode

After installation, check mode resolves the latest release and reports a pending
update without switching the binary.

## Service Behavior

A full run installs the latest stable release, configures the service and
enables a persistent daily update timer. Ansible and the timer share the same
installer and lock. Verified versions are retained in
`/usr/local/libexec/unpoller` and selected through `/usr/local/bin/unpoller`. A
scheduled update restarts a running exporter when its executable changes. An
unchanged release leaves the process running.

## Security Notes

- The installer verifies the release archive against its SHA-256 manifest. It
  verifies detached OpenPGP signatures for the archive and manifest with the Go
  Lift public key (`B93DD66EF98E54E2EAE025BA0166AD34ABC5A57C`). Ansible checks
  the key download against a pinned SHA-256 digest before the installer or daily
  timer uses it.
- Supply the credential from Ansible Vault or a secret manager. Only
  `/etc/unpoller/credential` contains its value (root:unpoller, mode 0640).
  Rotation replaces the file with logging, diffs and backups disabled.
- The exporter runs with an empty capability set, a read-only filesystem and
  restricted address families. Set the listener to a private address reachable
  by Prometheus when scraping remotely.

## Supported Platforms

| OS Family | Distribution | Version | Container Image |
| --------- | ------------ | ------- | --------------- |
| RedHat | AlmaLinux | latest | [jomrr/molecule-almalinux:latest](https://hub.docker.com/r/jomrr/molecule-almalinux) |
| Debian | Debian | latest | [jomrr/molecule-debian:latest](https://hub.docker.com/r/jomrr/molecule-debian) |
| RedHat | Fedora | latest | [jomrr/molecule-fedora:latest](https://hub.docker.com/r/jomrr/molecule-fedora) |
| Suse | OpenSuse Leap | latest | [jomrr/molecule-opensuse-leap:latest](https://hub.docker.com/r/jomrr/molecule-opensuse-leap) |
| Suse | OpenSuse Tumbleweed | latest | [jomrr/molecule-opensuse-tumbleweed:latest](https://hub.docker.com/r/jomrr/molecule-opensuse-tumbleweed) |
| Debian | Ubuntu | latest | [jomrr/molecule-ubuntu:latest](https://hub.docker.com/r/jomrr/molecule-ubuntu) |

## Example Playbook

### Export metrics with a local controller API key

```yaml
- name: Export UniFi metrics
  hosts: monitoring
  roles:
    - role: jomrr.unpoller
      unpoller_controller_url: https://unifi.example.org
      unpoller_credential: "{{ vault_unpoller_api_key }}"
      unpoller_sites: [default]
```

### Use a read-only controller account

```yaml
unpoller_controller_url: https://unifi.example.org:8443
unpoller_auth_method: password
unpoller_username: metrics
unpoller_credential: "{{ vault_unpoller_password }}"
unpoller_save_dpi: true
```

### Install the account and executable only

Controller settings are optional for this entry point.

```yaml
- name: Install unpoller
  ansible.builtin.include_role:
    name: jomrr.unpoller
    tasks_from: install
```

## References

- <https://github.com/unpoller/unpoller/releases>
- <https://github.com/golift/turbovanityurls/blob/master/static/gpgkey>
- <https://unpoller.com/docs/install/configuration/>

## Author

[Jonas Mauer](https://github.com/jomrr)

## License

This project is licensed under the MIT License.
See [LICENSE](LICENSE) for the full license text.

Copyright (c) 2026 Jonas Mauer.
