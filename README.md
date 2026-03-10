# Berth Agent

Lightweight deployment agent for [Berth](https://getberth.dev) — runs on your Linux server and receives deployments from the Berth desktop app or CLI.

## Install

```bash
curl -sSL https://agent.getberth.dev/install.sh | sudo bash
```

The installer will:
- Create a `berth` system user
- Download the agent binary for your architecture (x86_64 or aarch64)
- Ask you to choose a connection mode (Synadia Cloud or Direct)
- Install a systemd service with auto-restart and auto-rollback

## Connection Modes

### Synadia Cloud (recommended)

Uses [Synadia Cloud](https://cloud.synadia.com) NATS relay — zero inbound ports required, works behind NAT and firewalls. Both the agent and desktop app connect outbound to Synadia's infrastructure.

1. Create a free account at [cloud.synadia.com](https://cloud.synadia.com)
2. Create a Team and System
3. Download your agent credentials (`.creds` file)
4. The installer will ask for the credentials file path during setup

After installation, the agent displays a pairing code. Enter it in **Berth → Targets → Pair Agent**.

### Direct Connection (mTLS)

The desktop app connects directly to the agent's IP. Requires network reachability and uses mutual TLS (mTLS) for encryption and authentication.

During installation, the agent generates TLS certificates automatically. Copy the CA and client certificates to your desktop machine and import them in **Berth → Settings → Direct Connection (mTLS)**.

You can also generate certificates manually:
```bash
sudo -u berth berth-agent init-tls
```

## Useful commands

```bash
systemctl status berth-agent          # check status
journalctl -u berth-agent -f          # follow logs
berth-agent update                    # self-update to latest version
berth-agent update --version 0.3.0    # update to specific version
```

## Uninstall

```bash
curl -sSL https://agent.getberth.dev/install.sh | sudo bash -s -- --uninstall
```

## How it works

The agent is a single Rust binary with zero runtime dependencies. It supports two connection modes:

- **Synadia Cloud**: Both sides connect outbound to your Synadia NATS account. Zero inbound ports, secure by default.
- **Direct**: The agent listens on port 50051 with mTLS. The desktop app connects directly using client certificates.

Features:
- Deploy and run code (Python, Node.js, Go, Rust, shell scripts)
- Dependency installation (pip, npm, go mod, cargo)
- Cron-style scheduling (runs jobs even when the app is offline)
- Public URL publishing via cloudflared tunnels
- Auto-upgrade with rollback on failure
- SQLite-backed execution history and log storage

## Requirements

- Linux (x86_64 or aarch64)
- systemd
- No other dependencies

## License

This project is licensed under the [Business Source License 1.1](LICENSE) (BSL-1.1).

- **Change Date**: March 10, 2030
- **Change License**: Apache License, Version 2.0

After the change date, the code converts to Apache 2.0. You may freely use, modify, and self-host Berth for your own deployments. See the [LICENSE](LICENSE) file for full terms.
