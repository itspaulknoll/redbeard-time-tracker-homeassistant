# Redbeard Time Tracker

Version 2.5.1 continues the production `redbeard_time_tracker` identity introduced in 2.5.0. It includes the Redbeard app artwork, a shorter sidebar title, and improved mileage route layout. The private Business Profile and validated database import workflow remain available.

The application source and GHCR image can remain private. The separate public Home Assistant repository contains only app metadata, documentation, and this changelog.

For day-to-day use, see the [User Guide](https://github.com/itspaulknoll/redbeard-time-tracker-homeassistant/blob/main/USER_GUIDE.md). For system requirements, logs, common errors, and recovery steps, see [Technical Support](https://github.com/itspaulknoll/redbeard-time-tracker-homeassistant/blob/main/SUPPORT.md).

## Before installing

1. Open the old Redbeard Time Tracker app.
2. Go to **Settings → Download backup** and save the `.db` file somewhere safe.
3. Record the entry, customer, and project counts shown under Installation.
4. Leave the old app and its source folder installed until the migration is verified.
5. Confirm the private `ghcr.io/itspaulknoll/redbeard-time-tracker:2.5.1` image build completed successfully.

## Allow Home Assistant to pull the private image

Create a GitHub personal access token (classic) with only `read:packages` and an appropriate expiration date. Do not place the token in this repository or share it in support messages.

In Home Assistant, open **Settings → Apps**, use the three-dot menu, and open **Registries**. Add:

- Registry: `ghcr.io`
- Username: your GitHub username
- Password: the read-only package token

Home Assistant stores this credential for private image pulls. Rotate it before it expires.

## Recommended repository-source migration

1. Add `https://github.com/itspaulknoll/redbeard-time-tracker-homeassistant` as a Home Assistant app repository source.
2. Refresh the app store and install **Redbeard Time Tracker 2.5.1** from that repository.
3. Open the new app. It will initially have an empty database.
4. Go to **Settings → Import database**.
5. Select the backup downloaded from the old app, check the confirmation box, and choose **Validate and import**.
6. After the automatic reload, compare the entry, customer, and project counts with the old app.
7. Complete **Settings → Business profile** with the invoice contact and payment details. These values are stored only in the imported SQLite database.
8. Generate a test invoice and download a fresh database backup.
9. Only after all checks pass, stop and uninstall the old app and remove its old local source folder.

The new hostname contains `redbeard-time-tracker` and no longer contains `time-tracker-modernization-test`. Repository installations may still include Home Assistant's repository identifier before the clean slug.

## Local migration test

To test before publishing the metadata repository, copy the generated `dist/redbeard-time-tracker-2-5-1` folder into the Home Assistant `addons` share under a new folder name. Do not overwrite the old source folder. Refresh local apps, install the 2.5.1 copy, and follow the same import and verification steps.

## Import safeguards

- The upload must be a valid SQLite database and pass SQLite's integrity check.
- Schema migrations run against the uploaded candidate before it becomes active.
- The database currently used by the new installation is retained as `timetracker-pre-import-<date-and-id>.db`.
- If activation fails, the app restores the pre-import database.
- Business Profile values and a custom invoice logo are stored inside SQLite, so subsequent backups and imports carry them together.

## Rollback

If verification fails, stop the new app and start the old app again. The migration never changes the old app's private data. Keep both the original downloaded backup and the automatically retained pre-import backup until the new installation has been used successfully.

This release does not request location permission or run a background location watcher. Historical route-derived mileage remains available in reports and exports, while all new trips use odometer readings.
