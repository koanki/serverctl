# Server Management Script

This shell script provides an interactive command-line tool for managing Unix-like servers. It allows users to log in via SSH, upload and download files, execute commands, and manage server configurations efficiently.

## Features

- **Server Group Management**: Organizes servers into groups for easier access.
- **SSH Login**: Login to servers using a username and password.
- **File Transfers**: Upload and download files with support for batch operations and wildcards.
- **Command Execution**: Execute predefined commands or custom commands on remote servers.
- **Color-Coded Output**: Enhanced user experience with color-coded console output for better readability.
- **User-Friendly Selection**: Easily select servers and actions using intuitive prompts.

## Requirements

- **jq**: A command-line JSON processor. Install it using your package manager (e.g., `apt`, `brew`, `yum`).
- **sshpass**: A tool for non-interactive SSH password authentication. Install it as well.

## Configuration

### Server Configuration File

Create a `servers.json` file that defines your server groups, servers, and commands. Here's an example structure:

```json
{
  "server_groups": {
    "Development Servers": [
      {
        "name": "dev-server1",
        "ip": "192.168.1.100",
        "vault_path": "path/to/vault",
        "vault_prefix": "unix_login",
        "description": "Main development server",
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
        "vault_path": "path/to/vault",
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
