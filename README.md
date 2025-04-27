# Kubernetes Cluster Deployment with Ansible (Kubernetes v1.33)

This repository contains an Ansible playbook and associated roles for deploying a Kubernetes cluster (v1.33) on RHEL-based systems. The setup includes configuring the necessary system settings, installing container runtime (containerd), and deploying Kubernetes components such as `kubeadm`, `kubelet`, and `kubectl`.

## Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Prerequisites](#prerequisites)
- [Setup](#setup)
  - [Step 1: Clone the Repository](#step-1-clone-the-repository)
  - [Step 2: Update Inventory](#step-2-update-inventory)
  - [Step 3: Run the Playbook](#step-3-run-the-playbook)
- [Role Structure](#role-structure)
- [Key Configuration Details](#key-configuration-details)
- [Kubernetes Version](#kubernetes-version)
- [Conclusion](#conclusion)

---

## Overview

This project automates the deployment of a Kubernetes cluster using Ansible. It is designed for RHEL-based systems and ensures that all necessary configurations, such as SELinux, swap, and container runtime, are properly set up before initializing the Kubernetes cluster.

---

## Features

- **Kubernetes v1.33**: Deploys the latest stable release of Kubernetes.
- **Container Runtime**: Configures and installs `containerd` as the container runtime.
- **Pod Network**: Configures the pod network CIDR for Calico (`10.244.0.0/16`).
- **System Configuration**: Automates tasks like disabling SELinux, turning off swap, and configuring sysctl parameters.
- **Role-Based Structure**: Modular Ansible role for better organization and reusability.

---

## Prerequisites

1. **Operating System**: RHEL-based systems (e.g., RHEL, CentOS, Rocky Linux).
2. **Ansible**: Installed on the control node (version 2.9 or higher recommended).
3. **SSH Access**: Passwordless SSH access to all target nodes.
4. **Inventory File**: Define the target nodes in the `inventory` file under the `tests/` directory.
5. **Root Privileges**: Ensure the Ansible user has `sudo` privileges.

---

## Setup

### Step 1: Clone the Repository

Clone this repository to your Ansible control node:

```bash
git clone https://github.com/your-username/k8s-ansible-rhel.git
cd k8s-ansible-rhel
```

## Step 2: Update Inventory
Edit the tests/inventory file to include the IP addresses or hostnames of your target nodes. For example:
```env
[masters]
master-node ansible_host=192.168.1.10 ansible_user=ansible

[workers]
worker-node1 ansible_host=192.168.1.11 ansible_user=ansible
worker-node2 ansible_host=192.168.1.12 ansible_user=ansible
```

## Step 3: Run the Playbook
Run the Ansible playbook to deploy the Kubernetes cluster:

```bash
ansible-playbook -i tests/inventory [main.yaml]
```

## Role Structure
The repository follows a modular role-based structure:

```env
kubernetes_cluster/
├── ansible.cfg          # Ansible configuration file
├── defaults/
│   └── [main.yml](http://_vscodecontentref_/2)         # Default variables
├── files/               # Static files
├── handlers/
│   └── [main.yml](http://_vscodecontentref_/3)         # Handlers for tasks
├── meta/
│   └── [main.yml](http://_vscodecontentref_/4)         # Role metadata
├── tasks/
│   └── [main.yml](http://_vscodecontentref_/5)         # Main tasks for the role
├── templates/           # Jinja2 templates
├── tests/
│   ├── inventory        # Inventory file for testing
│   └── test.yml         # Test playbook
└── vars/
    └── [main.yml](http://_vscodecontentref_/6)         # Role-specific variables
```

## Key Configuration Details
## 1. System Configuration
**SELinux**: Set to permissive mode.
**Swap**: Disabled to meet Kubernetes requirements.
**Sysctl Parameters**: Configured for Kubernetes networking:

```bash
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1
net.bridge.bridge-nf-call-ip6tables = 1
```

## 2. Container Runtime
Installs and configures containerd as the container runtime.
Ensures CRI plugin is enabled and SystemdCgroup is set to true.

## 3. Kubernetes Installation
- Adds the Kubernetes repository.
- Installs kubeadm, kubelet, and kubectl.
- Initializes the Kubernetes cluster on the master node using kubeadm:

```bash
kubeadm init --pod-network-cidr={{ kubernetes_pod_network_cidr }}
    --apiserver-advertise-address={{ ansible_default_ipv4.address }}
    --ignore-preflight-errors=SystemVerification,CRIService,NumCPU,Mem
```
## 4. Pod Network
- Configures Calico as the pod network with the CIDR 10.244.0.0/16.

### Kubernetes Version
This playbook is designed for Kubernetes v1.33, the latest stable release. Key features of this version include:

- Enhanced support for container runtimes.
- Improved scalability and performance.
- Updated sandbox image: registry.k8s.io/pause:3.10.

### Conclusion
This Ansible playbook simplifies the deployment of a Kubernetes cluster on RHEL-based systems. By automating system configurations, container runtime setup, and Kubernetes installation, it ensures a consistent and reliable deployment process.

If you have any questions or suggestions, feel free to open an issue or submit a pull request. Happy automating!

## License
This project is licensed under the MIT License. See the LICENSE file for more details.