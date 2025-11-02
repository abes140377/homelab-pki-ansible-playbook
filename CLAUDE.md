# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is an Ansible playbook project for managing homelab PKI infrastructure. The project follows the structure recommended by `ansible-creator` and includes a local collection (`homelab.pki`) embedded in the repository at `collections/ansible_collections/homelab/pki/`.

## Development Environment Setup

The project uses `mise` for environment management:

```bash
# Install dependencies (runs automatically on mise enter)
mise run install-deps

# Manual installation if needed
uv pip install -r requirements.txt
uv pip uninstall -q pytest-ansible  # Avoid plugin conflicts
```

Dependencies are managed via `requirements.txt` (Python) and `requirements.yml` (Ansible collections).

## Running Playbooks

### Standard Execution

```bash
# Run the main playbook
ansible-playbook site.yml

# Run with specific inventory
ansible-playbook -i inventory/hosts.yml site.yml

# Check mode (dry run)
ansible-playbook site.yml --check

# Limit to specific hosts
ansible-playbook site.yml --limit vault1
```

### Dagger Execution

```bash
# Run via Dagger containerized environment
mise run dagger:ansible-build
```

## Project Structure

### Key Directories

- `collections/ansible_collections/homelab/pki/` - Local collection containing roles and modules
- `inventory/` - Inventory files with host and group variables
  - `hosts.yml` - Main inventory file
  - `group_vars/` - Group-specific variables
  - `host_vars/` - Host-specific variables
- `site.yml` - Main playbook entrypoint
- `ansible.cfg` - Ansible configuration

### Collection Structure

The embedded `homelab.pki` collection follows standard Ansible collection layout:
- `roles/install/` - Installation role with tasks in `tasks/main.yml`
- `galaxy.yml` - Collection metadata
- `meta/runtime.yml` - Collection runtime configuration

## Configuration

### Ansible Settings (ansible.cfg)

- Collections path: `./collections`
- Roles path: `./roles`
- Inventory variables: `inventory/host_vars` and `inventory/group_vars`
- Become method: `sudo`
- SSH: Host key checking disabled for lab environment

### Inventory

The inventory defines:
- `vault_servers` group for PKI/Vault infrastructure
- Individual hosts with connection details (ansible_host, ansible_user, ansible_ssh_private_key_file)

## Ansible Development Guidelines

Follow the practices described in AGENTS.md, which references https://raw.githubusercontent.com/ansible/ansible-creator/refs/heads/main/docs/agents.md

### Key Principles

- **YAML formatting**: 2-space indentation, `.yml` extensions, double quotes for strings, max 160 char lines
- **Booleans**: Use `true`/`false` (not `yes`/`no`)
- **Task names**: Imperative form with capital letters (e.g., "Debug print task-1")
- **Idempotency**: All tasks must be idempotent - no changes on second run
- **Variables**: Use `snake_case`, prefix role variables with role name to avoid collisions
- **Modules**: Use specific modules instead of generic `command` or `shell` when possible

### Role Development

- Design roles focused on functionality, not software implementation
- Support check mode with accurate change reporting
- Use handlers instead of `when: foo_result is changed`
- All role variables should be prefixed with role name (e.g., `install_x` for the `install` role)

### Testing

The project uses:
- `ansible-lint` for linting (tested with >=24.2.0)
- `pytest` with `pytest-testinfra` for testing
- Molecule for role testing (part of ansible-dev-tools)

```bash
# Lint playbooks and roles
ansible-lint

# Run tests
pytest
```

## Common Tasks

### Adding a New Role

Roles should be created within the collection structure at `collections/ansible_collections/homelab/pki/roles/`:

```bash
# Create role structure manually or use ansible-galaxy
ansible-galaxy role init --init-path collections/ansible_collections/homelab/pki/roles/ role_name
```

### Installing Collection Dependencies

```bash
# Install required collections
ansible-galaxy collection install -r requirements.yml
```

### SSH Key Management

The project expects an SSH key at `keys/ansible_id_ecdsa` (created by mise hooks from the `ANSIBLE_SSH_PRIVATE_KEY` environment variable).
