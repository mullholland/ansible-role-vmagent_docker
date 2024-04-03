# [Ansible role vmagent_docker](#vmagent_docker)

Installs and configures vmagent container based on official vmagent docker container

|GitHub|Downloads|Version|
|------|---------|-------|
|[![github](https://github.com/mullholland/ansible-role-vmagent_docker/actions/workflows/molecule.yml/badge.svg)](https://github.com/mullholland/ansible-role-vmagent_docker/actions/workflows/molecule.yml)|[![downloads](https://img.shields.io/ansible/role/d/mullholland/vmagent_docker)](https://galaxy.ansible.com/mullholland/vmagent_docker)|[![Version](https://img.shields.io/github/release/mullholland/ansible-role-vmagent_docker.svg)](https://github.com/mullholland/ansible-role-vmagent_docker/releases/)|
## [Example Playbook](#example-playbook)

This example is taken from [`molecule/default/converge.yml`](https://github.com/mullholland/ansible-role-vmagent_docker/blob/master/molecule/default/converge.yml) and is tested on each push, pull request and release.

```yaml
---
- name: Converge
  hosts: all
  become: true
  gather_facts: true
  vars:
    adguardhome_docker_config:
      tls:
        options:
          modern:
            minVersion: "VersionTLS13"
            sniStrict: true

  roles:
    - role: "mullholland.vmagent_docker"
```

The machine needs to be prepared. In CI this is done using [`molecule/default/prepare.yml`](https://github.com/mullholland/ansible-role-vmagent_docker/blob/master/molecule/default/prepare.yml):

```yaml
---
- name: Prepare
  hosts: all
  become: true
  gather_facts: true
  vars:
    pip_packages:
      - "docker"

  roles:
    - role: mullholland.docker
    - role: mullholland.repository_epel
    - role: mullholland.pip
```



## [Role Variables](#role-variables)

The default values for the variables are set in [`defaults/main.yml`](https://github.com/mullholland/ansible-role-vmagent_docker/blob/master/defaults/main.yml):

```yaml
---
# General config
vmagent_docker_network_name: "web"
vmagent_docker_base_path: "/opt"
vmagent_docker_timezone: "Europe/Berlin"

# User/Group of the stack. Everything is mapped to this, instead of root.
vmagent_docker_user: "homelab"
vmagent_docker_uid: "900"
vmagent_docker_group: "homelab"
vmagent_docker_gid: "900"
vmagent_docker_user_system: true

# which container version to install
# https://hub.docker.com/r/victoriametrics/vmagent/tags
# can also be latest
vmagent_docker_version: "victoriametrics/vmagent:latest"

# https://docs.victoriametrics.com/vmagent/#advanced-usage
vmagent_docker_commands:
  - "--promscrape.config=/etc/prometheus/prometheus.yml"
  - "--remoteWrite.url=http://victoriametrics:8428/api/v1/write"
  - "--remoteWrite.tmpDataPath=/vmagentdata"

vmagent_docker_environment_variables: []
vmagent_docker_volumes:
  - "{{ vmagent_docker_base_path }}/vmagent/data:/vmagentdata"
  - "{{ vmagent_docker_base_path }}/vmagent/config/prometheus.yml:/etc/prometheus/prometheus.yml"

# which port to expose. can be empty
vmagent_docker_ports:
  - "8429:8429"

vmagent_docker_labels:
  - "traefik.enable=false"
# - "traefik.enable=true"
# - "traefik.http.routers.vmagent.entryPoints=https"
# - "traefik.http.routers.vmagent.rule=Host(`vmagent.example.com`)"
# - "traefik.http.routers.vmagent.tls.certResolver=letsEncrypt"
# - "traefik.http.routers.vmagent.tls=true"
#  - "traefik.http.routers.vmagent.service=vmagent"
#  - "traefik.http.routers.vmagent.loadbalancer.server.port=80"

vmagent_docker_config:
  global:
    scrape_interval: 10s
  scrape_configs:
    - job_name: 'vmagent'
      static_configs:
        - targets: ['vmagent:8429']
    - job_name: 'vmalert'
      static_configs:
        - targets: ['vmalert:8880']
    - job_name: 'victoriametrics'
      static_configs:
        - targets: ['victoriametrics:8428']```

## [Requirements](#requirements)

- pip packages listed in [requirements.txt](https://github.com/mullholland/ansible-role-vmagent_docker/blob/master/requirements.txt).

## [State of used roles](#state-of-used-roles)

The following roles are used to prepare a system. You can prepare your system in another way.

| Requirement | GitHub | GitLab |
|-------------|--------|--------|
|[mullholland.repository_epel](https://galaxy.ansible.com/mullholland/repository_epel)|[![Build Status GitHub](https://github.com/mullholland/ansible-role-repository_epel/workflows/Ansible%20Molecule/badge.svg)](https://github.com/mullholland/ansible-role-repository_epel/actions)|[![Build Status GitLab](https://gitlab.com/opensourceunicorn/ansible-role-repository_epel/badges/master/pipeline.svg)](https://gitlab.com/opensourceunicorn/ansible-role-repository_epel)|
|[mullholland.docker](https://galaxy.ansible.com/mullholland/docker)|[![Build Status GitHub](https://github.com/mullholland/ansible-role-docker/workflows/Ansible%20Molecule/badge.svg)](https://github.com/mullholland/ansible-role-docker/actions)|[![Build Status GitLab](https://gitlab.com/opensourceunicorn/ansible-role-docker/badges/master/pipeline.svg)](https://gitlab.com/opensourceunicorn/ansible-role-docker)|
|[mullholland.pip](https://galaxy.ansible.com/mullholland/pip)|[![Build Status GitHub](https://github.com/mullholland/ansible-role-pip/workflows/Ansible%20Molecule/badge.svg)](https://github.com/mullholland/ansible-role-pip/actions)|[![Build Status GitLab](https://gitlab.com/opensourceunicorn/ansible-role-pip/badges/master/pipeline.svg)](https://gitlab.com/opensourceunicorn/ansible-role-pip)|

## [Context](#context)

This role is a part of many compatible roles. Have a look at [the documentation of these roles](https://mullholland.net) for further information.

Here is an overview of related roles:
![dependencies](https://raw.githubusercontent.com/mullholland/ansible-role-vmagent_docker/png/requirements.png "Dependencies")

## [Compatibility](#compatibility)

This role has been tested on these [container images](https://hub.docker.com/u/mullholland):

|container|tags|
|---------|----|
|[EL](https://hub.docker.com/r/mullholland/enterpriselinux)|all|
|[Fedora](https://hub.docker.com/r/mullholland/fedora/)|38, 39|
|[Ubuntu](https://hub.docker.com/r/mullholland/ubuntu)|all|
|[Debian](https://hub.docker.com/r/mullholland/debian)|all|

The minimum version of Ansible required is 2.10, tests have been done to:

- The previous version.
- The current version.
- The development version.

If you find issues, please register them in [GitHub](https://github.com/mullholland/ansible-role-vmagent_docker/issues).

## [License](#license)

[MIT](https://github.com/mullholland/ansible-role-vmagent_docker/blob/master/LICENSE).

## [Author Information](#author-information)

[mullholland](https://mullholland.net)
