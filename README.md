kube-resource
=============

Manages Kubernetes resources based on a set of manifest templates

The kube-resource role implements the principles of immutable configmaps
and secrets as
[presented at AnsibleFest 2018](https://www.ansible.com/managing-kubernetes-is-easy-with-ansible)

Requirements
------------

* openshift 0.10.1
* kubernetes-validate (only needed if `kube_resource_validation_options` is set
* jmespath (only needed if `kube_resource_set_owner` is set)

Role Variables
--------------

* `kube_resource_namespace` - namespace in which to create resources
* `kube_resource_name` - name of resource being managed
* `kube_resource_configmaps` - a dict of ConfigMaps, mapping a reference name to a ConfigMap definition
* `kube_resource_manifest_files` - a list of resource definition template file names
* `kube_resource_secrets` - a dict of Secrets, mapping a reference name to a Secret definition
* `kube_resource_secrets_files` - a list of Secret definition template file names
* `kube_resource_apply` - whether to use apply. Defaults to true. Only takes effect for v2.9 and above
* `kube_resource_UNSAFE_show_logs` - whether to show the logs when working with secrets. Defaults to `no`.
  For use when troubleshooting problems with secret definitions.
* `kube_resource_set_owner` - set owners of configmaps and secrets to the replicaset belonging to the
  resource. This is actually a dangerous feature, as too many restarts of a deployment can cause
  configmaps and secrets to disappear without warning! So this feature will be removed.

* `kube_resource_prefix_label` - name of label that allows all configmaps of a particular type (e.g.
  the environment variable configmap) for a particular resource to be found. Defaults to
  `kube_resource_prefix`

* `kube_resource_validate_options` - how to validate Kubernetes resources. Defaults to an empty dict,
  disabling validation. A sensible setting is:
  ```
  kube_resource_validate_options:
    strict: yes
    fail_on_error: yes
  ```
* `kube_resource_wait` - whether to wait for resources to reach their desired state (default `no`)
* `kube_resource_wait_timeout` - how long to wait in seconds for resources if `kube_resource_wait` is on
  (default 120)
* `kube_resource_deployments_api` - only needed if `kube_resource_set_owner` is set to `yes`. Defaults
  to `apps/v1` - might need to be `apps/v1beta2` or `extensions/v1beta1` for older Kubernetes versions


Dependencies
------------

None

Example Playbook
----------------

```
kube_resource_configmaps:
  my-resource-env: "{{ lookup('template', template_dir + '/my-resource-env.j2') }}"
```

```
kube_resource_manifest_files: "{{ lookup('fileglob', template_dir + '/*manifest.yml') }}"
```

```
- hosts: "{{ application }}-{{ env }}-runner"
  roles:
    - kube-resource
```

The molecule/default directory now contains a working playbook and inventory suitable for
running on Kubernetes for Docker

Testing
-------

The role comes with a [molecule](https://molecule.readthedocs.io/) test suite that should
work against any reasonable Kubernetes implementation (it has been tested against Kubernetes
for Docker)

```
K8S_AUTH_CONTEXT=docker-for-desktop molecule test
```

Versions
--------

This module relies on functionality of Ansible that won't be released until Ansible 2.8 in mid 2019.
The functionality has been included in this role, but will need to be removed once 2.8 is released.
As such, the role will be available by Ansible version.

Versions will be tagged `v2.7-x` where `x` is the release number of the role for version 2.7, with
a parallel `v2.8-y` version for use with Ansible 2.8 (or ansible devel branch before then)

License
-------

This role contains modules and filters from the Ansible project and thus inherit Ansible's license

GPL 3

Author Information
------------------

Will Thames (@willthames)
