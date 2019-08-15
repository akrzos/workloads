# Upgrade Workload

The upgrade workload playbook is `workloads/upgrade.yml` and will upgrade a cluster with or without tooling.

Note that upgrades can reboot nodes and thus any node that is rebooted hosting a pbench agent pod that is actively collecting data will be interrupted. As with cloud native workloads, pods are supposed to be ephemeral anyway.

Running from CLI:

```sh
$ cp workloads/inventory.example inventory
$ # Add orchestration host to inventory
$ # Edit vars in workloads/vars/scale.yml or define Environment vars (See below)
$ time ansible-playbook -vv -i inventory workloads/upgrade.yml
```

## Environment variables

### PUBLIC_KEY
Default: `~/.ssh/id_rsa.pub`  
Public ssh key file for Ansible.

### PRIVATE_KEY
Default: `~/.ssh/id_rsa`  
Private ssh key file for Ansible.

### ORCHESTRATION_USER
Default: `root`  
User for Ansible to log in as. Must authenticate with PUBLIC_KEY/PRIVATE_KEY.

### WORKLOAD_IMAGE
Default: `quay.io/openshift-scale/scale-ci-workload`  
Container image that runs the workload script.

### WORKLOAD_JOB_NODE_SELECTOR
Default: `true`  
Enables/disables the node selector that places the workload job on the `workload` node.

### WORKLOAD_JOB_TAINT
Default: `true`  
Enables/disables the toleration on the workload job to permit the `workload` taint.

### WORKLOAD_JOB_PRIVILEGED
Default: `false`  
Enables/disables running the workload Pod as privileged.

### KUBECONFIG_FILE
Default: `~/.kube/config`  
Location of kubeconfig on orchestration host.

### PBENCH_INSTRUMENTATION
Default: `false`  
Enables/disables running the workload wrapped by pbench-user-benchmark. When enabled, pbench agents can then be enabled (`ENABLE_PBENCH_AGENTS`) for further instrumentation data and pbench-copy-results can be enabled (`ENABLE_PBENCH_COPY`) to export captured data for further analysis.

### ENABLE_PBENCH_AGENTS
Default: `false`  
Enables/disables the collection of pbench data on the pbench agent Pods. These Pods are deployed by the tooling playbook.

### ENABLE_PBENCH_COPY
Default: `false`  
Enables/disables the copying of pbench data to a remote results server for further analysis.

### PBENCH_SSH_PRIVATE_KEY_FILE
Default: `~/.ssh/id_rsa`  
Location of ssh private key to authenticate to the pbench results server.

### PBENCH_SSH_PUBLIC_KEY_FILE
Default: `~/.ssh/id_rsa.pub`  
Location of the ssh public key to authenticate to the pbench results server.

### PBENCH_SERVER
Default: There is no public default.  
DNS address of the pbench results server.

### SCALE_CI_RESULTS_TOKEN
Default: There is no public default.  
Future use for pbench and prometheus scraper to place results into git repo that holds results data.

### JOB_COMPLETION_POLL_ATTEMPTS
Default: `360`  
Number of retries for Ansible to poll if the workload job has completed. Poll attempts delay 10s between polls with some additional time taken for each polling action depending on the orchestration host setup.

### UPGRADE_TEST_PREFIX
Default: `upgrade`  
Test to prefix the pbench results.

### UPGRADE_NEW_VERSION_URL
Default: No default.  
The url portion of a new version to upgrade to. An example would be `quay.io/openshift-release-dev/ocp-release` or `registry.svc.ci.openshift.org/ocp/release`.

### UPGRADE_NEW_VERSION
Default: No default.  
The new version to upgrade to. Check [https://openshift-release.svc.ci.openshift.org/](https://openshift-release.svc.ci.openshift.org/) for versions and upgrade paths based on the installed cluster.

### FORCE_UPGRADE
Default: `false`  
Determines the `--force` flag value for the `oc adm upgrade` command to initiate an upgrade.

### UPGRADE_POLL_ATTEMPTS
Default: `1800`  
Number of times to poll to determine if the cluster has been upgraded. Each poll attempted corresponds to approximately a 2s wait plus poll time.

### EXPECTED_UPGRADE_DURATION
Default: `1800`  
Pass/fail criteria. Value to determine if upgrade workload executed in duration expected.

## Smoke test variables

```
UPGRADE_TEST_PREFIX=upgrade_smoke
UPGRADE_NEW_VERSION_URL=registry.svc.ci.openshift.org/ocp/release
UPGRADE_NEW_VERSION=4.2.0-0.nightly-2019-08-13-183722
FORCE_UPGRADE=true
UPGRADE_POLL_ATTEMPTS=7200
```
