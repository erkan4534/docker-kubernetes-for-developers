# Infrastructure as Code

This directory contains three different approaches to provision a Kubernetes cluster on Hetzner Cloud. Pick the one that fits your needs.

## Options

| Option | Tool | K8s Flavor | Complexity | Best For |
|---|---|---|---|---|
| [hetzner-terraform/](hetzner-terraform/) | Terraform + cloud-init | kubeadm (v1.35) | Medium | Full control over cluster setup |
| [kubespray/](kubespray/) | Ansible (Kubespray) | kubeadm | High | Production-grade, highly configurable |
| [manual/](manual/) | Shell scripts | kubeadm | Low | Learning / step-by-step understanding |

There is also a [hetzner_k8s.yaml](hetzner_k8s.yaml) config file for [hetzner-k3s](https://github.com/vitobotta/hetzner-k3s), which provisions a k3s cluster with autoscaling support.

## Quick Comparison

### hetzner-terraform/
- Terraform manages the full lifecycle (create, update, destroy)
- cloud-init bootstraps nodes, `local-exec` automates worker join
- Kubeconfig pulled to local machine automatically
- Calico CNI

### kubespray/
- Ansible-based, uses the [Kubespray](https://kubespray.io/) project
- Requires a pre-provisioned inventory of hosts
- Highly customizable (CNI, container runtime, addons)

### manual/
- Raw shell commands from `node_preparations.txt`
- Run manually on each node via SSH
- Good for understanding what happens under the hood

### hetzner_k8s.yaml
- Config file for the `hetzner-k3s` CLI tool
- Lightweight k3s instead of full kubeadm
- Built-in autoscaling, multi-location masters
- Flannel CNI
