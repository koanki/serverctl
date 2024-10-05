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

- **sshpass**: A tool for non-interactive SSH password authentication. Install it using:

  ```bash
  sudo apt-get install sshpass   # Ubuntu/Debian
  brew install hudochenkov/sshpass/sshpass  # macOS
  ```

- **Vault CLI**: HashiCorp Vault CLI to retrieve secrets. Install it by following [Vault's official installation guide](https://www.vaultproject.io/docs/install).

- **fzf (optional)**: Fuzzy finder to improve server and command selection. Install with:

  ```bash
  sudo apt-get install fzf       # Ubuntu/Debian
  brew install fzf               # macOS
  ```

## Configuration

### Vault Configuration

The script uses **HashiCorp Vault** to securely retrieve server credentials. The `servers.json` file specifies the Vault paths and prefixes for each server. Ensure you have the Vault CLI configured and authenticated to access the secrets.

**Vault Entry Structure**:
- For each server, store the username and password in Vault. For example, for a server entry `unix_login`:
  - `unix_login_name` → Contains the username.
  - `unix_login_pw` → Contains the password.

```bash
vault kv put secret/unix_login_name value="myusername"
vault kv put secret/unix_login_pw value="mypassword"
```

### Server Configuration File

Create a `servers.json` file to define your server groups and their associated credentials. The file should include the Vault path and the credential prefixes.

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

### Shell Script Configuration

Ensure the `servers.json` path is correctly set in the script:

```bash
SERVERS_FILE="path/to/servers.json"
```

## Usage

1. **Run the script**:
   ```bash
   ./serverctl.sh
   ```

2. **Select a server group**:
   - You will be prompted to choose a group of servers from `servers.json`.

3. **Select an action**:
   - You can log in via SSH, upload/download files, or execute commands (predefined or custom).

### Example Interaction

```bash
$ ./serverctl.sh

Available server groups:
1) Development Servers
2) Production Servers

Enter the number of the server group: 1
Selected group: Development Servers

Available servers in group 'Development Servers':
1) dev-server1 - Development server for testing

Enter the number of the server to connect to: 1
Selected server: dev-server1

Select an action:
1) Login via SSH with password (retrieved from Vault)
2) Upload files to the server
3) Download files from the server
4) Execute a command on the server
5) Execute a custom command on the server

Enter the number of the action: 5
Selected action: Execute a custom command on the server

Enter the custom command to execute on the server: ls -l /home/dev

Executing command on dev-server1: ls -l /home/dev
total 8
drwxrwxr-x 2 dev dev 4096 Oct 5 09:13 logs
-rw-r--r-- 1 dev dev 2048 Oct 5 09:14 README.txt
```

## Batch File Transfers

- **Upload**: Supports uploading multiple files using wildcards (`*`).
- **Download**: Similarly, you can download multiple files by specifying wildcards.

### Example:

```bash
# Upload all `.txt` files to the remote server's `/home/dev/` directory
./serverctl.sh --upload "*.txt" /home/dev/

# Download all `.log` files from the server to the local `logs/` directory
./serverctl.sh --download "*.log" ./logs/
```

## Custom Command Execution

You can execute any custom command on the server. The command is logged to a file for future reference, and you will have an option to repeat it from the history.

```bash
$ ./serverctl.sh --custom-command "ls -l /var/log"
```

## Error Handling

- **SSH Connection Failures**: If SSH login fails, an error message will be displayed, and you will be prompted to retry or exit.
- **Vault Access Issues**: If the credentials cannot be retrieved from Vault, the script will alert you and exit.

## Color Coding

- **Success Messages**: Displayed in green.
- **Error Messages**: Displayed in red.
- **Info/Prompts**: Displayed in yellow for clarity.

To disable color coding, run the script with the `--no-color` flag:

```bash
./serverctl.sh --no-color
```

## Customization

1. **General Commands**: Add general commands to the `servers.json` under `"general_commands"` to make them available for all servers.
2. **Server-Specific Commands**: Each server in `servers.json` can have its own set of commands.

## License

This project is licensed under the MIT License. See the LICENSE file for more information.

## Acknowledgements

- [jq](https://stedolan.github.io/jq/) for JSON processing.
- [sshpass](https://github.com/kevinburke/sshpass) for handling SSH password authentication.
- [fzf](https://github.com/junegunn/fzf) for fuzzy searching.
