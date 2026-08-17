# Changelog

## 2.5.0

- Added a private, database-backed Business Profile for invoice business name, address, phone, email, payment instructions, thank-you message, and an optional PNG or JPEG logo.
- Removed personal contact and payment details from distributable application files.
- Added a validated SQLite database import flow with an automatic pre-import backup and recovery safeguards.
- Changed the Home Assistant slug to `redbeard_time_tracker`, removing the old test/modernization hostname.
- Documented the side-by-side migration from the old local app so its database remains recoverable until verification is complete.
- Documented private GHCR image pulls using Home Assistant registry credentials and a public metadata-only repository.
- Added complete user and technical-support guides covering every workflow, system requirements, troubleshooting, diagnostics, backup, and recovery.
- Fixed PDF invoice rows so long customer, project, and trip-charge descriptions expand vertically without overlapping their detail text.

## 2.4.0

- Added prebuilt `aarch64` and `amd64` Home Assistant images so updates no longer compile the app on the Home Assistant device.
- Added a metadata-only Home Assistant repository bundle for future repository-source installs.
- Added versioned release notes that Home Assistant can display in its update dialog.
- Added automated release checks to keep the app, package, documentation, and changelog versions aligned.
- Kept the existing compatibility slug and database location for local-source upgrades.
- No database schema or application-data changes are included in this release.

## 2.3.2

- Replaced long customer, project, and linked-work dropdowns with searchable pickers.
- Improved selection behavior on desktop and mobile.

## 2.3.1

- Made PDF invoices inherit all customers and projects selected in the report filters.
- Added support for multiple payments with an independently recorded received date.

## 2.3.0

- Added customer and project search, select-all, bulk archive, and bulk restore tools.
- Added branded PDF invoice generation from reports.
- Added optional trip charges, separate from labor, using a $45 base plus $1 per mile over 25 miles.
- Improved the mobile quick-add menu and add-time workflow.

## 2.2.0

- Switched new mileage tracking to start and end odometer readings without location monitoring.
- Improved one-press form submission and trip-stop reliability.
- Preserved historical route-derived mileage in reports and exports.
