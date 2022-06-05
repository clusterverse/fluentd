# fluentd-ansible

This Ansible role is used to provision a fluentd cluster.  It can do so on AWS, where it provisions an NLB, or on-prem (KVM/Libvirt), where it installs and configures keeaplived/IPVS. 

This project can operate in isolation, but is designed to operate using [clusterverse](https://github.com/dseeley/clusterverse) to manage the base infrastructure.  Please see the [README.md](https://github.com/dseeley/clusterverse/blob/master/README.md) there for instructions on deployment. 

Please see the [/EXAMPLE](https://github.com/dseeley/fluentd-ansible/tree/master/EXAMPLE) directory in this repository for a basic configuration that can be copied to a new location

## EXAMPLE:

### Configuration
Cluster configuration is stored in `cluster_defs/**/cluster_vars[*].yml` files.
Application configuration is stored in `cluster_defs/**/app_vars[*].yml` files.

### Playbook execution
`cluster.yml` is included that is compatible with [clusterverse](https://github.com/dseeley/clusterverse).

`redeploy.yml` is included that functions only in conjunction with [clusterverse](https://github.com/dseeley/clusterverse).


### Invocation
To create a cluster:
```
ansible-playbook cluster.yml -e cloud_type=aws -e region=eu-west-1 -e buildenv=dev
```

To redeploy a cluster
```
ansible-playbook redeploy.yml -e cloud_type=aws -e region=eu-west-1 -e buildenv=dev -e canary=none
```

To delete a cluster
```
ansible-playbook cluster.yml -e cloud_type=aws -e region=eu-west-1 -e buildenv=dev --tags=clusterverse_clean -e clean=_all_
```