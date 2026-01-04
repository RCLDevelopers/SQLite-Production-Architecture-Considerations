
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

### 1. Concurrency & Locking

SQLite uses file-level locking. High-traffic periods (e.g., enrollment deadlines) can trigger `SQLITE_BUSY` errors if multiple writes happen simultaneously.

* **Laravel Optimization:** We recommend enabling **Write-Ahead Logging (WAL)** mode to allow concurrent reads during write operations.

### 2. Infrastructure Requirements

* **Persistent Storage:** **Do not host on Ephemeral filesystems** (like Heroku or Vercel). Deployment restarts will wipe the `.sqlite` file and all student data. Use a VPS (DigitalOcean, Linode, AWS EC2) with persistent SSD storage.
* **Disk Type:** Avoid Network Attached Storage (NAS). Use local SSDs to ensure ACID compliance and prevent file corruption during payment processing.

### 3. Security & Scaling

* **File Permissions:** Ensure the database file is restricted (e.g., `chmod 664`) so it is not accessible to unauthorized system users.
* **Scaling Limit:** This system is designed to **Scale Up** (more powerful CPU/RAM). If your school requires a multi-server load-balanced setup (**Scaling Out**), a migration to MySQL/PostgreSQL is required.


## 📊 Feature Comparison: SQLite vs. MySQL

| Feature | SQLite (Current) | MySQL/PostgreSQL |
| --- | --- | --- |
| **Setup** | Zero configuration | Requires managed service/setup |
| **Enrollment** | Ideal for low/medium volume | Best for high-concurrency "rush" |
| **Payments** | File-level locking | Row-level locking (Higher throughput) |
| **Support Tickets** | Efficient for text | Better for large blobs/complex searches |
| **Backups** | Simple file copy | Standardized `mysqldump` |


## 🛠 Modules Included

* **Course Management:** Schedule and manage lessons.
* **Enrollment System:** Student registration and onboarding.
* **Payment Processing:** Integrated transactional handling.
* **User Management:** Role-based access for instructors and students.
* **Support Tickets:** Internal module for handling student inquiries.

## 📝 Created by CTO
[Zangtics Digital](https://zangticsdigital.com/).

## 📝 License

This project is open-sourced software licensed under the [MIT license](https://opensource.org/licenses/MIT).

