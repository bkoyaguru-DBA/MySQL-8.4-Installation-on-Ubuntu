# MySQL-8.4-Installation-on-Ubuntu
# Install MySQL 8.4 on Ubuntu/Debian (APT method)

This guide installs MySQL Community Server 8.4 on Ubuntu/Debian using Oracle's official APT repository.

> **Note:** This uses the `.deb` / APT method (`dpkg`, `apt`), not RPM. RPM is used on RHEL/CentOS/Fedora/Rocky Linux with `rpm`/`yum`/`dnf` instead.

## Prerequisites

- Ubuntu or Debian system
- `sudo` / root access
- Internet access to `dev.mysql.com` and `repo.mysql.com`

## 1. Download the MySQL APT config package

This package adds Oracle's official MySQL repositories to your system so you can install and update MySQL via `apt`.

Go to the MySQL Community Downloads page and select **Ubuntu Linux** as the OS (the APT repository also supports Debian):

![MySQL Community Downloads page - selecting Ubuntu Linux and Install Using APT](https://github.com/bkoyaguru-DBA/MySQL-8.4-Installation-on-Ubuntu/blob/4e2bed4ed4f750d1f78fa276bab7464f7e2a018e/mysql-download-page.png)

Click through to the APT repository setup page and download the DEB package:

![MySQL APT Repository - download mysql-apt-config .deb package](images/apt-config-download.png)

You may be prompted to log in or sign up for an Oracle account. You don't need an account — click **"No thanks, just start my download"** at the bottom of the page.

![Oracle account login prompt - skip via "No thanks, just start my download"](images/oracle-login-prompt.png)

Or skip the browser entirely and download directly from the terminal:

```bash
wget https://dev.mysql.com/get/mysql-apt-config_0.8.39-1_all.deb
```

## 2. Install the repository config package

```bash
sudo dpkg -i mysql-apt-config_0.8.39-1_all.deb
```

During this step you'll get an interactive menu to pick which MySQL product/version repository to enable. Confirm `mysql-8.4-lts` is selected under **MySQL Server & Cluster**, then choose **Ok**.

![MySQL APT config package selection screen](images/dpkg-install-output.png)

## 3. Refresh the package list

Required after adding a new repository.

```bash
sudo apt update
```

## 4. Install MySQL Server

```bash
sudo apt install mysql-community-server
```

- Confirm with `Y` when prompted.
- You'll be asked to set the **root password** during installation — set a strong password here. (This step is easy to click through without noticing; don't skip it.)

## 5. Verify the service is running

```bash
sudo systemctl status mysql
```

You should see `Active: active (running)`.

## 6. Enable MySQL to start on boot

Not covered in a plain install — without this, MySQL won't restart automatically after a server reboot.

```bash
sudo systemctl enable mysql
```

## 7. Secure the installation

Run this before using the server for anything real. It lets you confirm the root password, remove anonymous users, disable remote root login, and remove the test database.

```bash
sudo mysql_secure_installation
```

## 8. Log in and verify the version

```bash
mysql -u root -p
```

Inside the MySQL prompt:

```sql
\s
```

Confirm the server version reports `8.4.x`.

## 9. (Optional) Allow remote connections

By default MySQL only listens on `localhost`. If you need remote access:

1. Edit the config file (path varies; typically `/etc/mysql/mysql.conf.d/mysqld.cnf`) and change `bind-address` to `0.0.0.0` or your server's IP.
2. Restart MySQL: `sudo systemctl restart mysql`
3. Open the firewall port: `sudo ufw allow 3306/tcp`
4. Create a MySQL user allowed to connect from a remote host and grant privileges — don't just open root to `%` (any host).

## Uninstalling

```bash
sudo apt remove --purge mysql-community-server mysql-community-client mysql-community-client-core mysql-community-client-plugins mysql-common
sudo apt autoremove
sudo rm -rf /etc/mysql /var/lib/mysql
```

## Troubleshooting

| Symptom | Check |
|---|---|
| `systemctl status mysql` shows failed | `sudo journalctl -xeu mysql.service` |
| Can't connect with password | Confirm you set it correctly in step 4, or reset via `mysql_secure_installation` |
| `apt install` can't find `mysql-community-server` | Re-run `sudo apt update`, confirm step 2 completed without error |
