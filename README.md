# Ansible Config Collector

This project uses Ansible to connect to a list of network devices and retrieve their **raw running configuration**. Each device's config is saved as a `.cfg` file with a date-based filename.

---

## Features

- Works with Cisco IOS and Arista EOS devices
- Uses `network_cli` connection method
- One `.cfg` file per device (timestamped)
- Supports real and simulated testing (via localhost)
- Organized using Ansible roles and inventory

---

## Directory Structure

```
ansible_config_collector/
├── inventory/                   # Static inventory
│   └── networkdevices
├── playbooks/
│   └── get_running_config.yml  # Main playbook
├── roles/
│   └── collect_config/
│       └── tasks/
│           └── main.yml
├── group_vars/
│   └── all.yml                 # Global vars
├── output/                     # Output .cfg files
├── ansible.cfg
├── Makefile
└── README.md
```

---

## Inventory Example

`inventory/networkdevices`

```ini
[cisco]
cisco-r1 ansible_host=10.0.0.1 ansible_network_os=ios

[arista]
arista-r1 ansible_host=10.0.0.2 ansible_network_os=eos

[all:vars]
ansible_user=admin
ansible_password=changeme
ansible_connection=network_cli
```

---

## Usage

### 1. Install required Ansible collections:

```bash
ansible-galaxy collection install ansible.netcommon cisco.ios arista.eos
```

### 2. Run the playbook against real devices:

```bash
make run
```

Or manually:

```bash
ansible-playbook -i inventory/networkdevices playbooks/get_running_config.yml
```

To limit to one device:

```bash
ansible-playbook -i inventory/networkdevices playbooks/get_running_config.yml --limit cisco-r1
```

---

## Simulated (Dev) Mode

You can test output generation without live devices:

```bash
ansible-playbook -i inventory/networkdevices playbooks/get_running_config.yml --limit localhost
```

This will create:

```
output/cisco-sw1-YYYY-MM-DD.cfg
```

With simulated config data.

---

## Output

Each device will output to:

```
output/<hostname>-YYYY-MM-DD.cfg
```

Example:

```
output/cisco-r1-2025-05-19.cfg
output/arista-r1-2025-05-19.cfg
```

---

## Makefile Commands

```makefile
make run         # Run the full config collector playbook
```

---

## Notes for Integration Team

- Update `inventory/networkdevices` with **real hostnames or IPs**
- Ensure devices are reachable and SSH is enabled
- If needed, customize `group_vars/all.yml` for different device types or users

---

## Requirements

- Ansible 2.12+
- Python 3.10+
- Collections:
  - `ansible.netcommon`
  - `cisco.ios`
  - `arista.eos`

---

## Version

This project is `v1.0.0` and ready for deployment or extension.

---