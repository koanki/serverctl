# Servalizer

**Servalizer** is a powerful and flexible server management tool designed to simplify server access, command execution, and file transfers across Unix-like and Windows servers. It supports SSH for Unix-like systems and RDP (via `mstsc`) for Windows nodes, with the ability to securely store and retrieve credentials from a HashiCorp Vault. Additional features include batch execution, command customization, and server grouping for more efficient operations.

## Features
- **Automatic login**: Login to Unix-like or Windows servers using credentials stored in HashiCorp Vault.
- **Command execution**: Execute general or server-specific commands on the selected server or batch across groups.
- **Batch file transfers**: Upload or download files with support for wildcards.
- **Color-coded output**: Improve readability with customizable colors in the terminal.
- **FZF (optional)**: Enable fuzzy searching for server and command selection.
- **Batch execution**: Run commands in parallel across multiple servers or groups.
- **Error handling**: Friendly error messages and guidance for unsupported OS actions.

## Installation

1. Install necessary dependencies:
   - `jq`: A lightweight command-line JSON processor.
   - `fzf`: (Optional) A command-line fuzzy finder for easier selection.

2. Download or clone the repository and make the script executable:
   ```bash
   chmod +x servalizer.sh

	3.	Add servalizer to your PATH for easy access:

export PATH=$PATH:/path/to/servalizer



Usage

General syntax:

servalizer [options]

Options:

Option	Description
-h, --help	Display help message and usage information
--no-fzf	Disable fuzzy finding for manual server and command selection
--no-color	Disable color output in the terminal

Example usage:

	•	Basic server login:

servalizer

	•	Select the server from the list or use FZF for fuzzy searching.
	•	Automatically log in using credentials retrieved from Vault.

	•	Execute a command on a server:

servalizer

	•	Choose a server, then select the command you want to execute.

	•	Batch command execution on all servers in a group:

servalizer --batch


	•	Transfer files:

servalizer --upload /local/file/path /remote/directory
servalizer --download /remote/file/path /local/directory



Configuration File (server.json)

The configuration file defines the servers, their groups, and the commands associated with them. Here’s a sample server.json file:

{
  "general_commands": [
    {
      "name": "Check disk space",
      "command": "df -h"
    },
    {
      "name": "Show memory usage",
      "command": "free -m"
    }
  ],
  "server_groups": {
    "Core Servers": [
      {
        "server": "linux-server-111",
        "description": "Ansible Controller",
        "vault_path": "kv/jenkins",
        "vault_prefix": "UNIX_LOGIN",
        "os": "linux",
        "commands": [
          {
            "name": "Stop Jenkins Service",
            "command": "sudo systemctl stop jenkins"
          },
          {
            "name": "Backup Jenkins configs",
            "command": "tar -czf /backup/jenkins_$(date +%F).tar.gz /var/lib/jenkins"
          }
        ]
      },
      {
        "server": "windows-server-112",
        "description": "SQL Server",
        "vault_path": "kv/sql",
        "vault_prefix": "WIN_LOGIN",
        "os": "windows"
      }
    ]
  }
}

Customization and Color Coding

The script uses customizable color-coded output to enhance readability. You can disable the colors with the --no-color option or modify the color scheme directly in the script by changing the color variables.

	•	Example colors:
	•	Red: Used for errors or warnings.
	•	Green: Used for success messages.
	•	Cyan: Used for general information or prompts.
	•	Yellow: Used for warnings or lack of available commands.

Error Handling

When executing a command that is not supported for a specific OS, Servalizer provides clear and helpful error messages. Example:

print_color "$RED" "Error: The action you attempted is not supported for the selected OS: '$SERVER_OS'. Please review the available commands for this OS or try again with a different server. Exiting."

This ensures that the user understands the problem and is given options to resolve it.

Adding New Commands

To add new general or server-specific commands, simply edit the server.json file. General commands can be added to the general_commands array, while server-specific commands should be added under the appropriate server entry in server_groups.

Example: Backup Jenkins Before Upgrade

{
  "name": "Backup Jenkins",
  "command": "tar -czf /backup/jenkins_backup_$(date +%F).tar.gz /var/lib/jenkins /etc/default/jenkins"
}

Example: Restore Jenkins from Backup

{
  "name": "Restore Jenkins",
  "command": "tar -xzf /backup/jenkins_backup.tar.gz -C /"
}

Windows Support

For Windows servers, Servalizer uses mstsc for login. You can configure Windows-specific settings and commands in the server.json file, and for authentication, you can use cmdkey.

Example Windows login:

mstsc /v:<hostname>

If credentials are stored in Vault, they will be retrieved automatically for use in the login process.

Future Enhancements

Some possible enhancements include:

	•	Web-based interface for easier management and visibility.
	•	REST API support for remote command execution.
	•	Expanded Windows support with more advanced features.

License

Servalizer is released under the MIT License.