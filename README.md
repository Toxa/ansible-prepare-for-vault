
# Ansible Playbook: Vault Integration for Linux Hosts

This Ansible playbook prepares a Linux host to use HashiCorp Vault for storing secrets. It sets up SSH access using certificates signed by Vault and configures a password rotation system that updates user passwords nightly and stores them in Vault.

## Requirements

- Ansible 2.10 or higher
- HashiCorp Vault with the [AppRole authentication method](https://www.vaultproject.io/docs/auth/approle)
- `community.hashi_vault` Ansible collection
- A running Vault server (`vault.example.com` in this case)
- Access to the Vault AppRole credentials (`role-id` and `secret-id`)

## Overview of the Playbook

### Tasks:

1. **Get Vault Role-ID:** Fetches the Role-ID for the `pass-rotation` AppRole.
2. **Get Vault Secret-ID:** Retrieves the Secret-ID for the `pass-rotation` AppRole based on the host's IP address.
3. **Install Required Packages:** Installs `curl` and `jq` on the host.
4. **Download CA File:** Retrieves the Vault SSH client signer public key to configure trusted CA for SSH.
5. **Configure SSH:** Updates the `sshd_config` with Vault's SSH configuration template and restarts SSH service.
6. **Modify bashrc:** Adds a logging marker to the `.bashrc` file.
7. **Create /root/bin Directory:** Ensures the `/root/bin` directory exists for storing scripts.
8. **Copy Password Rotation Script:** Copies the password rotation script (`vault-pass-rotate.sh`) to `/root/bin`.
9. **Add `sysadm` User:** Creates the `sysadm` user.
10. **Create `sysadm` Sudoers File:** Configures `sysadm` user with sudo permissions.
11. **Run Password Rotation for `sysadm` and `root`:** Executes the password rotation script for the `sysadm` and `root` users.
12. **Schedule Password Rotation via Cron:** Creates a cron job to rotate the `sysadm` password daily at midnight.

## Usage

### Running the Playbook

To run this playbook, execute the following command:

```bash
ansible-playbook -i inventory.ini main.yml
```

Ensure you have your Vault server's URL and credentials (Role-ID, Secret-ID) set up correctly.

### Variables

- `vault_url`: URL of the Vault server (default: `https://vault.example.com:8200`).
- `vault_role_id`: Role-ID used for AppRole authentication.
- `vault_secret_id`: Secret-ID used for AppRole authentication.

### Customizing the Templates

- **SSHD Config Template:** The playbook uses `templates/90-vault.conf.j2` for configuring SSH with Vault. You can customize this template if needed.
- **Password Rotation Script:** The playbook uses the `templates/vault-pass-rotate.sh.j2` to rotate passwords. Modify this script to suit your environment.

## Directory Structure

```bash
├── main.yml
├── templates/
│   ├── 90-vault.conf.j2
│   ├── bashrc.j2
│   └── vault-pass-rotate.sh.j2
└── README.md
```

## License

This playbook is released under the MIT License.

## Author

Anton Starikov
```
