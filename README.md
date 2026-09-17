# fluentd

This Ansible role is used to provision a fluentd cluster.  It can do so on AWS, where it provisions an NLB, or on-prem (KVM/Libvirt), where it installs and configures keepalived/IPVS.

This project is designed to operate using [**clusterverse**](https://github.com/clusterverse/clusterverse) to manage the base infrastructure.  Please see the [README.md](https://github.com/clusterverse/clusterverse/blob/master/README.md) there for detailed instructions on its usage.

## Requirements
+ ansible-core >= 2.17.4 (pypi >= 10.4.0)
+ clusterverse.clusterverse >= 6.1.2
+ See [docs/EXAMPLE/Dockerfile_nonroot](https://github.com/clusterverse/clusterverse/blob/master/docs/EXAMPLE/Dockerfile_nonroot) for a full list of dependencies.

## Example
Please see the [EXAMPLE](https://github.com/clusterverse/fluentd/tree/master/EXAMPLE) directory in this repository for some basic configuration.  This can be copied in the root directory, and used as a starting point for your own configuration.

### Configuration
Clusters are defined as code within Ansible yaml files that are imported at runtime.  Because clusters are built from scratch on the localhost, the automatic Ansible `group_vars` inclusion cannot work with anything except the special `all.yml` group (actual `groups` need to be in the inventory, which cannot exist until the cluster is built).  The `group_vars/all.yml` file is instead used to bootstrap _merge_vars_, and the definitions are hierarchically defined in [cluster_defs](https://github.com/clusterverse/fluentd/tree/master/EXAMPLE/cluster_defs).  Please see the full documentation in the main [clusterverse/README.md](https://github.com/clusterverse/clusterverse/blob/master/README.md#cluster-definition-variables)
+ Cluster configuration is stored in `cluster_defs/**/cluster_vars[*].yml` files.
+ Application configuration is stored in `cluster_defs/**/app_vars.yml` files.
+ Regions are defined per hosttype in `cluster_vars[buildenv].hosttype_vars.<hosttype>.region_vars` (clusterverse v6+ is multi-region).


### Invocation

_**For full clusterverse invocation examples and command-line arguments, please see the [clusterverse README.md](https://github.com/clusterverse/clusterverse/blob/master/README.md#usage)**_

#### Deploy (also performs _up-scaling_ and _repairs_)
+ [deploy.yml](https://github.com/clusterverse/fluentd/tree/master/EXAMPLE/deploy.yml) - Deploys a cluster from scratch, or repairs a cluster, or scales it up (note: not _down_).
    + e.g. `ansible-playbook -e buildenv=dev -e cloud_type=aws deploy.yml`
+ [redeploy.yml](https://github.com/clusterverse/fluentd/blob/master/EXAMPLE/redeploy.yml) - Redeploys the cluster, replacing all the nodes entirely.
    + e.g. `ansible-playbook -e buildenv=dev -e cloud_type=aws -e canary=none redeploy.yml`
+ [destroy.yml](https://github.com/clusterverse/fluentd/tree/master/EXAMPLE/destroy.yml) - Destroys the cluster.
    + e.g. `ansible-playbook -e buildenv=dev -e cloud_type=aws destroy.yml`


### Invocation via Docker
Can also be run within the included Docker container (e.g. for CI/CD)
+ `docker build -t ansibuild -f Dockerfile_nonroot .`
+ `docker run --rm --name ansibuilder_fluentd -e VAULT_PASSWORD_BUILDENV=$VAULT_PASSWORD ansibuild ansible-playbook -e cloud_type=aws -e buildenv=dev deploy.yml -vvv`
