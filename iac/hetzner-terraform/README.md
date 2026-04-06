# Kubernetes Cluster on Hetzner Cloud

Provisions a kubeadm-based Kubernetes cluster on Hetzner Cloud using Terraform.
One control plane node + configurable worker nodes. Worker join is fully automated via `local-exec` provisioners.

## Prerequisites

- Terraform >= 1.5
- SSH key pair at `~/.ssh/id_rsa` (or override via variables)
- `HCLOUD_TOKEN` environment variable set in `~/.zshrc`:

```bash
export HCLOUD_TOKEN="your-hetzner-api-token"
```

## Quick Start

```bash
cp terraform.tfvars.example terraform.tfvars   # adjust if needed
terraform init
terraform plan
terraform apply
```

After apply completes, a `kubeconfig.yaml` file is saved locally:

```bash
export KUBECONFIG=$(pwd)/kubeconfig.yaml
kubectl get nodes
```

## Tear Down

```bash
terraform destroy
```

## Variables

| Variable | Description | Type | Default |
|---|---|---|---|
| `location` | Hetzner datacenter location | `string` | `fsn1` |
| `master_server_type` | Server type for the control plane node | `string` | `cpx22` |
| `worker_server_type` | Server type for worker nodes | `string` | `cpx22` |
| `worker_count` | Number of worker nodes | `number` | `2` |
| `os_image` | OS image for all nodes | `string` | `ubuntu-24.04` |
| `ssh_public_key_path` | Path to SSH public key | `string` | `~/.ssh/id_rsa.pub` |
| `ssh_private_key_path` | Path to SSH private key | `string` | `~/.ssh/id_rsa` |
| `network_zone` | Hetzner network zone | `string` | `eu-central` |
| `network_cidr` | CIDR for the private network | `string` | `10.0.0.0/16` |
| `subnet_cidr` | CIDR for the subnet | `string` | `10.0.1.0/24` |
| `pod_network_cidr` | CIDR for the Kubernetes pod network | `string` | `192.168.0.0/16` |
| `kubernetes_version` | Kubernetes minor version for apt repo | `string` | `1.35` |

## Outputs

| Output | Description |
|---|---|
| `master_public_ip` | Public IP of the control plane node |
| `worker_public_ips` | Public IPs of the worker nodes |
| `ssh_to_master` | SSH command to connect to the master |

## Architecture

```
terraform apply
  |
  +--> hcloud_network + subnet + firewall + ssh_key
  |
  +--> hcloud_server.master
  |      cloud-init: node prep + kubeadm init + Calico CNI
  |      remote-exec: waits for cloud-init to finish
  |
  +--> hcloud_server.worker[0..N]
  |      cloud-init: node prep (containerd + kubelet)
  |      remote-exec: waits for cloud-init to finish
  |
  +--> null_resource.worker_join[0..N]
  |      local-exec: fetches join command from master, runs on worker
  |
  +--> null_resource.kubeconfig
         local-exec: pulls kubeconfig to ./kubeconfig.yaml
```

## File Structure

```
hetzner-terraform/
├── versions.tf              # hcloud provider, local state
├── variables.tf             # all input variables
├── network.tf               # private network + subnet
├── firewall.tf              # K8s required ports
├── ssh.tf                   # SSH key upload
├── servers.tf               # master + workers + join + kubeconfig
├── outputs.tf               # IPs and SSH command
├── terraform.tfvars.example # sample variable values
└── templates/
    ├── master.yaml.tpl      # cloud-init: node prep + kubeadm init + Calico
    └── worker.yaml.tpl      # cloud-init: node prep only
```
