[![Build Status](https://travis-ci.org/portworx/ansible-portworx-defaults.svg?branch=master)](https://travis-ci.org/portworx/ansible-portworx-defaults)
# Install Portworx through Ansible

Portworx is typically deployed through/with the container orchestrator.
For Kubernetes, Portworx is deployed as a `daemonset` through the 
[Portworx installer](https://install.portworx.com/)
For DCOS, Portworx is deployed through [Universe](https://docs.portworx.com/scheduler/mesosphere-dcos/install.html)

This `ansible` script is intended to help install Portworx
for [HashiCorp Nomad](https://www.nomadproject.io/), [Docker Swarm](https://docs.docker.com/engine/swarm/),
or for other environments where Portworx is to be deployed directly.

## Sample Playbook

Here is a sample playbook, that uses the `portworx` role
to create a custom ClusterID and install Portworx on all nodes:
```
---
- hosts: localhost
  tasks:
  - name: Set Unique ClusterID Once
    command : "uuidgen"
    register: clusterID
- hosts: all
  roles:
    - role: portworx
```

## Variables and Arguments

The sample variables provided are:
```
       kvdb : ""
       data_if : "-d eth1"
       mgmt_if : "-m eth1"
       cluster_id : "-c {{ hostvars['localhost']['clusterID'].stdout }}"
```

For a complete list of command line arguments, please see [https://docs.portworx.com/runc/options.html](https://docs.portworx.com/runc/options.html)

## Notes

The actual installation task is long running and does not provide incremental output

```
TASK [portworx : debug] ********************************************************
ok: [test1] => {
    "msg": "Command output does not display.  Command takes ~3-5 minutes to complete.  Please be patient ..."
}
...
```

Please be patient.
