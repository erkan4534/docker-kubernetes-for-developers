# Kubespray

Deploys a production-ready Kubernetes cluster using [Kubespray](https://kubespray.io/) (Ansible-based).

## Architecture

- 1 control-plane node (etcd co-located)
- N worker nodes
- Calico CNI, CoreDNS, containerd runtime
- Fully configurable via Ansible group_vars

## Prerequisites

- Python 3.x
- Servers already provisioned and accessible via SSH (e.g., from `hetzner-terraform`)
- Node preparation completed (see `../manual/`)

## Usage

```bash
# Clone kubespray
git clone https://github.com/kubernetes-sigs/kubespray.git kubespray-repo
cd kubespray-repo

# Create virtual environment and install dependencies
python3 -m venv .venv
source .venv/bin/activate
pip install -r ../requirements.txt

# Copy the inventory into kubespray
cp -r ../inventory/mycluster inventory/mycluster

# Edit the inventory with your server IPs
vim inventory/mycluster/inventory.ini

# Run the playbook
ansible-playbook -i inventory/mycluster/inventory.ini cluster.yml -b
```

## Customization

- `inventory/mycluster/inventory.ini` - Node IPs and roles
- `inventory/mycluster/group_vars/all/all.yml` - General cluster settings
- `inventory/mycluster/group_vars/k8s_cluster/k8s-cluster.yml` - Kubernetes config (CNI, proxy mode, DNS, etc.)
- `inventory/mycluster/group_vars/k8s_cluster/addons.yml` - Optional addons (Helm, MetalLB, cert-manager, etc.)

## Reset Cluster

```bash
ansible-playbook -i inventory/mycluster/inventory.ini reset.yml -b
```
