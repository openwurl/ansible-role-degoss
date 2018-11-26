# ansible-role-degoss [![Build Status][img-build-status]][build-status]

Installs, runs, and removes [Goss][goss] and one or more Goss spec file(s) on a system. If you'd like to lint a system
without leaving many "traces,"<sup>1</sup> `degoss` should do what you want.

Props to upstream [ansible-goss][ansible-goss] for inspiration for the module and first steps.

Available on Ansible Galaxy at [`naftulikay.degoss`][galaxy].

## Requirements

This module downloads a 64-bit Linux Goss binary. If support for multiple operating systems and architectures is
desired, pull requests are welcome!

## Role Variables

Here is a sampling of valuable role variables that may be of interest to the user.

##### Degoss Variables

Variables for configuring the `degoss` role.

<dl>
  <dt><code>degoss_debug</code></dt>
  <dd>Booleanish value that decides whether to output debug information or not. Defaults to <code>false</code>.</dd>
  <dt><code>degoss_clean</code></dt>
  <dd>Booleanish value that decides whether to clean all Goss binaries and tests from the machine after the run. Defaults to <code>true</code>, which means that by default, `degoss` will remove all traces from a machine after finished. Set to <code>false</code> to enable caching the Goss binary on the machine.</dd>
</dl>

##### Goss Variables

Variables affecting the runtime of Goss.

<dl>
  <dt><code>goss_file</code></dt>
  <dd>A Goss test file to run on the target machine.</dd>
  <dt><code>goss_source_dir</code></dt>
  <dd>The directory, local to the playbook, to look for the Goss test file. Only affects goss_file</dd>
  <dt><code>goss_addtl_files</code></dt>
  <dd>Additional Goss test files to copy to the machine.</dd>
  <dt><code>goss_addtl_dirs</code></dt>
  <dd>Additional directories containing Goss test files to copy to the machine, recursively.</dd>
  <dt><code>goss_version</code></dt>
  <dd>The version string of Goss to install, or <code>latest</code>. Example: <code>0.2.5</code>.</dd>
  <dt><code>goss_env_vars</code></dt>
  <dd>A dictionary of environment variables to set during Goss execution.</dd>
</dl>

## Dependencies

None.

## Example Playbooks

### Single Test File

Fetches the latest version of Goss and executes the `goss.yml` file locally.

```yaml
---
- name: test
  hosts: all
  roles:
    - { role: degoss, goss_file: goss.yml }
```

Uses a custom directory

```yaml
---
- name: test
  hosts: all
  roles:
    - { role: degoss, goss_source_dir: tests/, goss_file: goss.yml }
```

### Pinned Version

Downloads a specific version of Goss and executes the `goss.yml` file locally.

```yaml
---
- name: test
  hosts: all
  roles:
    - { role: degoss, goss_version: 0.2.5, goss_file: goss.yml }
```

### Multiple Files and Directories

Executes `goss.yaml`, uploading the `tests` directory and some other files:

```yaml
---
- name: test
  hosts: all
  roles:
    - role: degoss
      goss_file: goss.yaml
      goss_addtl_files: [include.yml]
      goss_addtl_dirs: [tests/]
```

### Environment Variables

Executes `goss.yml`, passing in the given environment variables:

```yaml
---
- name: test
  hosts: all
  roles:
    - role: degoss
      goss_file: goss.yml
      goss_env_vars:
        distro: "{{ ansible_distribution | lower }}"
```

In this case, `distro` will be available to Goss at runtime at `{{.Env.distro}}`.

## License

MIT

## Development
In order to use the Makefile to test you will need two variables, `IMAGE_NAME` and `ROLE_NAME`.

* `ROLE_NAME`
  * This role, `degoss`
* `IMAGE_NAME`
  * one of:
    * `IMAGE_NAME: "naftulikay/centos-vm:7"`
    * `IMAGE_NAME: "naftulikay/xenial-vm:latest"`
    * `IMAGE_NAME: "naftulikay/trusty-vm:latest"`
* Example
  * `IMAGE_NAME=naftulikay/xenial-vm:latest ROLE_NAME=degoss make test-clean`

---

Footnotes:
 1. If you're really so dense as to assume that you can use this to maliciously execute code on arbitrary systems,
    there are a number of problems with this assumption:
    1. Ansible _itself_ is a remote-code-execution framework. That's _what it is_, that's _what it does_.
    2. Ansible is poor at doing things maliciously because by default, activity is logged via `sudo` and `syslog`. If the
       machine is forwarding syslog elsewhere, you're caught.
    3. Ansible, having been designed as an automation and configuration management system _for_ systems administrators,
       is likely to try to leave auditable traces because this is a common use case for companies attempting to answer
       security audit questions.

 [ansible-goss]: https://github.com/indusbox/goss-ansible
 [build-status]: https://travis-ci.org/naftulikay/ansible-role-degoss
 [img-build-status]: https://travis-ci.org/naftulikay/ansible-role-degoss.svg?branch=master
 [galaxy]: https://galaxy.ansible.com/naftulikay/degoss/
 [goss]: https://goss.rocks
