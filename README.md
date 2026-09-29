Ansible role for GitLab Runner
=========

Install and configure GitLab Runner, as well as register new runners. The role accepts the following tags in order to run specific tasks: 
- `install`: to install only the GitLab Runner package;
- `config`: to update the configuration file (`config.toml`) and to upload new configuration template files;
- `register`: to register a new runner.


Requirements
------------

None.


Role Variables
--------------

A description of the settable variables for this role (see `defaults/main.yml` and `vars/main.yml`).

### Install latest version
By default, this role installs the latest GitLab Runner release avaiable in official repository.
```yaml
gitlab_runner_package_latest: true
```

### Install specific version
To install a specific version (e.g., `19.1.0`), setup the variables as follows:
```yaml
gitlab_runner_package_latest: false
gitlab_runner_package_version: 19.1.0
```

### Setup global section
Configure global section using `yaml` dictionary, its key-value pairs will be converted into `toml` syntax.

An example:
```yaml
gitlab_runner_config_global:
  concurrent: 10
  log_level: info
  log_format: json
```

By default, the dictionary is empty, so the global settings are not overridden.
```yaml
gitlab_runner_config_global: {}
```

Consult the official documentation for more info about [global section](https://docs.gitlab.com/runner/configuration/advanced-configuration/#the-global-section).

### User-defined configuration templates
Upload user-defined configuration templates that can be reused when registering runners.
The destination directory is specified by the variable `gitlab_runner_config_templates_dir`, default path is `/etc/gitlab-runner/templates` (see `vars/main.yml`).
Pre-defined template files are located into `files` directory of this role.
Custom templates can be located into `files` or in a sub-directory of `inventory_dir`.
See example the below.

```yaml
gitlab_runner_config_templates:
  - docker-unprivileged.toml
  - "{{ inventory_dir }}/group_vars/gitlab_runners/docker-privileged.toml"
```

By default, no configuration template file is uploaded to the target host.
```yaml
gitlab_runner_config_templates: []
```

Consult the official documentation for more info about [configuration template](https://docs.gitlab.com/runner/register/#register-with-a-configuration-template).

### Register runners

Specify the list of runners to be registered using non-interactive mode.
```yaml
gitlab_runner_register_runners: []
```

This method relies on a set of mandatory fields to provide a bare minimum configuration.
They are `name`, `url`, `token`, and `executor`.

There are two ways to apply advanced configuration of an executor: 
1. use the optional field `extra_args` to pass a list of arguments and parameters supported by `gitlab-runner register` command;
2. use the optional field `template` to specify the name configuration template file (see `gitlab_runner_config_templates` variable).

An example:
```yaml
gitlab_runner_register_runners:
  # configure a Docker executor
  - name: docker
    url: https://gitlab.example.org
    token: GITLAB_AUTH_TOKEN
    executor: docker
    template: docker-privileged.toml
    extra_args:
      - --docker-image alpine:latest
  # configure a shell executor
  - name: shell:
    url: https://gitlab.example.org
    token: GITLAB_AUTH_TOKEN
    executor: shell
    extra_args:
      - --shell bash
```

Consult the official documentation for more info about [register a runner](https://docs.gitlab.com/runner/register) and [executors](https://docs.gitlab.com/runner/executors/).


Dependencies
------------

None.


Example Playbook
----------------

Install and configure GitLab Runner using this playbook.

```yaml
# Usage:
#   ansible-playbook -l <host/group> gitlab_runner.yml [--tags [install,config,register]]
---
- name: Install and configure GitLab Runner
  hosts: all
  become: true

  roles:
    - mcaliandro.gitlab_runner
```


Compatibility
-------------

This role has been tested on these Linux distributions:
- Ubuntu Server LTS 22.04
- Ubuntu Server LTS 24.04
- Ubuntu Server LTS 26.04


License
-------

Apache License


Author Information
------------------

Created in 2026 by Matteo Caliandro, <mcaliandro.dev@gmail.com>