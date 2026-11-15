BAYANALERT
Community Complaint and Hazard Reporting System
Barangay Talon, Amadeo

OVERVIEW
BayanAlert lets residents submit community complaints and hazard reports and check their status. Barangay staff can review reports, add updates, and track filed cases.

TECHNOLOGY
- Laravel 12 and PHP 8.2
- SQLite database
- Blade web pages and local CSS
- Laravel Eloquent models and migrations

RUN LOCALLY IN VS CODE
Open the BayanAlert folder. Open a PowerShell terminal in that folder (the folder containing artisan) and run:

& 'C:\xampp\php\php.exe' artisan package:discover
& 'C:\xampp\php\php.exe' artisan migrate --seed
& 'C:\xampp\php\php.exe' artisan serve

Then visit http://127.0.0.1:8000.

PREPARED SIGN-IN ACCOUNTS
Resident: resident@bayanalert.test / bayanalert123
Admin: admin@bayanalert.test / bayanalert123
New registrations are residents. Admin accounts are provisioned separately.

PASSWORD CHANGES AND RECOVERY
Residents and staff can change their password in Profile & Settings after entering their current password. New passwords must be at least 8 characters and be confirmed.
On the sign-in page, choose Forgot your password? and enter the account email. Reset links expire after 60 minutes and requests are rate limited.
This local setup uses MAIL_MAILER=log, so reset emails are written to storage/logs/laravel.log instead of being delivered to an inbox. Request a reset and copy its URL from that log into your browser. To send real email, configure SMTP mail settings in .env. The password_reset_tokens table already exists; no migration is needed.

REPORTING
Residents choose a complaint type, then a specific complaint from a dependent list, enter the incident and location, and may attach up to four report photos. Choosing Other opens a text box to describe the issue. Urgency is assigned by the server from the complaint type and specific complaint; residents cannot select priority. Accidents, active threats, fires, flooding, and natural hazards are marked Urgent. Fire and natural hazards are reported through the complaint type list; there is no separate emergency toggle. Edit config/bayanalert.php to update complaint choices and urgency mappings.

Locations are selected from dropdown lists. The configured municipality is Amadeo, Cavite; the barangay dropdown contains the PSGC list for Amadeo. Landmark options are currently Near the covered court and Near the basketball court, with an Other field for another location. Edit config/bayanalert.php to update these options.

Reports start as Awaiting review. Staff can mark them In progress, Resolved, or Cancelled. A resident can cancel their own complaint while it is Awaiting review or In Progress. Each change is added to report history with a timestamp.

REPORT PROGRESS AND ARCHIVE
The admin archive retains all reports and displays monthly submission totals for every month with reports. In-progress cases include Awaiting review and In Progress reports. Completed cases are resolved reports. Cancelled-by-resident cases are tracked separately from staff cancellations. Residents and staff can filter report lists by start and end date. Staff can export filtered records to CSV for offline reference. Filing time and last status update time appear in report lists; full status history and timestamps appear in each report detail.

REGISTRATION AND ID PHOTOS
Registration requires a home address and a photo of a valid government ID. Accepted ID images are JPG, PNG, or WebP up to 10 MB. Uploaded ID photos are saved in Laravel private storage. Only an administrator can view them from a resident's report details. The system stores the document but does not automatically verify identity. Residents can replace their ID photo in Profile & Settings.

DATABASE
SQLite file: database/database.sqlite
Main tables: users, reports, report_photos, report_updates.
The reports table records whether a cancelled case was withdrawn by its resident. The report_updates table preserves status history and timestamps.
Report photo and ID photo files live in Laravel private local storage; the database keeps their file paths and metadata.

CURRENT SCOPE
- The text-update checkbox stores a preference. No SMS provider is connected.
- BayanAlert does not dispatch emergency responders.
- Emergency contact numbers are not configured.
- There is no GPS map or announcements page in this implementation.
- The application is set up for local use.

PROJECT FILES
- app/Http/Controllers: authentication, dashboard, report, profile, and admin logic
- app/Http/Middleware/AdminOnly.php: admin access control
- app/Models: database models
- routes/web.php: web routes and access rules
- database/migrations: schema creation and upgrades
- database/seeders/DatabaseSeeder.php: initial accounts and records
- resources/views: Blade pages
- public/css/bayanalert.css: app styles
- storage/app/private: private uploaded photos
