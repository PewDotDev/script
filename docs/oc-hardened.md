# `oc-hardened.sh`

Back to repo index: [`README.md`](../README.md)

`oc-hardened.sh` sets up a fresh Ubuntu 24.04 server with a safer default config, then runs OpenClaw as a dedicated sudo user.

## Quick Start

```bash
curl -fsSL https://script.pew.dev/oc-hardened.sh | sudo bash
```

## What It Does

- Creates/reuses a user (default: `openclaw`) and adds sudo access
- Sets up SSH hardening (`PermitRootLogin no`, password auth off, key auth on, `AllowUsers <user>`)
- Configures UFW (SSH only by default)
- Installs/configures `fail2ban` and `unattended-upgrades`
- Runs OpenClaw install as the dedicated user

## Requirements

- Ubuntu `24.04`
- Run as root (`sudo`)
- You must have an SSH key for the target user, or one in `/root/.ssh/authorized_keys`
- If no key exists, the script stops before SSH hardening (to avoid lockout)

## Common Commands

Default:

```bash
curl -fsSL https://script.pew.dev/oc-hardened.sh | sudo bash
```

Custom user + SSH port:

```bash
curl -fsSL https://script.pew.dev/oc-hardened.sh | sudo bash -s -- \
  --user myadmin \
  --ssh-port 2222
```

Pass your public key directly:

```bash
curl -fsSL https://script.pew.dev/oc-hardened.sh | sudo bash -s -- \
  --user myadmin \
  --authorized-key "ssh-ed25519 AAAA... you@laptop"
```

Allow web ports (`80`, `443`):

```bash
curl -fsSL https://script.pew.dev/oc-hardened.sh | sudo bash -s -- --allow-web-ports
```

Dry run:

```bash
curl -fsSL https://script.pew.dev/oc-hardened.sh | sudo bash -s -- --dry-run
```

## Options

- `--user <name>` (default: `openclaw`)
- `--ssh-port <port>` (default: `22`)
- `--openclaw-cmd <cmd>` (default: `curl -fsSL https://openclaw.ai/install.sh | bash`)
- `--authorized-key <key>`
- `--skip-user-password` (no `passwd` prompt)
- `--no-switch-user` (do not auto-switch to the user shell at the end)
- `--allow-web-ports` (allow `80/tcp` and `443/tcp`)
- `--dry-run`
- `-h`, `--help`

## Notes

- OpenClaw command runs once per user+command hash marker: `/var/lib/openclaw-bootstrap/openclaw_cmd.<user>.sha256`
- Remove that file to force re-run with the same command.
- `--authorized-key` must be a single valid OpenSSH public key line.
- In non-interactive runs, use `--skip-user-password` if you cannot answer `passwd` prompts.

## Important

Before closing your current root session, confirm SSH login works for the dedicated user.
