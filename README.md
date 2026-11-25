# Ansible Role: unpoller

![GitHub](https://img.shields.io/github/license/jomrr/ansible-role-unpoller)
![GitHub last commit](https://img.shields.io/github/last-commit/jomrr/ansible-role-unpoller)
![GitHub issues](https://img.shields.io/github/issues-raw/jomrr/ansible-role-unpoller)
[![dev](https://img.shields.io/github/actions/workflow/status/jomrr/ansible-role-unpoller/dev.yml?branch=dev&label=dev)](https://github.com/jomrr/ansible-role-unpoller/actions/workflows/dev.yml?query=branch%3Adev)
[![main](https://img.shields.io/github/actions/workflow/status/jomrr/ansible-role-unpoller/main.yml?branch=main&label=main)](https://github.com/jomrr/ansible-role-unpoller/actions/workflows/main.yml?query=branch%3Amain)

Install unpoller and export UniFi network metrics to Prometheus.

## Purpose

This role is intended to export UniFi controller, wireless, client and switch
metrics to Prometheus. Implementation is pending. The listed platforms and
dependencies are planned, and the CI badges have yet to record a role validation
run.

## Dependencies

```yaml
collections:
  - name: community.general
    version: '>=12.0.0'
  - name: ansible.posix
    version: '>=2.0.0'
```

## Role Variables

No role variable source was detected by the README generator.

## Supported Platforms

| OS Family | Distribution | Version | Container Image |
| --------- | ------------ | ------- | --------------- |
| RedHat | AlmaLinux | latest | [jomrr/molecule-almalinux:latest](https://hub.docker.com/r/jomrr/molecule-almalinux) |
| Debian | Debian | latest | [jomrr/molecule-debian:latest](https://hub.docker.com/r/jomrr/molecule-debian) |
| RedHat | Fedora | latest | [jomrr/molecule-fedora:latest](https://hub.docker.com/r/jomrr/molecule-fedora) |
| Suse | OpenSuse Leap | latest | [jomrr/molecule-opensuse-leap:latest](https://hub.docker.com/r/jomrr/molecule-opensuse-leap) |
| Suse | OpenSuse Tumbleweed | latest | [jomrr/molecule-opensuse-tumbleweed:latest](https://hub.docker.com/r/jomrr/molecule-opensuse-tumbleweed) |
| Debian | Ubuntu | latest | [jomrr/molecule-ubuntu:latest](https://hub.docker.com/r/jomrr/molecule-ubuntu) |

## Author

[Jonas Mauer](https://github.com/jomrr)

## License

This project is licensed under the MIT License.
See [LICENSE](LICENSE) for the full license text.

Copyright (c) 2026 Jonas Mauer.
