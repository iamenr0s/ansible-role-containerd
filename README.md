# Ansible Role: Containerd

This Ansible Role automates the installation of Containerd on Linux systems.

## Requirements

None.

## Role Variables

Available variables are listed below, along with default values (see `defaults/main.yml`):

    containerd_package: containerd.io
    containerd_package_state: present

Package name and state controls.

    containerd_service_state: started
    containerd_service_enabled: true

Service controls. You can install containerd but not have it running or enabled on boot by changing these defaults.

    containerd_config_default_write: true

Write containerd defaults to the containerd config.toml file.

    containerd_config_cgroup_driver_systemd: false

Set systemd as cgroup driver in config.toml. Only valid with `containerd_config_default_write: true`

    docker_yum_repo_url: https://download.docker.com/linux/{{ (ansible_distribution == "Fedora") | ternary("fedora","centos") }}/docker-ce.repo
    docker_yum_repo_enable_nightly: '0'
    docker_yum_gpg_key: https://download.docker.com/linux/centos/gpg

Yum/DNF installation parameters, useful if you want to switch from the stable repository.

## Dependencies

None.

## Example Playbook

```yaml
- hosts: all
  roles:
    - iamenr0s.containerd
```

## License

This project is licensed under the MIT License.

## Author Information
