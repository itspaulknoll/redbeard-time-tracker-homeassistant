# Redbeard Time Tracker User Guide

This guide applies to Redbeard Time Tracker 2.5.1. It covers the Home Assistant, Docker, and standalone versions; the features are the same in each installation.

## What the app tracks

Redbeard Time Tracker organizes work into four related record types:

- **Customers** identify who the work is for and can carry a default hourly rate.
- **Projects** belong to a customer and can override the customer's default rate.
- **Time entries** record completed work, hours, notes, billing status, and an optional project.
- **Mileage trips** record business purpose, vehicle, distance, and an optional trip charge. A trip can be linked to its related time entry.

The app stores these records, invoice settings, and an optional invoice logo in one SQLite database.

## First-time setup

### 1. Complete the Business Profile

Open **Settings → Business profile** and enter:

- Business name
- Mailing address, phone, and email as desired
- Payment instructions, such as how a customer should pay
- An optional invoice thank-you message
- An optional PNG or JPEG invoice logo, up to 2 MB

Business name and payment instructions are required before the app can create a PDF invoice. The profile and custom logo are private data stored in the SQLite database; they are also included in database backups.

When the business name is Redbeard Solutions and no custom logo is stored, the bundled Redbeard logo is used. Other business names can upload their own logo.

### 2. Add customers and projects

Open **Customers** on desktop, or **More → Customers & projects** on mobile.

1. Select **Add customer**.
2. Enter the customer name and, optionally, a default hourly rate.
3. Save the customer.
4. Use **Add project** on the customer card to create its projects.

A project's default rate takes precedence over the customer's default. You can still override the rate on an individual time entry or timer.

### 3. Download an initial backup

Open **Settings → Download backup** and save the `.db` file somewhere protected. Repeat this before upgrades, database imports, or large bulk changes.

## Navigation

The desktop navigation contains **Time**, **Mileage**, **Reports**, **Customers**, and **Settings**.

On mobile:

- **Time**, **Miles**, and **Reports** are in the bottom navigation.
- The center **+** button opens Add Time or Start Timer.
- **More** opens Customers & projects and Settings.
- The header **Timer** button opens the guided timer and shows when a timer is active.

## Customers and projects

### Search

The customer screen searches customer and project names as you type. Searchable customer and project selectors are also used throughout the app, so you can type part of a name instead of scrolling through a long list.

### Rates

Rates are selected in this order:

1. Rate entered on the time entry or timer
2. Project default rate
3. Customer default rate

An entry without a rate can still be saved, but its calculated billable amount is $0 until a rate is added.

### Archive and restore

Archiving hides customers or projects from normal selection without deleting their historical work. Archiving a customer also archives its projects.

To archive several records:

1. Search or filter the customer screen if needed.
2. Select individual customers/projects, or choose **Select all visible**.
3. Choose **Archive selected**.

Enable **Show archived** to find archived records, select them, and choose **Restore selected**.

## Time entries

### Add completed time

1. Open **Time** and select **Add time**. On mobile, use the center **+** button and choose **Add time**.
2. Choose a customer and optional project.
3. If the customer or project does not exist, use **New customer** or **New project** inside the same form.
4. Enter the date/time and hours. The 15m, 30m, 1h, and 2h buttons provide quick values.
5. Add useful work notes.
6. Choose whether the work is billable. For billable work, confirm the hourly rate and billing status.
7. Select **Save entry**.

Select **Log mileage after saving this entry** when the work also has a trip. The mileage form opens after the time entry is saved and links the two records.

### Edit, delete, and restore

Use the action buttons on a time row or mobile card to edit or delete it. Deleting a time entry is a soft deletion; use the on-screen **Undo** action immediately if it was removed by mistake.

Editing an existing time entry also shows its linked mileage and provides **Log linked mileage**.

### Find time

The Time screen can filter by:

- Text in customer, project, or notes
- Customer
- Project
- Billing status
- From and to dates
- Newest or oldest first

Use **Clear filters** to return to the complete active work log. The row selector controls how many records appear on each page.

### Bulk billing status

Use the selection checkbox to select the current page. When more matching records exist, **Select all matching entries** applies the selection to the complete filtered result, not just the visible page.

Choose the new status and select **Apply**. Clear the selection when finished. Always confirm the current filters before using an all-matching change.

## Live timer

The guided Start Work flow is useful when beginning work now.

1. Select **Start timer** in the header, or use mobile **+ → Start timer**.
2. Choose or create the customer.
3. Choose or create an optional project.
4. Add notes, billable status, and rate.
5. Optionally choose **Also start a business mileage trip** and supply the starting odometer details.
6. Select **Start work**.

The running timer is stored by the server, so closing the dialog or browser does not stop it. Only one timer can run at a time.

Select the timer again and choose **Stop & save** to create a time entry. If mileage was started with the timer, the saved time entry is linked to that trip. Complete the mileage trip separately with its ending odometer. Choose **Discard timer** only when the elapsed work should not be saved.

## Mileage log

The app does not use GPS or request location permission. New trips use odometer readings or a manually entered distance.

### Start and complete a current trip

1. Open **Mileage** and select **Start mileage trip**.
2. Choose the customer, optional project, and optional related work entry.
3. Enter the business purpose, starting place, starting odometer, and vehicle.
4. Optionally enable the billable trip charge.
5. Select **Start trip**.
6. At the destination, select **Stop trip**.
7. Enter the destination and ending odometer, then select **Complete trip**.

The ending odometer must be at least the starting odometer. The app calculates business miles from the difference.

Use **Discard** only when the active trip should be removed instead of completed.

### Log a past trip

Select **Log past trip**, then enter the customer, dates, business purpose, locations, and vehicle. Choose one distance method:

- **Use odometer readings** requires both starting and ending odometers.
- **Enter business miles** accepts the known business distance directly.

You can link the trip to an existing work entry. Historical records that were once route-derived remain readable and exportable as legacy records, but new GPS/route tracking is not available.

### Edit a trip

Edit a completed trip to correct its work link, purpose, locations, vehicle, odometer readings or manual distance, notes, trip-charge setting, and trip-charge billing status. Existing legacy distance records retain their recorded distance unless deliberately changed to a supported method.

### Yearly vehicle odometer

Choose a tax year and record the vehicle's start-of-year and end-of-year odometer values. This provides a year-end reconciliation record alongside the individual business trips.

### Mileage filters and export

Filter mileage by tax year, customer, or project. **Export mileage CSV** downloads the completed trips and includes distance source, odometers, trip-charge details, and linked-work information.

Mileage records are organizational tools, not tax advice. Confirm recordkeeping and deductions with a qualified tax professional.

## Trip charges

A trip charge is separate from labor. Enable **Add billable trip charge** on a mileage trip when it should be billed.

The current formula is:

`$45.00 base charge + $1.00 for each business mile over 25 miles`

Examples:

| Business miles | Trip charge |
| ---: | ---: |
| 12 | $45.00 |
| 25 | $45.00 |
| 40 | $60.00 |

Only completed trips with the option enabled appear as trip charges in reports and invoices. Each trip charge has its own **Unbilled**, **Invoiced**, or **Paid** status; changing a linked time entry's status does not change the trip charge automatically.

## Billing statuses

Billable time entries and trip charges use these statuses:

- **Unbilled** — ready for review or a future invoice
- **Invoiced** — included in an invoice or otherwise billed
- **Paid** — payment has been received

Non-billable time uses **Non-billable** and does not contribute to billable totals or PDF invoices.

Creating or downloading a PDF invoice does **not** automatically change any statuses. After reviewing the invoice, mark the matching time entries and trip charges Invoiced. Mark them Paid when payment is received.

## Reports and exports

### Preview a report

Open **Reports**, choose any combination of customer, project, billing status, and date range, then select **Preview report**.

The preview shows:

- Total hours
- Labor charges
- Trip charges
- Combined billable total
- Customer and project totals

Leaving customer or project blank includes all matching customers/projects. The current filters also define the invoice contents.

### CSV exports

- **Detailed CSV** contains one row per matching time entry, including notes, hours, rate, amount, and status.
- **Summary CSV** groups time by customer and project.
- **Mileage CSV** is downloaded from the Mileage screen and includes the mileage and trip-charge fields.

CSV exports protect leading spreadsheet formula characters in text fields. Review imported column types and date formatting in your spreadsheet program.

### Create a PDF invoice

1. Complete the Business Profile in Settings.
2. Apply the desired report filters and preview the report.
3. Select **PDF invoice**.
4. Confirm the invoice number, invoice date, and billing period.
5. Review the automatically determined customer scope. All customers matching the report filters are included; a separate customer selection is not required.
6. Enter or review the bill-to details.
7. Add a previous balance if applicable.
8. Use **Add payment** for each payment already received and enter its received date and amount.
9. Review payment instructions and optional invoice notes.
10. Select **Download PDF**.

The invoice itemizes matching billable labor and completed trip charges, shows labor and trip subtotals separately, subtracts dated payments, and calculates the amount due. A combined-customer invoice identifies customers in its line descriptions.

## Settings, backups, and database import

### Installation information

Settings displays the running version, protection mode, database filename and size, entry count, and customer/project counts. These values are useful when verifying an upgrade or preparing a support request.

### Download a backup

Select **Download backup** to create a consistent SQLite snapshot. Store it somewhere protected because it can contain customer, billing, mileage, contact, and payment information.

Recommended times to back up:

- Before every upgrade
- Before importing another database
- Before a large archive or billing-status operation
- After verifying a successful migration

### Import a database

Database import replaces the active database in the installation where it is performed.

1. Download and retain a backup of the current installation.
2. Open **Settings → Import database**.
3. Choose a Time Tracker `.db`, `.sqlite`, or `.sqlite3` backup.
4. Check the replacement confirmation.
5. Select **Validate and import** and confirm the browser prompt.
6. Wait for validation and the automatic page reload.
7. Compare the counts in Settings and inspect representative time, mileage, and invoice records.

The app verifies the SQLite header and integrity, applies schema migrations to the candidate, and keeps the previous active database as a dated pre-import backup. If activation fails, it attempts to restore the previous database automatically.

Do not upload an encrypted Home Assistant full-backup archive. Use a `.db` file downloaded from Time Tracker.

## Data and privacy notes

- Everyone who can open the same installation can see and modify its Time Tracker data; the app does not provide per-user roles.
- Home Assistant installations rely on Home Assistant ingress authentication.
- Standalone installations use the configured application username and password.
- The database is not synchronized to a cloud service by the app.
- Do not send database backups, passwords, or registry tokens in ordinary support messages.

For installation requirements, diagnostics, and solutions to common problems, see [Technical Support](SUPPORT.md).
