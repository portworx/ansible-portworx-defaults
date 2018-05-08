[![Build Status](https://travis-ci.org/portworx/ansible-portworx-defaults.svg?branch=master)](https://travis-ci.org/portworx/ansible-portworx-defaults)
# Install Portworx through Ansible

[ ***experimental*** ]
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
  vars:
    - kvdb: "-k etcd://70.0.1.2:2379"
    - data_if: "-d eth1"
    - mgmt_if: "-m eth1"
    - device_args: "-s /dev/sdb"
    - cluster_id: "-c {{ hostvars['localhost']['clusterID'].stdout }}"
  roles:
     - ../../ansible-portworx-defaults
```

The following variables are ``required``:
* ``kvdb``  :  IPaddress:port of 'etcd' or 'consul' kvdb
* ``cluster_id``
* ``device_args`` :  should correspond to list of devices (each with a `-s`), or should be ``-a`` to use all available devices

Please note that the corresponding ``prefix`` must be included in the variable clause (ex: ``-s /dev/sdb``)

The ``cluster_id`` must be the same for all nodes in the Portworx cluster, but unique among any clusters sharing the same ``kvdb``.
The actual value can have a friendly name (ex: ``-c my-portworx-cluster``)

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
