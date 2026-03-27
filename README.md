# 🔑 OpenClaw Skill: SSH Tunnel - Secure tunneling, port forwarding, and remote access.

![OpenClaw Skill](https://img.shields.io/badge/OpenClaw-Skill-blue) ![Status](https://img.shields.io/badge/Status-Active-green)

## Overview
The `ssh-tunnel` skill provides a comprehensive guide and set of patterns for leveraging SSH for secure tunneling, various forms of port forwarding, and advanced remote access configurations. This skill covers scenarios from simple local port forwards to complex multi-hop jump host setups, dynamic SOCKS proxies, SSH key management, connection multiplexing, and secure file transfers using `scp` and `rsync`. It's an essential tool for agents needing to establish secure connections, bypass firewalls, or manage remote resources effectively.

## Features
-   **Local Port Forwarding:** Access remote services as if they were running locally.
-   **Remote Port Forwarding:** Expose local services to remote machines.
-   **Dynamic Port Forwarding (SOCKS Proxy):** Create a SOCKS proxy for routing traffic through a remote server.
-   **Jump Hosts / Bastion:** Connect to internal networks via intermediary hosts (ProxyJump, ProxyCommand).
-   **SSH Config Management:** Utilize `~/.ssh/config` for streamlined and persistent connection settings.
-   **Connection Multiplexing:** Reuse existing SSH connections for faster subsequent sessions (`ControlMaster`).
-   **SSH Key Management:** Generate, deploy, and manage SSH keys and agent forwarding.
-   **Secure File Transfer:** Transfer files and directories efficiently and securely with `scp` and `rsync` over SSH.
-   **Connection Debugging:** Tools and tips for troubleshooting SSH connection issues.

## Installation
This skill relies on the standard `ssh` client, `scp`, and `rsync` utilities, which are typically pre-installed on macOS and Linux systems.
> [!NOTE]
> Ensure `ssh` and `rsync` are available in your system's PATH. No additional installation is required for the skill itself beyond these standard tools.

## Usage
The `ssh-tunnel` skill provides patterns and commands for various SSH functionalities.

### Port Forwarding
| Type            | Description                                                 | Command Example                                                                   |
| :-------------- | :---------------------------------------------------------- | :-------------------------------------------------------------------------------- |
| **Local**       | Access a remote service from your local machine.            | `ssh -L 5432:localhost:5432 user@remote-server`                                   |
| **Remote**      | Expose a local service to a remote machine.                 | `ssh -R 8080:localhost:3000 user@remote-server`                                   |
| **Dynamic**     | Create a SOCKS5 proxy on your local machine.                | `ssh -D 1080 user@remote-server`                                                  |

### Jump Hosts / Bastion
| Method          | Description                                                 | Command Example                                                                   |
| :-------------- | :---------------------------------------------------------- | :-------------------------------------------------------------------------------- |
| **ProxyJump**   | Connect through one or more bastion hosts (OpenSSH 7.3+).   | `ssh -J bastion-user@bastion.example.com target-user@internal-server`             |
| **ProxyCommand**| More flexible for older systems or complex setups.          | `ssh -o ProxyCommand="ssh -W %h:%p bastion-user@bastion" target-user@internal`  |

### SSH Config Patterns
Leverage `~/.ssh/config` to define hosts, users, identity files, ports, and advanced options like `ProxyJump`, `ControlMaster` for multiplexing, and `ServerAliveInterval` to prevent disconnections.

```ssh
# ~/.ssh/config example
Host bastion
    HostName bastion.example.com
    User bastion-user

Host app-server
    HostName 10.0.1.50
    User deploy
    ProxyJump bastion
    ControlMaster auto
    ControlPath ~/.ssh/sockets/%r@%h-%p
    ControlPersist 600

Host *
    ServerAliveInterval 60
    AddKeysToAgent yes
```
> [!TIP]
> Always create a `~/.ssh/sockets` directory (`mkdir -p ~/.ssh/sockets`) if using `ControlPath`.

### Key Management
| Action           | Description                                                     | Command Example                                                           |
| :--------------- | :-------------------------------------------------------------- | :------------------------------------------------------------------------ |
| **Generate Key** | Create a new SSH key pair (Ed25519 recommended).                | `ssh-keygen -t ed25519 -C "user@machine" -f ~/.ssh/mykey_ed25519`       |
| **Deploy Key**   | Copy your public key to a remote server's `authorized_keys`.    | `ssh-copy-id -i ~/.ssh/mykey_ed25519.pub user@remote-server`              |
| **SSH Agent**    | Start the agent and add keys to avoid re-entering passphrases.  | `eval "$(ssh-agent -s)" && ssh-add ~/.ssh/mykey_ed25519`                |
| **Permissions**  | Set correct file permissions for SSH keys and config.           | `chmod 600 ~/.ssh/id_ed25519` (private key)                               |

### File Transfer
| Tool         | Description                                                     | Command Example                                                                   |
| :----------- | :-------------------------------------------------------------- | :-------------------------------------------------------------------------------- |
| **`scp`**    | Simple file copy over SSH.                                      | `scp file.txt user@remote:/path/to/destination/`                                  |
| **`rsync`**  | Efficient file synchronization (only transfers changes).        | `rsync -avz ./local-dir/ user@remote:/path/to/remote-dir/`                        |

### Connection Debugging
Use verbose output for troubleshooting: `ssh -vvv user@remote`.
Common fixes include `ssh-keygen -R hostname` for host key changes or setting `IdentitiesOnly=yes` to prevent too many authentication failures.

## File Structure
```
/Users/charlesM/.openclaw/workspace/skills/ssh-tunnel/
├── SKILL.md      # Detailed documentation and usage guide for the OpenClaw skill.
└── _meta.json    # Metadata file for the OpenClaw skill.
```
