# Redbeard Time Tracker Technical Support

This guide applies to Redbeard Time Tracker 2.5.2. Start with the troubleshooting table, then collect the diagnostic information near the end if the problem continues.

For feature instructions, see the [User Guide](USER_GUIDE.md).

## Supported installation types

| Installation | Requirements | Authentication | Persistent data |
| --- | --- | --- | --- |
| Home Assistant app | Home Assistant OS or Supervised with custom app repositories; `aarch64` or `amd64` host | Home Assistant ingress | App-private `/config/timetracker.db` |
| Docker Compose | Current Docker Engine or Docker Desktop with Compose v2 | Application HTTP Basic authentication | Named volume mounted at `/data` |
| Native Node.js | Node.js 24 or newer and npm on Linux, Windows, or macOS | Application HTTP Basic authentication | Configured `DATA_DIR` or `DB_PATH` |

### Home Assistant architecture

The published Home Assistant image supports:

- `aarch64`, including a Raspberry Pi running a 64-bit Home Assistant operating system
- `amd64`, common on Intel/AMD PCs and servers

A 32-bit `armv7` installation is not supported by the published 2.5 image. Check Home Assistant's System information if the architecture is uncertain.

### Network access

Home Assistant must be able to reach:

- `github.com` to read the public app repository
- `ghcr.io` to download the private application image

The private image requires a GitHub personal access token (classic) with `read:packages`. Store it in **Home Assistant → Settings → Apps → three-dot menu → Registries** with registry `ghcr.io` and the GitHub username. Never put the token in a repository, screenshot, log, or support message.

### Browser requirements

Use a current release of Safari, Chrome, Edge, or Firefox with JavaScript and file downloads enabled. The Home Assistant companion app uses its embedded browser and is supported through ingress. Very old browsers are not supported.

### Storage and operating expectations

- Keep enough free storage for the container image, the active database, temporary upgrades, and multiple backups.
- The database directory must be writable by the application.
- Do not run two Time Tracker processes against the same SQLite file or mount the same live database into multiple containers.
- Keep the host's date, time, and timezone correct.
- Back up the database before upgrades or bulk data changes.

## Configuration reference

| Variable | Default | Notes |
| --- | --- | --- |
| `PORT` | `3000` | HTTP listening port inside the app |
| `HOST` | `0.0.0.0` | Listening address |
| `DATA_DIR` | `./data` | Directory used when `DB_PATH` is not supplied |
| `DB_PATH` | `<DATA_DIR>/timetracker.db` | Optional exact SQLite path |
| `AUTH_MODE` | `basic` | Use `none` only behind a trusted authentication layer such as Home Assistant ingress |
| `APP_USERNAME` | `admin` | Standalone login username |
| `APP_PASSWORD` | none | Required when `AUTH_MODE=basic` |

The application does not automatically load a `.env` file. Docker Compose loads the project `.env`; native installations must provide environment variables through the shell or service manager.

## Health and startup checks

The health endpoint is:

`http://<host>:<port>/api/health`

A healthy response contains status `ok` and the running version. The endpoint intentionally remains available without Basic authentication so Docker and supervisors can perform health checks.

A normal startup log includes lines similar to:

```text
Redbeard Time Tracker 2.5.2 running at http://0.0.0.0:3000
SQLite database: /config/timetracker.db
```

`Received SIGTERM; shutting down.` or `Received SIGINT; shutting down.` means an external supervisor or operator requested an orderly stop. It is not, by itself, an application crash.

## Where to find logs

### Home Assistant

1. Open **Settings → Apps → Redbeard Time Tracker**.
2. Open the app's **Log** tab for application startup and runtime messages.
3. For installation, update, image-download, or ingress failures, also inspect Home Assistant Supervisor logs under **Settings → System → Logs**.
4. Note the local timestamp of the failed action so it can be matched to the log.

### Docker Compose

Run from the project directory:

```bash
docker compose ps
docker compose logs --tail=200 time-tracker
```

### Native Node.js

Read the terminal output or the logs collected by the service manager used to start `npm start`.

## Common problems

| Symptom | Likely cause | What to do |
| --- | --- | --- |
| Home Assistant cannot add the repository | GitHub outage, network/DNS issue, or incorrect URL | Use `https://github.com/itspaulknoll/redbeard-time-tracker-homeassistant`, check GitHub status, then refresh the app store. |
| Installation says unauthorized, denied, or cannot pull image | Missing/expired GHCR credentials or token lacks `read:packages` | Recreate or rotate the classic token, update **Apps → Registries**, and retry. Do not paste the token into logs. |
| App is not offered on the device | Unsupported host architecture | Confirm the host is `aarch64` or `amd64`; the published image does not support `armv7`. |
| Update shows but says Up-to-date | Home Assistant has stale repository metadata or the advertised version was not changed everywhere | Refresh repository sources, reload the app store, and compare Installed and Latest versions. Do not repeatedly uninstall just to refresh metadata. |
| Install/update temporarily disconnects Home Assistant | Image download, extraction, storage pressure, or host resource contention | Wait several minutes, reconnect, then check the installed version and Supervisor logs. A Raspberry Pi may respond slowly during image replacement. |
| Installed app will not start | Image pull, configuration, architecture, storage, permissions, or database startup problem | Wait for the first start, then read both app and Supervisor logs. Look for the normal listening/database lines or the first actual error. |
| Bad Gateway when opening the app | Ingress reached the app before it was ready, the process stopped, or port 3000 is not healthy | Wait briefly and reopen. Check the app is running and inspect `/api/health` and logs. Restart once after collecting the error. |
| Standalone app refuses to start | `AUTH_MODE=basic` without `APP_PASSWORD`, unsupported Node version, or unwritable data directory | Set a strong password, confirm Node 24+, and verify write permission on `DATA_DIR`/`DB_PATH`. |
| Standalone password is forgotten | Password is supplied by the environment, not stored in SQLite | Change `APP_PASSWORD` in the service or Compose `.env` and restart. Do not edit the database. |
| A button appears to do nothing | A required field failed validation, the browser lost connection, or an old page is cached | Look for the visible form error, scroll to the first invalid field, confirm connectivity, and reload the page once. Preserve entered data before reloading when possible. |
| Save appears to require two presses | Slow connection or a failed refresh after the first save | Check whether the record was already created before pressing again. Reload and inspect logs if it repeats; include the exact form name in a support report. |
| Customer/project is missing from a selector | It is archived, excluded by the chosen customer, or hidden by search text | Clear the search, verify the customer selection, and enable **Show archived** on Customers. Restore the record if needed. |
| Time or report results are missing | Active filters, date boundaries, billing status, or soft deletion | Clear filters, check dates/status, and verify the record from the Time screen. |
| Billable total is $0 | Entry has no hourly rate or is non-billable | Edit the entry or configure a project/customer default rate. Existing entries keep their saved rate. |
| PDF invoice cannot be created | Business Profile incomplete, invalid billing period, no matching billable lines, or downloads blocked | Complete Business name and Payment instructions, preview the same filters, confirm dates, and allow the download. |
| PDF does not include an expected customer | Report customer/project/status/date filters exclude that customer's labor and trips | Clear or adjust the report filters and preview again. The invoice uses all customers in the current filtered result. |
| Invoice status did not change after PDF download | Invoice creation intentionally does not mutate billing records | Mark matching time entries and trip charges Invoiced after review; mark them Paid when payment arrives. |
| Trip charge is missing from a report/invoice | Trip is active, charge option is off, charge status does not match the filter, or trip is outside the dates | Complete/edit the trip, enable its trip charge, and check its independent billing status and dates. |
| Trip charge amount looks wrong | Charge is based on recorded business miles | Confirm distance. The formula is $45 plus $1 per mile over 25. |
| Ending odometer is rejected | Ending value is below the starting value or a required reading is absent | Enter both readings and make sure ending odometer is greater than or equal to starting odometer. |
| Location/GPS is unavailable | GPS tracking was deliberately removed | Use starting and ending odometer readings or manually enter miles for a past trip. The app should not request location access. |
| Timer or trip still appears active after stopping | The save completed but the browser lost the following refresh | Reload once and inspect the saved entry/trip before trying to stop it again. If it remains active, collect logs and a backup before discarding anything. |
| Downloaded database appears empty or is in an unexpected share | Home Assistant app data is private and is not the same as an SMB folder | Use **Settings → Download backup**. Do not copy a zero-byte or guessed `timetracker.db` from a share. |
| Database import rejects the file | File is not a direct SQLite backup, is corrupt, exceeds 250 MB, or is an encrypted Home Assistant archive | Use a `.db` downloaded by Time Tracker. Do not upload an encrypted `.tar` backup. Keep the original file unchanged. |
| Imported data is not what was expected | Wrong backup selected or counts were not verified | Stop changing data, keep both installations, compare counts, and return to the old installation or original backup. Do not uninstall the old app until verification passes. |
| CSV text or dates look unusual | Spreadsheet application inferred a type or local date format | Import the CSV explicitly as UTF-8, review column types, and set the desired date/time format. |

## Safe backup and recovery

### Preferred backup

Use **Settings → Download backup**. This creates a consistent SQLite snapshot while the app is running. A backup contains the Business Profile and custom invoice logo as well as customers, projects, time, mileage, and billing data.

### Import safeguards

The in-app importer:

1. Requires explicit replacement confirmation.
2. Accepts a direct SQLite upload up to 250 MB.
3. Checks the SQLite header and database integrity.
4. Applies current schema migrations to the candidate file.
5. Closes the active database only after validation.
6. Retains the previous database as `timetracker-pre-import-<date-and-id>.db`.
7. Attempts automatic rollback if activation fails.

After an import, compare entry, customer, and project counts and inspect representative time, mileage, and invoice records before removing the previous installation or backup.

### Legacy Home Assistant 2.5 identity migration

Version 2.5 uses the clean `redbeard_time_tracker` slug, so Home Assistant treats it as a new app with a different private data folder from the old test/modernization installation.

Use this order:

1. Download a database backup from the old app.
2. Leave the old app installed.
3. Install 2.5 from the public metadata repository.
4. Import the downloaded `.db` in the new app.
5. Verify counts and create a test invoice.
6. Download a fresh backup from 2.5.
7. Only then uninstall the old app.

## Upgrade troubleshooting

Before an upgrade, download a database backup and note the installed version and record counts.

For Home Assistant repository releases, three pieces must agree:

- The version in public `config.yaml`
- The private GHCR image tag
- The installed version displayed by Home Assistant

If metadata advertises a version whose image build failed, Home Assistant may show an update but fail to install it. Check the source repository's release workflow and GHCR package before repeatedly retrying on the Home Assistant host.

If an update fails, keep the existing installation and database in place. Collect the Supervisor error before uninstalling anything.

## Security guidance

- Home Assistant uses ingress authentication; its container intentionally uses `AUTH_MODE=none` behind ingress.
- Do not expose the Home Assistant container's internal port directly to an untrusted network.
- Standalone/Docker installations should use `AUTH_MODE=basic` with a long unique password.
- Basic authentication should be placed behind HTTPS, a trusted reverse proxy, or a private VPN when accessed beyond a trusted local network.
- The app has no per-user permissions or audit log. Anyone with access can view and change all records.
- Treat database backups and PDF invoices as confidential business records.
- Never share passwords, personal access tokens, complete databases, or unredacted customer information in a public issue.

## Information to collect for support

Provide:

1. Time Tracker version shown in Settings
2. Installation type: Home Assistant, Docker, or native Node.js
3. Host architecture and operating system
4. Browser or Home Assistant companion-app platform
5. Exact action performed and expected result
6. Exact visible error text
7. Local date/time when it happened
8. Relevant app logs and, for installation/update problems, Supervisor or container logs
9. Whether the problem continues after one reload or restart
10. Whether a current database backup is available

Before sharing logs, remove customer names, addresses, notes, invoice details, filesystem usernames, tokens, and passwords. Do not share the database unless a secure transfer has been explicitly arranged and the data owner has approved it.

## Escalation and last-resort recovery

If the app does not start but the database file is available:

1. Stop the app/container before manipulating files.
2. Preserve the original database without editing it.
3. Copy it to a separate safe location.
4. Start a clean installation with a new empty data directory.
5. Use the in-app importer with a downloaded backup when possible.

Do not delete an old Home Assistant app, Docker volume, or native data directory until the replacement installation has been verified and backed up.
