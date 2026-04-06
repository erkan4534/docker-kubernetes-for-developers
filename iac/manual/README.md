# Manual Kubernetes Installation

Step-by-step commands to manually prepare nodes and bootstrap a Kubernetes cluster using kubeadm.

## Overview

This approach runs commands directly on each node via SSH. It covers:

1. Kernel module loading (overlay, br_netfilter)
2. Sysctl network settings for Kubernetes
3. Container runtime installation (containerd via docker.io)
4. Kubernetes package installation (kubelet, kubeadm, kubectl)

## Usage

1. Provision servers (any cloud provider or bare metal)
2. SSH into each node and run the commands from `node_preparations.txt`
3. On the **master** node, run `kubeadm init`
4. On each **worker** node, run the `kubeadm join` command from the master's output

## Files

- `node_preparations.txt` - Commands to prepare each node before running kubeadm
