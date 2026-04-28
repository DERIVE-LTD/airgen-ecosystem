# Configuring the two-track backup model and restoring from restic

AIRGen runs two complementary automated backup tracks against a
[restic](https://restic.net) remote. The full-database track captures
the whole stack for disaster recovery; the per-project track exports
individual projects as portable Cypher scripts. This guide covers
configuring both, choosing a restic remote, verifying integrity, and
restoring.

> **Prerequisites:**
>
> - A self-hosted AIRGen instance ([self-hosting AIRGen with Docker
>   Compose](./self-hosting-airgen-with-docker-compose.md)).
> - A storage backend supported by restic — DigitalOcean Spaces, AWS
>   S3, Backblaze B2, SFTP, or local disk.
> - About 30 minutes to wire up the first run.

## The two tracks at a glance

| Track            | Scope                                                        | Schedule                                |
| ---------------- | ------------------------------------------------------------ | --------------------------------------- |
| Full-database    | Neo4j data dir tar.gz, PostgreSQL `pg_dump`, config files.   | Daily 02:00 UTC + weekly Sunday 03:00 UTC. Verify run at 02:30 UTC. |
| Per-project      | Single project as a self-contained Cypher script, registered in a `project_backups` PostgreSQL catalog and uploaded with `tenant:` / `project:` tags. | Daily 04:00 UTC + weekly Sunday 05:00 UTC (parallelised). |

Both tracks upload to the same restic remote. restic provides
client-side AES-256 encryption and content-addressed deduplication on
the wire — successive backups send only changed chunks.

## 1. Set up the restic remote

restic supports many backends. The two most common for AIRGen:

### DigitalOcean Spaces (S3-compatible)

```sh
export RESTIC_REPOSITORY=s3:fra1.digitaloceanspaces.com/your-bucket/airgen
export RESTIC_PASSWORD=long-random-encryption-passphrase
export AWS_ACCESS_KEY_ID=...
export AWS_SECRET_ACCESS_KEY=...
```

### Backblaze B2

```sh
export RESTIC_REPOSITORY=b2:your-bucket-name:airgen
export RESTIC_PASSWORD=long-random-encryption-passphrase
export B2_ACCOUNT_ID=...
export B2_ACCOUNT_KEY=...
```

### Local disk

For testing or air-gapped sites:

```sh
export RESTIC_REPOSITORY=/var/backups/airgen-restic
export RESTIC_PASSWORD=long-random-encryption-passphrase
```

### Initialise the repo

```sh
restic init
```

`restic init` prints a summary; the repo password and storage
credentials are now what stand between an attacker and your data.
Store them in a password manager, *not* in the script that runs the
backups.

## 2. Configure AIRGen's backup environment

AIRGen reads the same env vars (`RESTIC_REPOSITORY`,
`RESTIC_PASSWORD`, and the backend-specific credentials) when it runs
the scripts in `scripts/`. Add them to a private file:

```sh
sudo tee /etc/airgen-backup.env > /dev/null <<'EOF'
RESTIC_REPOSITORY=s3:...
RESTIC_PASSWORD=...
AWS_ACCESS_KEY_ID=...
AWS_SECRET_ACCESS_KEY=...
EOF
sudo chmod 600 /etc/airgen-backup.env
```

The backup scripts source this file before each run, so credentials
never appear in cron output.

## 3. Schedule the four cron jobs

```cron
# Full-database track
0  2 * * *   . /etc/airgen-backup.env && /opt/airgen/scripts/backup-daily.sh
0  3 * * 0   . /etc/airgen-backup.env && /opt/airgen/scripts/backup-weekly.sh

# Verification
30 2 * * *   . /etc/airgen-backup.env && /opt/airgen/scripts/backup-verify.sh

# Per-project track
0  4 * * *   . /etc/airgen-backup.env && /opt/airgen/scripts/backup-all-projects.sh --remote --retention --continue-on-error
0  5 * * 0   . /etc/airgen-backup.env && /opt/airgen/scripts/backup-all-projects.sh --remote --retention --parallel 3
```

Place these in `/etc/cron.d/airgen-backup` (root-owned, mode 644).

## 4. Manual backup commands

Sometimes you want a one-off backup ahead of a deploy or before a
risky operation:

```sh
# Full stack
./scripts/backup-weekly.sh

# Single project (Cypher + remote upload)
./scripts/backup-project.sh <tenant> <projectKey> --remote --retention

# All projects in one pass
./scripts/backup-all-projects.sh --remote --retention --continue-on-error

# Verify integrity of the most recent backup
./scripts/backup-verify.sh /path/to/backup
```

## 5. Retention

The default retention runs `restic forget` with conservative rules:

- Keep all daily backups for 7 days.
- Keep all weekly backups for 12 weeks.
- Keep all monthly backups for 12 months.

restic's content-addressed storage means even multi-month retention
costs surprisingly little disk — only the diff is stored. Adjust the
rules in `scripts/backup-daily.sh` etc. if your domain has stricter
retention requirements.

## 6. Restore from a backup

### Full-stack restore

```sh
# List available snapshots
restic snapshots

# Restore the latest snapshot to /tmp/restore
restic restore latest --target /tmp/restore

# Then run the AIRGen restore script against the extracted files
./scripts/backup-restore.sh /tmp/restore
```

The restore script:

1. Stops the backend.
2. Restores the Neo4j data directory.
3. Loads the PostgreSQL dump.
4. Replays the config.
5. Restarts the stack.

Test this on a non-production host before relying on it.

### Per-project restore

The per-project Cypher format is portable: you can restore a single
project to a different AIRGen instance entirely.

```sh
# Find the project's snapshot in restic
restic snapshots --tag tenant:acme --tag project:widget-x

# Pull the Cypher script out
restic restore <snapshot-id> \
  --target /tmp/restore \
  --include 'projects/widget-x.cypher'

# Replay it against your AIRGen Neo4j
cypher-shell -u neo4j -p $GRAPH_PASSWORD < /tmp/restore/projects/widget-x.cypher
```

For migration / DR scenarios where the destination's tenant or
project naming differs, edit the Cypher script before replay.

## 7. Verify integrity

The 02:30 UTC verify cron job runs `restic check` (subset by default
to keep the run cheap; full `--read-data` once a week is reasonable
for paranoia). It writes a status line to syslog. Pipe that into
your alerting:

```sh
journalctl -u cron --since "yesterday" | grep airgen-backup-verify
```

Do **not** rely on the existence of recent snapshots alone — check
the verify run.

## 8. Practice the restore

A backup that has never been tested may not be a backup. Once a
quarter:

1. Spin up a fresh AIRGen host (or a test compose project on the
   same host).
2. Run `./scripts/backup-restore.sh` against the latest snapshot.
3. Smoke-test: log in, list projects, run `airgen reqs list` against
   a known project.
4. Tear down.

If anything fails, the time to find out is now, not during an
incident.

## What's next

- [Self-hosting AIRGen with Docker Compose](./self-hosting-airgen-with-docker-compose.md)
- [Using the AIRGen CLI](./using-the-airgen-cli.md)
