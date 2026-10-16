# server-bootstrap

Declarative bootstrap for fresh DigitalOcean droplets. When a new server is
created, its cloud-init `user-data` clones this repository and:

1. Installs every apt package listed in [`tools.txt`](tools.txt)
2. Installs [Kimi Code CLI](https://www.kimi.com/code) (AI coding agent)
3. Clones every repository listed in [`repos.txt`](repos.txt) into `~/projects`
4. Runs Kimi Code unattended with the task in [`kimi-task.md`](kimi-task.md)
   so each project gets its dependencies installed and validated

## Files

| File           | Purpose                                              |
| -------------- | ---------------------------------------------------- |
| `tools.txt`    | apt packages to install (one per line, `#` comments) |
| `repos.txt`    | GitHub repos to clone, `username/repo` per line      |
| `kimi-task.md` | Instructions Kimi Code executes on first boot        |

## Usage

1. Fork or copy this repository to your own GitHub account.
2. Edit `tools.txt` and `repos.txt` to match your setup.
3. Create a DigitalOcean droplet (Ubuntu 24.04) and paste the
   `cloud-config.yaml` user-data (kept locally — **never commit it**).
4. SSH in after boot and check the logs:

   ```bash
   cat /var/log/cloud-init-output.log   # full bootstrap log
   cat /var/log/kimi-bootstrap.log      # Kimi Code task output
   kimi doctor                          # validate the CLI configuration
   ```

## Security notes

- **No secrets in this repo.** API keys and tokens live only in the
  droplet's `user-data`, are written to a `0600` file at boot, and are
  shredded after the bootstrap finishes.
- `kimi-task.md` runs with `--yolo` (no interactive approvals). Only list
  repositories you trust in `repos.txt`.
- Private repositories require a fine-grained GitHub PAT (`GH_TOKEN`) in
  the `user-data`. With a public bootstrap repo, prefer cloning public
  repos only.
