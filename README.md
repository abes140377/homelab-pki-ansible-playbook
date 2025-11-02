# Homelab Pki Ansible Project

## Included content/ Directory Structure

The directory structure follows best practices recommended by the Ansible
community. Feel free to customize this template according to your specific
project requirements.

```shell
ansible-project/
|── .github/
|    └── workflows/
|        └── tests.yml
|    └── ansible-code-bot.yml
|── .vscode/
|    └── extensions.json
|── collections/
|   └── requirements.yml
|   └── ansible_collections/
|       └── project_org/
|           └── project_repo/
|               └── README.md
|               └── roles/sample_role/
|                         └── README.md
|                         └── tasks/main.yml
|── inventory/
|   |── hosts.yml
|   └── groups_vars/
|   └── host_vars/
|── ansible.cfg
|── README.md
|── site.yml
```

## Compatible with Ansible-lint

Tested with ansible-lint >=24.2.0 releases and the current development version
of ansible-core.
