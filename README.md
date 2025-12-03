# pfSense Authentication Monitoring System

A lightweight system for monitoring authentication events on pfSense firewalls with email notifications, including SSH connections and SSHGuard blocking events.

> **Note:** This is a fork of [ngfblog/pfLoginTracker](https://github.com/ngfblog/pfLoginTracker).
>
> **Changes in this fork:**
> *   Removed Gotify support (email only).
> *   Added monitoring for OpenVPN logins (success and failure).
> *   Updated log parsing to support RFC 5424 / RFC 3339 timestamp formats (including year).
> *   Fixed duplicate email notifications.

## Overview

This project provides two shell scripts that work together to:

1. Monitor the pfSense authentication log file (`/var/log/auth.log`) for successful and failed login attempts
2. Track SSH connections and SSHGuard blocking activities
3. Send notifications via:
   - Email (using pfSense's built-in notification system)

## Features

- Track successful and failed login attempts
- Monitor SSH connections to your pfSense system
- Receive alerts when SSHGuard blocks suspicious IP addresses
- Send email notifications using your pfSense SMTP settings
- Keep track of processed log entries to avoid duplicate notifications
- Configurable for your environment

## Installation

### Prerequisites

- A pfSense firewall with shell access
- SMTP configuration set up in pfSense System > Advanced > Notifications

### Setup

> **Tip:** You can execute the shell commands listed below either via SSH or by using the **Diagnostics > Command Prompt** feature in the pfSense WebGUI.

1. Create a directory for the scripts:
   ```
   mkdir -p /root/Scripts
   ```

2. Create the `check_pfsense_login.sh` script:
   ```
   vi /root/Scripts/check_pfsense_login.sh
   ```
   Copy the contents from the file in this repository

3. Create the `email_auth_alert.sh` script:
   ```
   vi /root/Scripts/email_auth_alert.sh
   ```
   Copy the contents from the file in this repository

4. Make both scripts executable:
   ```
   chmod +x /root/Scripts/check_pfsense_login.sh
   chmod +x /root/Scripts/email_auth_alert.sh
   ```

5. Install the `Cron` package via **System > Package Manager** if it is not already installed. Then set up a cron job to run the monitoring script periodically. Add the following to **Services > Cron**:
   - Command: `/root/Scripts/check_pfsense_login.sh`
   - Schedule: `*/5 * * * *` (runs every 5 minutes)

## Configuration

### Email Configuration

The script uses pfSense's built-in notification system, so make sure your SMTP settings are correctly configured in pfSense at:

System > Advanced > Notifications > E-Mail

## How It Works

1. `check_pfsense_login.sh` scans the auth.log file for new entries since the last check
2. The script detects different types of events:
   - Standard authentication successes and failures
   - SSH connection attempts
   - SSHGuard blocking actions
3. When it finds an event, it extracts the relevant information (username, IP address)
4. It calls `email_auth_alert.sh` with these details
5. `email_auth_alert.sh` sends notifications to email

## Alert Types

The system now monitors and alerts on:

- **Authentication Success**: Successful logins to the pfSense web interface
- **Authentication Failure**: Failed login attempts to the pfSense web interface
- **SSH Connection**: When someone connects to your pfSense system via SSH
- **SSHGuard Block**: When SSHGuard detects and blocks suspicious IP addresses

## Customizing

You can customize the scripts to:

- Change notification priorities
- Add geo-location information for IP addresses
- Filter out specific users or IP addresses
- Adjust the notification format

## Testing

You can manually test the alert script to verify that email notifications are working correctly. Run the following command via SSH or in **Diagnostics > Command Prompt**:

```bash
/root/Scripts/email_auth_alert.sh "testuser" "127.0.0.1" "Test Event" "Test Service"
```

## Troubleshooting

Check the system logs for error messages:

```
tail -f /var/log/system.log | grep pfsense_auth_alert
```

## License

MIT License - See LICENSE file for details.

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## 🧡 Like it?

Give the project a ⭐ on GitHub and spread the word!
