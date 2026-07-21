[![Molecule](https://github.com/iamenr0s/ansible-role-containerd/actions/workflows/molecule.yml/badge.svg)](https://github.com/iamenr0s/ansible-role-containerd/actions/workflows/molecule.yml) ![Ansible Role](https://img.shields.io/ansible/role/d/iamenr0s/ansible_role_containerd) [![CodeFactor](https://www.codefactor.io/repository/github/iamenr0s/ansible-role-containerd/badge)](https://www.codefactor.io/repository/github/iamenr0s/ansible-role-containerd)

Ansible Role: Containerd
========================

This role installs and configures [containerd](https://containerd.io/) from the Docker CE repository on RHEL-family (AlmaLinux/RockyLinux), Fedora, and Debian-family (Debian/Ubuntu) servers. It can write containerd's built-in default configuration and switch the cgroup driver to systemd, which is the recommended setup for Kubernetes nodes.

Inspired by @geerlingguy's [ansible-role-containerd](https://github.com/geerlingguy/ansible-role-containerd/).

Features
--------
- Adds the Docker CE repository and GPG key (RedHat family and Debian family).
- Installs `containerd.io`, plus `container-selinux` on RedHat-family hosts.
- Manages the containerd service state and boot enablement.
- Optionally writes containerd's default configuration to `/etc/containerd/config.toml`.
- Optionally sets systemd as the cgroup driver (`SystemdCgroup = true`).
- Restarts containerd only when the configuration actually changes.

Requirements
------------
- Python 3 available on the managed hosts.
- The `community.general` collection (used for repo file management):
  - `ansible-galaxy collection install community.general`
- On Debian-family hosts, `python3-apt` is installed automatically by the role (required by
  the `apt_repository` module) — no manual prerequisite needed.
- Run with privilege escalation on real hosts: `become: true` is recommended.

Supported Platforms
-------------------
- AlmaLinux 8, 9, 10
- RockyLinux 8, 9, 10
- Fedora 42, 43, 44
- Debian 12, 13
- Ubuntu 22.04, 24.04

Role Variables
--------------
Defined in `defaults/main.yml`:

- `containerd_package` (str): Package name to install (default: `containerd.io`).
- `containerd_package_state` (str): Package state, `present` or `latest` (default: `present`).
- `containerd_service_state` (str): Service state, `started` or `stopped` (default: `started`).
- `containerd_service_enabled` (bool): Enable the service at boot (default: `true`).
- `containerd_config_default_write` (bool): Write containerd's default configuration to `/etc/containerd/config.toml` (default: `true`).
- `containerd_config_cgroup_driver_systemd` (bool): Set systemd as the cgroup driver in config.toml; only used with `containerd_config_default_write: true` (default: `false`).
- `docker_yum_repo_url` (str): URL of the Docker CE yum/dnf repository file (default: derived from the distribution — `fedora` or `centos`).
- `docker_yum_repo_enable_nightly` (str): Enable the docker-ce-nightly repository, `'0'` or `'1'` (default: `'0'`).
- `docker_yum_gpg_key` (str): URL of the Docker GPG key (default: `https://download.docker.com/linux/centos/gpg`).
- `docker_apt_gpg_key` (str): URL of the Docker GPG key for apt (default: derived from the distribution — `debian` or `ubuntu`).
- `docker_apt_repo_url` (str): Base URL of the Docker CE apt repository (default: derived from the distribution — `debian` or `ubuntu`).
- `docker_apt_repo_enable_nightly` (str): Enable the docker-ce-nightly apt component, `'0'` or `'1'` (default: `'0'`).

Tags
----
All tasks are tagged `containerd`, allowing selective runs:
- `ansible-playbook ... --tags containerd`
- `ansible-playbook ... --skip-tags containerd`

Dependencies
------------
- Collections: `community.general`.
- Role dependencies: none.

Example Playbook
----------------
Basic install with defaults:

```yaml
- hosts: all
  become: true
  roles:
    - role: iamenr0s.ansible_role_containerd
```

Kubernetes node setup (systemd cgroup driver):

```yaml
- hosts: all
  become: true
  vars:
    containerd_config_default_write: true
    containerd_config_cgroup_driver_systemd: true
  roles:
    - role: iamenr0s.ansible_role_containerd
```

Contributing & Security
-----------------------
- Contributions are welcome — see [CONTRIBUTING.md](CONTRIBUTING.md).
- Report vulnerabilities privately per [SECURITY.md](SECURITY.md); do not open public issues for them.

CI & Release (maintainers)
--------------------------
A single workflow (`.github/workflows/molecule.yml`) runs lint and the full Molecule distro matrix on pushes to `main`, PRs, and `v*` tags. On `v*` tags, a `release` job publishes to Ansible Galaxy after all tests pass.

The Galaxy API key lives in the `galaxy` GitHub environment, which only `v*` tags may target. One-time setup:

```bash
# Galaxy publishing key (environment-scoped, get it from galaxy.ansible.com/ui/token)
gh secret set GALAXY_API_KEY --env galaxy --repo iamenr0s/ansible-role-containerd

# Code scanning notifications (Slack webhook URL; for Discord append /slack to the webhook URL)
gh secret set SECURITY_ALERT_WEBHOOK --env galaxy --repo iamenr0s/ansible-role-containerd
```

`.github/workflows/code-scanning-notify.yml` polls the code-scanning API every 6 hours and posts new or updated open alerts to that webhook (GitHub Actions cannot trigger on `code_scanning_alert` directly).

To release: tag a commit `vX.Y.Z` and push the tag — CI gates the Galaxy publish.

License
-------
This project is licensed under the MIT License.

Author Information
------------------
Author: iamenr0s
Galaxy: `iamenr0s.ansible_role_containerd`
