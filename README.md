# Keycloak user onboarding with Ansible

This project contains:

- A docker-compose stack for a local Keycloak in dev mode
- An Ansible inventory and config
- A reusable role (`keycloak_user`) that pushes user definitions to Keycloak

## Prerequisites

- Docker and docker-compose plugin
- Ansible >= 2.15
- Required collection: `ansible-galaxy collection install community.general`

## Usage

1. Start Keycloak locally:
   ```bash
   docker compose up -d
   ```
   The admin console will be available at http://127.0.0.1:8080

2. (One-time) install the collection:
   ```bash
   ansible-galaxy collection install -r requirements.yml
   ```
   or directly `ansible-galaxy collection install community.general`.

3. Update connection parameters if necessary in `inventory/group_vars/all/sa-user.yml` (defaults point to the local dev Keycloak).

4. Define users in `inventory/group_vars/all/keycloak_users.yml`, adjusting any attributes you need.

5. Apply the playbook:
   ```bash
   ansible-playbook playbooks/add_keycloak_users.yml
   ```

## Customizing users

Edit `inventory/group_vars/all/keycloak_users.yml` (or host/group-specific vars) to change the `keycloak_users` list. Each user supports:

- `username` (required)
- `first_name`, `last_name`, `email`
- `enabled` (default true)
- `email_verified`
- `password` (optional) and `temporary_password`
- `credential_type` (default `password`)
- `attributes` dict passed through to Keycloak
- `groups` list (example: `[{ name: devs }, { name: ops, state: present }]`);
  entries default to `state: present`, and missing groups are auto-created

You can also override connection parameters per host or via extra vars.
The shared defaults live in `inventory/group_vars/all/sa-user.yml`.
