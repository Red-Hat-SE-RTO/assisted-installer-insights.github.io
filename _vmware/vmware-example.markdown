---
layout: post
title:  "OpenShift Agent Based Installer Helper  on VMware vSphere"
date:   2024-03-07 20:52:41 +0000
categories: jekyll update
---

# OpenShift Agent Based Installer Helper  on VMware vSphere

Git Repository: [https://github.com/Red-Hat-SE-RTO/openshift-agent-install](https://github.com/Red-Hat-SE-RTO/openshift-agent-install)

## Prerequisites

- A RHEL system to work from
- OpenShift CLI Tools - run `./download-openshift-cli.sh` then `sudo cp ./bin/* /usr/local/bin/`
- NMState CLI `dnf install nmstate`
- Ansible Core `dnf install ansible-core`
- Ansible Collections for the automation: `ansible-galaxy install -r openshift-agent-install/collections/requirements.yml`
- Red Hat OpenShift Pull Secret saved to a file: https://console.redhat.com/openshift/downloads#tool-pull-secret
- Any other Pull Secret for a disconnected registry, joined with the Red Hat OpenShift Pull Secret

## Usage - Declarative

In the `examples` directory you'll find sample cluster configuration variables.  By defining the cluster in its own folder with the `cluster.yml` and `nodes.yml` files, you can easily template and generate the ABI ISO in one shot with:

```bash
./hack/create-iso.sh $FOLDER_NAME
```

This script will take those defined files, generate the templates with Ansible, create the ISO, and present next step instructions.

Alternatively, you can perform those steps manually with the instructions below.

---

## Usage - Manual

### 1. Templating Agent Based Installer Manifests

You can quickly and easily template the ABI manifests with the provided `create-manifests.yml` Ansible Playbook.

```bash=
# Make sure you're in the `playbooks` directory
cd playbooks/

# Execute the automation with your custom cluster configuration set in a YAML file
ansible-playbook -e "@your-cluster-vars.yml" create-manifests.yml
```

### 2. Creating the Agent Installer ISO

After running the automation to generate the manifests, you can create the ISO with the following:

```bash=
# Create the ISO
openshift-install agent create image --dir ./generated_manifests/<cluster_name>

# Watch the Bootstrap process
openshift-install agent wait-for bootstrap-complete --dir ./generated_manifests/<cluster_name>

# Watch the installation process
openshift-install agent wait-for install-complete --dir ./generated_manifests/<cluster_name>
```

You'll need to provide it some variables such as the following: Plase us the `examples` url  for reference as it may be updated.

[vmware-example/cluster.yml](https://github.com/Red-Hat-SE-RTO/openshift-agent-install/blob/main/examples/vmware-example/cluster.yml)

#### General Configuration Variables

```yaml=
# pull_secret path is the path to the pull-secret for the cluster
pull_secret_path: ~/ocp-install-pull-secret.json

# ssh_public_key_path is the path to the SSH public key to use for the cluster
# if this is not set then a new key pair will be generated
# ssh_public_key_path: ~/.ssh/id_rsa.pub

# Cluster metadata
base_domain: example.com
cluster_name: ocp4

# platform_type is the type of platform to use for the cluster (baremetal, vsphere, none)
# must be none for SNO
platform_type: vsphere
vcenter_host: "portal.example.com"
vcenter_username: "administrator@example.com"
vcenter_password: "example_password"
vcenter_datacenter: "Datacenter"
vcenter_default_datastore: "Datastore"
vcenter_cluster: "Cluster"
vcenter_network: "VM Network"
vcenter_folder_name: "ocp4"
vcenter_disk_type: thin

# VIPs - set as a list in case this is a dual-stack cluster
api_vips:
  -  192.168.180.4

app_vips:
  -  192.168.180.5

# Optional NTP Servers
ntp_servers:
  - 0.rhel.pool.ntp.org
  - 1.rhel.pool.ntp.org

# Optional DNS Server definitions
dns_servers:
  - 192.168.180.9
  - 192.168.180.10
dns_search_domains:
  - example.com
  - example.network

# cluster_network_cidr is the overall CIDR space for the Pods in the cluster
cluster_network_cidr: 10.128.0.0/14
# cluster_network_host_prefix is the number of bits in the cluster_network_cidr that are for each node
cluster_network_host_prefix: 23

# service_network_cidrs is the CIDR space for the Services in the cluster (ClusterIP/NodePort/LoadBalancer)
service_network_cidrs:
  - 172.30.0.0/16

# machine_network_cidr is the CIDR space for the Machines in the cluster
machine_network_cidrs:
  - 192.168.180.0/23

# networkType is the type of network to use for the cluster (OpenShiftSDN, OVNKubernetes)
network_type: OVNKubernetes

# rendezvous_ip is the IP address of the node that will be used for the bootstrap node
rendezvous_ip: 192.168.180.21

# Optional Outbound Proxy Configuration
# proxy:
#   http_proxy: http://192.168.42.31:3128
#   https_proxy: http://192.168.42.31:3128
#   no_proxy:
#     - .svc.cluster.local
#     - 192.168.0.0/16
#     - .example.network
#     - .example.labs

# Optional Additional CA Root Trust Bundle

create_ztp_manifests: false
#cluster_architecture: x86_64 # x86_64 | s390x | ppc64le | aarch64 | multi
```


#### HA Cluster Deployment
[vmware-example/nodes.yml](https://github.com/Red-Hat-SE-RTO/openshift-agent-install/blob/main/examples/vmware-example/nodes.yml)

```yaml=
# Node Counts the installer will expect
control_plane_replicas: 3
app_node_replicas: 3
octect: 192.168.180
node_one_mac: EC:F4:BB:C0:B9:C8
node_two_mac: EC:F4:BB:C0:B9:C9
node_three_mac: EC:F4:BB:C0:B9:CA
node_four_mac: EC:F4:BB:C0:B9:CB
node_five_mac: EC:F4:BB:C0:B9:CC
node_six_mac: EC:F4:BB:C0:B9:CD

# nodes defines the nodes to use for the cluster
nodes:
  - hostname: node-1
    role: master
    rootDeviceHints:
      deviceName: /dev/sda
      #deviceName: /dev/nvme0n1
    interfaces:
      - name: ens192
        mac_address: "{{ node_one_mac }}"
    networkConfig:
      interfaces:
        - name: ens192
          type: ethernet
          state: up
          mac-address: "{{ node_one_mac }}"
          ipv4:
            enabled: true
            address:
              - ip: "{{ octect }}.21"
                prefix-length: 24
            dhcp: false
      routes:
        config:
          - destination: 0.0.0.0/0
            next-hop-address: "{{ octect }}.1"
            next-hop-interface: ens192
            table-id: 254
  - hostname: node-2
    role: master
    rootDeviceHints:
      deviceName: /dev/sda
      #deviceName: /dev/nvme0n1
    interfaces:
      - name: ens192
        mac_address: "{{ node_two_mac }}"
    networkConfig:
      interfaces:
        - name: ens192
          type: ethernet
          state: up
          mac-address: "{{ node_two_mac }}"
          ipv4:
            enabled: true
            address:
              - ip: "{{ octect }}.22"
                prefix-length: 24
            dhcp: false
      routes:
        config:
          - destination: 0.0.0.0/0
            next-hop-address: "{{ octect }}.1"
            next-hop-interface: ens192
            table-id: 254
  - hostname: node-3
    role: master
    rootDeviceHints:
      deviceName: /dev/sda
      #deviceName: /dev/nvme0n1
    interfaces:
      - name: ens192
        mac_address: "{{ node_three_mac }}"
    networkConfig:
      interfaces:
        - name: ens192
          type: ethernet
          state: up
          mac-address: "{{ node_three_mac }}"
          ipv4:
            enabled: true
            address:
              - ip: "{{ octect }}.23"
                prefix-length: 24
            dhcp: false
      routes:
        config:
          - destination: 0.0.0.0/0
            next-hop-address: "{{ octect }}.1"
            next-hop-interface: ens192
            table-id: 254
  - hostname: node-4
    role: worker
    rootDeviceHints:
      deviceName: /dev/sda
      #deviceName: /dev/nvme0n1
    interfaces:
      - name: ens192
        mac_address: "{{ node_four_mac }}"
    networkConfig:
      interfaces:
        - name: ens192
          type: ethernet
          state: up
          mac-address: "{{ node_four_mac }}"
          ipv4:
            enabled: true
            address:
              - ip: "{{ octect }}.24"
                prefix-length: 24
            dhcp: false
      routes:
        config:
          - destination: 0.0.0.0/0
            next-hop-address: "{{ octect }}.1"
            next-hop-interface: ens192
            table-id: 254
  - hostname: node-5
    role: worker
    rootDeviceHints:
      deviceName: /dev/sda
      #deviceName: /dev/nvme0n1
    interfaces:
      - name: ens192
        mac_address: "{{ node_five_mac }}"
    networkConfig:
      interfaces:
        - name: ens192
          type: ethernet
          state: up
          mac-address: "{{ node_five_mac }}"
          ipv4:
            enabled: true
            address:
              - ip: "{{ octect }}.25"
                prefix-length: 24
            dhcp: false
      routes:
        config:
          - destination: 0.0.0.0/0
            next-hop-address: "{{ octect }}.1"
            next-hop-interface: ens192
            table-id: 254
  - hostname: node-6
    role: worker
    rootDeviceHints:
      deviceName: /dev/sda
      #deviceName: /dev/nvme0n1
    interfaces:
      - name: ens192
        mac_address: "{{ node_six_mac }}"
    networkConfig:
      interfaces:
        - name: ens192
          type: ethernet
          state: up
          mac-address: "{{ node_six_mac }}"
          ipv4:
            enabled: true
            address:
              - ip: "{{ octect }}.26"
                prefix-length: 24
            dhcp: false
      routes:
        config:
          - destination: 0.0.0.0/0
            next-hop-address: "{{ octect }}.1"
            next-hop-interface: ens192
            table-id: 254
```
