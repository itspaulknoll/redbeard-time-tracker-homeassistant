# Redbeard Time Tracker

Version 2.5.2 is a routine update for the production `redbeard_time_tracker` app. It improves recovery from temporary loading errors and cleans up the release process and documentation. It does not change the database schema or Home Assistant app identity.

The application source and GHCR image can remain private. The separate public Home Assistant repository contains only app metadata, documentation, and this changelog.

For day-to-day use, see the [User Guide](https://github.com/itspaulknoll/redbeard-time-tracker-homeassistant/blob/main/USER_GUIDE.md). For system requirements, logs, common errors, and recovery steps, see [Technical Support](https://github.com/itspaulknoll/redbeard-time-tracker-homeassistant/blob/main/SUPPORT.md).

## Before updating

1. Open the installed Redbeard Time Tracker app.
2. Go to **Settings → Download backup** and save the `.db` file somewhere safe.
3. Record the entry, customer, and project counts shown under Installation.
4. Confirm the private `ghcr.io/itspaulknoll/redbeard-time-tracker:2.5.2` image build completed successfully.

## Normal repository update

1. Go to **Settings → Apps → App store**.
2. Use the three-dot menu and select **Check for updates**.
3. Open the existing Redbeard Time Tracker app and confirm Latest version is **2.5.2**.
4. Keep the Home Assistant backup option enabled and choose **Update**.
5. After the app restarts, confirm Installed version is **2.5.2** and verify representative time and mileage records.

Routine updates keep the same app identity and private `/config` data. Do not copy a local app folder, create another installation, or import the database again.

## Allow Home Assistant to pull the private image

Create a GitHub personal access token (classic) with only `read:packages` and an appropriate expiration date. Do not place the token in this repository or share it in support messages.

In Home Assistant, open **Settings → Apps**, use the three-dot menu, and open **Registries**. Add:

- Registry: `ghcr.io`
- Username: your GitHub username
- Password: the read-only package token

Home Assistant stores this credential for private image pulls. Rotate it before it expires.

## First repository installation

1. Add `https://github.com/itspaulknoll/redbeard-time-tracker-homeassistant` as a Home Assistant app repository source.
2. Refresh the app store and install **Redbeard Time Tracker** from that repository.
3. Start the app and complete **Settings → Business profile**.
4. If migrating existing data, use **Settings → Import database** with a direct `.db` backup and verify record counts afterward.

## Legacy migration from a local app

The production identity introduced in 2.5.0 is separate from older local test/modernization installations. If one remains, download a database backup from the local app, import it into the repository-managed app, verify the data and a test invoice, then download a fresh backup. Only after those checks should the old local app be uninstalled and its old source folder removed. This is a one-time migration, not part of routine updates.

## Import safeguards

- The upload must be a valid SQLite database and pass SQLite's integrity check.
- Schema migrations run against the uploaded candidate before it becomes active.
- The database currently used by the new installation is retained as `timetracker-pre-import-<date-and-id>.db`.
- If activation fails, the app restores the pre-import database.
- Business Profile values and a custom invoice logo are stored inside SQLite, so subsequent backups and imports carry them together.

## Rollback

If an update fails, keep the existing installation in place and inspect the app and Supervisor logs before retrying. If the updated app starts but data is not as expected, stop making changes and preserve the downloaded database and Home Assistant backup before restoring anything.

This release does not request location permission or run a background location watcher. Historical route-derived mileage remains available in reports and exports, while all new trips use odometer readings.
