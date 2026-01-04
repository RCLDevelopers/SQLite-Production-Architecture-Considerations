
# SQLite Production Architecture Considerations

## 🚀 Quick Start
This project uses SQLite for rapid development and zero-configuration setup.

```bash
# Install dependencies
composer install
npm install && npm run build

# Setup database
touch database/database.sqlite
php artisan migrate --seed

# Start the server
php artisan serve

```

## ⚠️ Production Architecture: SQLite Considerations

While SQLite offers incredible speed and simplicity for development, moving a transactional system (Payments & Enrollments) to production requires specific architectural awareness.

Here is the content converted into a professional, structured `README.md` file suitable for a GitHub repository.


## 🚀 Architecture Overview

Using Laravel with SQLite offers a streamlined development experience and rapid deployment. However, because this application moves beyond a "read-only" system into a "write-heavy" transactional environment (handling payments and enrollments), there are specific production considerations to keep in mind.

## ⚠️ Production Challenges & Considerations

### 1. Database Locking and Concurrency

SQLite is designed for single-user write access. If two students attempt to enroll or pay at the exact same millisecond, SQLite locks the entire database file for the first write.

* **The Challenge:** The second user may receive a `SQLITE_BUSY` error or experience a timeout as the system grows.
* **Laravel Fix:** Enable **Write-Ahead Logging (WAL)** mode in your database configuration. This allows multiple users to read from the database even while a write operation is in progress.

### 2. Ephemeral Filesystems (Hosting Risks)

Modern PaaS providers (Heroku, Vercel, Laravel Cloud) use ephemeral storage.

* **The Challenge:** Every time you deploy code or the server restarts, the local filesystem is wiped. Since SQLite is a file within the project, all student data and payment records will be lost.
* **Recommendation:** Use a **traditional VPS** (DigitalOcean, Linode) with persistent storage, or switch to a managed MySQL/PostgreSQL instance if using a PaaS.

### 3. Payment Integrity and ACID Compliance

Payment processing requires 100% data integrity to prevent record corruption during power loss or crashes.

* **The Challenge:** SQLite's file locking can fail if the hosting provider uses Network Attached Storage (NAS).
* **Tip:** Always ensure your `.sqlite` file is stored on a **local SSD**, not a network-mounted drive.

### 4. Security & User Permissions

SQLite does not have internal "database users" like MySQL.

* **The Challenge:** Anyone with filesystem access can download the `.sqlite` file and view sensitive student info.
* **Security Step:** Strictly manage file permissions (e.g., `chmod 664`) to ensure only the web server user (e.g., `www-data`) can access the file.

### 5. Scaling Limitations

As modules like Support Tickets and Course Management grow, you may hit a "scaling wall."

* **The Challenge:** SQLite is a "Scale Up" solution. If you move to a multi-server load-balanced setup, Server A will not see updates made on Server B because the database is a local file.

## 📝 Created by CTO
[Zangtics Digital](https://zangticsdigital.com/).

## 📝 License

This project is open-sourced software licensed under the [MIT license](https://opensource.org/licenses/MIT).

