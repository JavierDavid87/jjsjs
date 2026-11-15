# BayanAlert

BayanAlert is a community complaint and hazard reporting system for Barangay Talon, Amadeo, built with Laravel 12.

## Main features

- Resident registration with home address and a valid ID photo
- Resident dashboard with total and monthly report counts
- Complaint type and dependent specific-complaint dropdowns, with a description field for Other
- Urgency assigned by the server from complaint type and specific complaint; residents cannot select it
- Fire and natural hazards can be reported through complaint categories without a separate emergency toggle
- Dropdowns for Amadeo municipality, its barangays, and configured landmarks, with an Other option
- Up to four private report-photo attachments
- Report history with start/end date filters, case groups, and filing/status timestamps
- Admin archive with monthly totals for every month, filtered CSV export, and active/completed/resident-cancelled case groups
- Admin-only viewing of submitted ID photos and reporter addresses
- Responsive Blade pages and local CSS

## Run locally in VS Code

Open the BayanAlert folder in VS Code. In a PowerShell terminal in that folder, run:

~~~powershell
& 'C:\xampp\php\php.exe' artisan package:discover
& 'C:\xampp\php\php.exe' artisan migrate --seed
& 'C:\xampp\php\php.exe' artisan serve
~~~

Open http://127.0.0.1:8000.

If PHP is already on your PATH, replace the XAMPP executable path with php.

## Prepared sign-in accounts

| Role | Email | Password |
| --- | --- | --- |
| Resident | resident@bayanalert.test | bayanalert123 |
| Admin | admin@bayanalert.test | bayanalert123 |

New registrations always receive the resident role. Admin accounts must be provisioned separately.

## Password changes and recovery

Signed-in residents and staff can change their password from Profile & Settings by entering their current password and confirming a new password of at least 8 characters. On the sign-in page, Forgot your password? starts an email reset flow. Reset links expire after 60 minutes and requests are rate limited.

For this local setup, `MAIL_MAILER=log` writes reset emails to `storage/logs/laravel.log` instead of sending them to an inbox. Request a reset, open the log, and copy the generated reset URL into your browser. Configure SMTP mail settings in `.env` to deliver links by email. The existing `password_reset_tokens` table supports this flow, so no database migration is needed.

## Reports and status

Reports start at Awaiting review. Staff can set them to In progress, Resolved, or Cancelled. Residents can cancel their own complaint while it is awaiting review or in progress. Each status change or withdrawal is written to the report history.

The admin archive retains all reports and lists monthly submission totals across the full archive. In-progress cases include Awaiting review and In progress reports; completed cases are resolved reports; cancelled-by-resident cases are tracked separately from staff cancellations. Residents and staff can filter report lists by start and end dates. Staff can export the matching archive as CSV. Filing time and each status update time are recorded; resident withdrawals are flagged in the archive.

Residents choose a complaint type, then a specific complaint from options configured in `config/bayanalert.php`. Choosing Other opens a text box to describe the issue. The server assigns Low, Medium, or Urgent from the type and specific complaint mapping in that same config file; the form has no priority selector. Accidents, active threats, fires, and natural hazards are marked Urgent. No separate emergency toggle is used.

## Location lists

The Amadeo municipality and barangay options are configured in config/bayanalert.php. The barangay list follows the Philippine Statistics Authority PSGC listing for Amadeo. The landmark dropdown currently uses the two locations already recorded in the project; an Other option lets residents enter another landmark. Edit the config file to change these lists.

## Database and uploaded files

The app uses SQLite at database/database.sqlite. Laravel migrations define the SQL tables. Uploaded report photos and resident ID photos are stored under Laravel's private local storage; their paths are saved in the database. ID photos are served only through an admin-only route.

Later migrations add resident address and ID-photo paths, convert old closed report statuses to cancelled, and record which reports were cancelled by residents.

## Personal data and scope

- The ID upload accepts JPG, PNG, or WebP images up to 10 MB. Staff can view the image, but the app does not automatically verify identity.
- Anonymous display hides a resident name in the report queue. Authorized staff can still see the account for follow-up.
- The text-update checkbox stores a preference; no SMS provider is connected.
- The app records reports for staff review; it does not dispatch emergency responders.
- Emergency contact numbers are not configured.
- Location is stored as entered text. There is no GPS or interactive map.
- The application is configured for local use. A public deployment needs its own server and production configuration.

## Project folders

- app/Http/Controllers — request handling and report logic
- app/Http/Middleware — admin access control
- app/Models — users, reports, photos, and status updates
- database/migrations — database structure and upgrades
- database/seeders — prepared accounts and report records
- resources/views — Blade page templates
- public/css/bayanalert.css — application styles
- storage/app/private — private uploads
