# Server Management Script with Vault Integration

This shell script provides a robust solution for managing multiple Unix-like servers through SSH. It integrates with HashiCorp Vault for secure credential management, allowing users to log in via SSH, transfer files, execute commands, and manage server configurations easily and securely.

## Features

- **Server Group Management**: Organize servers into groups for easier access and management.
- **Vault Integration**: Securely retrieve server credentials (username and password) from HashiCorp Vault.
- **SSH Login**: Automatically log in to servers using credentials stored in Vault.
- **File Transfers**: Upload and download files (including batch operations) to/from servers, with support for wildcards.
- **Command Execution**: Execute predefined or custom commands on remote servers.
- **Color-Coded Output**: Enhanced user experience with color-coded console output for better readability.
- **fzf (Fuzzy Finder) Integration**: Optional fuzzy search to select servers and commands more quickly.
- **Custom Commands**: Run general or server-specific custom commands and log them for future use.

## Requirements

Before using this script, ensure the following dependencies are installed:

- **jq**: A command-line JSON processor to handle `servers.json`. Install it using your package manager: 
  ```bash
  sudo apt-get install jq       # Ubuntu/Debian
  brew install jq               # macOS
  ```

-  sshpass: A tool for non-interactive SSH password authentication. Install it using:
  ```bash
  sudo apt-get install sshpass   # Ubuntu/Debian
  brew install hudochenkov/sshpass/sshpass  # macOS
  ```

- Vault CLI: HashiCorp Vault CLI to retrieve secrets. Install it by following Vault's official installation guide.

- fzf (optional): Fuzzy finder to improve server and command selection. Install with:
  ```bash
  sudo apt-get install fzf       # Ubuntu/Debian
  brew install fzf               # macOS
  ```

Configuration

Vault Configuration
The script uses HashiCorp Vault to securely retrieve server credentials. The servers.json file specifies the Vault paths and prefixes for each server. 
Ensure you have the Vault CLI configured and authenticated to access the secrets.

Vault Entry Structure:

For each server, store the username and password in Vault. For example, for a server entry unix_login:
unix_login_name → Contains the username.
unix_login_pw → Contains the password.

```bash
vault kv put secret/unix_login_name value="myusername"
vault kv put secret/unix_login_pw value="mypassword"
```

Server Configuration File
Create a servers.json file to define your server groups and their associated credentials. The file should include the Vault path and the credential prefixes.
```json
{
  "server_groups": {
    "Development Servers": [
      {
        "name": "dev-server1",
        "ip": "192.168.1.100",
        "vault_path": "secret/data/unix_login",
        "vault_prefix": "unix_login",
        "description": "Development server for testing",
        "commands": [
          {
            "name": "Check Disk Space",
            "command": "df -h"
          },
          {
            "name": "List Running Processes",
            "command": "ps aux"
          }
        ]
      }
    ],
    "Production Servers": [
      {
        "name": "prod-server1",
        "ip": "192.168.1.101",
        "vault_path": "secret/data/unix_login",
        "vault_prefix": "unix_login",
        "description": "Main production server",
        "commands": [
          {
            "name": "Check Web Server Status",
            "command": "systemctl status nginx"
          }
        ]
      }
    ]
  },
  "general_commands": [
    {
      "name": "Update Packages",
      "command": "sudo apt-get update && sudo apt-get upgrade"
    }
  ]
}
```

Shell Script Configuration
Ensure the servers.json path is correctly set in the script:

```bash
SERVERS_FILE="path/to/servers.json"
```




