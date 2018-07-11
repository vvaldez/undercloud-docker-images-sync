# Docker Registry for Undercloud

## Usage
Based on https://access.redhat.com/documentation/en-us/red_hat_openstack_platform/13/html/director_installation_and_usage/configuring-a-container-image-source#Configuring-Registry_Details-Local

To use this role, call it with something such as:

---
- hosts: undercloud
  tasks:
    - import_role:
        name: undercloud-docker-registry
...

## Workflow
The undercloud will be confiugred as a docker registry.

There are some built-in checks to see if this target undercloud is already configured as a docker registry. If so, it will skip the configuration and display a message that a variable called 'docker_registry_force' can be set to True to allow the undercloud docker registry to be updated.

There is also a check duing the docker configuration if /etc/syscoinfig/docker has already been modified to point to itself and the upstream repo, it will not re-configure this file.

## Verifying
Test by looking at the output of the output-env-file (overcloud-images.yaml) and doing a `docker pull <image>` with a image url in that file.

e.g.

[root@undercloud stack]# grep iscsid overcloud-images.yaml
  DockerIscsidConfigImage: 172.16.0.1:8787/rhosp13/openstack-iscsid:13.0-38
  DockerIscsidImage: 172.16.0.1:8787/rhosp13/openstack-iscsid:13.0-38

[root@undercloud stack]# docker pull 172.16.0.1:8787/rhosp13/openstack-iscsid:13.0-38
Trying to pull repository 172.16.0.1:8787/rhosp13/openstack-iscsid ...
13.0-38: Pulling from 172.16.0.1:8787/rhosp13/openstack-iscsid
e0f71f706c2a: Pull complete
121ab4741000: Pull complete
2824d3a8e244: Pull complete
edb8df6b610c: Pull complete
Digest: sha256:69e2bd1af17c609a42a2aadd2632f87b94d697e6f8036d747486f1f59fcf062e
Status: Downloaded newer image for 172.16.0.1:8787/rhosp13/openstack-iscsid:13.0-38

[root@undercloud stack]# docker images
REPOSITORY                                 TAG                 IMAGE ID            CREATED             SIZE
172.16.0.1:8787/rhosp13/openstack-iscsid   13.0-38             47f41decc493        3 weeks ago         458 MB

## Syncing
Normally the local registry will sync with upstream. To disable this set 'docker_registry_sync: False'

This also happens to be a good way to do a read-only check on an undercloud to see if it has a running registry and list the images with -v

