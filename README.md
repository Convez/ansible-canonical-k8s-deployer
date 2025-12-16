This repository contains a simple, role-based Ansible playbook to deploy a Canonical Kubernetes (k8s snap) cluster. It is designed for high availability and security, ensuring that all nodes are patched via Ubuntu Pro and correctly identified for TLS certificate validation.
🏗 Cluster Architecture

The deployment manages three distinct node roles:

    Master: The primary node used to bootstrap the cluster identity.

    Control Plane: Additional nodes that join as API/Etcd peers for High Availability.

    Worker: Execution nodes dedicated to running containerized workloads.

📁 Project Structure
```text
ansible-k8s/
├── group_vars/
│   └── all.yml           # Global variables (Channel, Pro Token)
├── inventory.ini         # Host definitions
├── main.yml              # Main entry point & inventory validation
└── roles/
    ├── k8s_common/       # Pro activation, system upgrades, snap installation
    ├── k8s_master/       # Cluster bootstrapping 
    └── k8s_join/         # Token-based expansion 
```
🚀 Key Features

    Identity Sanity Check: Verifies that the inventory_hostname matches the machine's ansible_hostname. This prevents "Server certificate SAN" errors during the join process.

    Ubuntu Pro & ESM: Automatically attaches Pro tokens and performs a dist-upgrade on every run to ensure Extended Security Maintenance patches are applied.

    Intelligent Join Logic: Uses a robust status check that parses both stdout and stderr to handle the specific behavior of the k8s snap on worker nodes.

🛠 Setup & Configuration
1. Prerequisites
```text
    Ansible 2.18+ installed on your control machine.

    Target nodes running Ubuntu 22.04 or 24.04 LTS.

    Important: The names in your inventory.ini must match the actual system hostnames of the servers.
```
2. Configure Inventory (inventory.ini)
```ini
[master]
k8s-master-01 ansible_host=10.0.0.10 ansible_user="ubuntu" ansible_ssh_host_key="<KEY>"

[control]
k8s-control-02 ansible_host=10.0.0.11 ansible_user="ubuntu" ansible_ssh_host_key="<KEY>"

[worker]
k8s-worker-01 ansible_host=10.0.0.20 ansible_user="ubuntu" ansible_ssh_host_key="<KEY>"
k8s-worker-02 ansible_host=10.0.0.21 ansible_user="ubuntu" ansible_ssh_host_key="<KEY>"
```

3. Configure Variables (group_vars/all.yml)
```yaml
k8s_channel: "1.32/stable"
ubuntu_pro_token: "YOUR_TOKEN_HERE" # Optional: Leave empty to skip
```

📦 Deployment

To deploy the cluster, run:
```bash
ansible-playbook -i inventory.ini main.yml
```

To override variables at runtime (e.g., testing a different channel):
```bash
ansible-playbook -i inventory.ini main.yml -e "k8s_channel=1.35/edge"
```
