# Berth Agent

Lightweight deployment agent for [Berth](https://getberth.dev) — runs on your Linux server and receives deployments from the Berth desktop app or CLI.

## Install

```bash
curl -sSL https://agent.getberth.dev/install.sh | sudo bash
```

This will:
- Create a `berth` system user
- Download the agent binary for your architecture (x86_64 or aarch64)
- Install a systemd service with auto-restart
- Set up auto-rollback on failed upgrades

## Configure

After installation, configure NATS relay for remote control (no inbound ports needed):

```bash
sudo nano /home/berth/.berth/agent.env
```

Uncomment and set:
```bash
BERTH_NATS_URL=tls://connect.ngs.global
BERTH_NATS_CREDS=/home/berth/.berth/nats.creds
BERTH_NATS_AGENT_ID=my-server
```

Then restart:
```bash
sudo systemctl restart berth-agent
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

The agent is a single Rust binary with zero runtime dependencies. It communicates with the Berth desktop app via NATS (zero inbound ports required — both sides connect outbound). It can also accept direct gRPC connections on port 50051.

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

The install and rollback scripts in this repository are MIT licensed. The agent binary is proprietary software distributed by [Berth](https://getberth.dev).
