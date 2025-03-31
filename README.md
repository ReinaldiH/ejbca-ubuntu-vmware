# Installing EJBCA on Ubuntu Server 22.04 LTS in VMWare Fusion

In this guide, I will install EJBCA using Ubuntu Server 22.04 LTS in VMWare Fusion. Of course, you can use other Linux operating systems as well.

## Prerequisites
Before installing EJBCA on the server, you must install the necessary supporting packages.

### Install Required Packages
To ensure a smooth installation process, follow these steps:

1. **Update all OS packages**:
   ```bash
   sudo apt update && sudo apt upgrade -y
   ```
![EJBCA Installation](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*D3LpYcdE7GbSbb7U8Fy4qQ.png)

2. **Install additional dependencies like curl, ant, ant-optional, psmisc, bc, patch, and unzip**:
   ```bash
   sudo apt install curl ant ant-optional psmisc bc patch unzip
   ```
![EJBCA Installation](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*3VRpDtWdvB1mOpXf1Kp7OQ.png)

3. **Install OpenJDK version 8 (Make sure it's version 8, not a higher version)**:
   ```bash
   sudo apt install openjdk-8-jdk
   ```
![EJBCA Installation](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*o0QYR7zspWAwU2nLZwv04w.png)
   
4. **Install MariaDB server and client as the database engine for EJBCA**:
   ```bash
   sudo apt install mariadb-client
   sudo apt install mariadb-server
   ```
![EJBCA Installation - Step 4](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*Vrhtla3ksgbYx0JFJIpLqw.png)

![EJBCA Installation - Step 5](https://miro.medium.com/v2/resize:fit:1400/format/webp/1*4VFZM4oIpU3u-MtfM-pCmQ.png)


   To check the MariaDB status:
   ```bash
   sudo systemctl status mariadb
   ```
![EJBCA Installation - Step 6](https://miro.medium.com/v2/resize:fit:1400/format/webp/1*ZkAPUXf1aU-DolFlzjr6jQ.png)

## Creating a Service Account
For security reasons, it is recommended to run applications under a non-root user. You can create a user and set a password as follows:
```bash
useradd reinaldi
passwd reinaldi
```

Deploy the PKI and its subsystems in the `/opt/` directory by modifying the new user's home directory and shell:
```bash
usermod -d /opt reinaldi
chown -R reinaldi: /opt
chmod 700 -R /opt
chsh -s /bin/bash reinaldi
exit
```

![EJBCA Installation - Step 7](https://miro.medium.com/v2/resize:fit:1400/format/webp/1*SAhnIszohcAOgr4IZh4VPA.png)

## Database Configuration
After installation, secure your database using MySQL configuration:
```bash
sudo mysql_secure_installation
```

![EJBCA Installation - Step 8](https://miro.medium.com/v2/resize:fit:1400/format/webp/1*8bfNyXgpMYb6tZKbpyS0QA.png)

EJBCA requires a MySQL (MariaDB) database. To log in as the root user:
```bash
mysql -u root -p
```
![EJBCA Installation - Step 9](https://miro.medium.com/v2/resize:fit:1400/format/webp/1*GqkJMQgifPp4glvas1eU5w.png)

Create a database, user, and password for EJBCA:
```sql
CREATE DATABASE ejbca CHARACTER SET utf8 COLLATE utf8_general_ci;
GRANT ALL PRIVILEGES ON ejbca.* TO 'ejbca'@'localhost' IDENTIFIED BY 'my-password';
FLUSH PRIVILEGES;
exit;
```
*Note: Replace `my-password` with a secure password of your choice.*

![EJBCA Installation - Step 10](https://miro.medium.com/v2/resize:fit:1400/format/webp/1*aTBc-ScFc1r4ayb72drWqw.png)

![EJBCA Installation - Step 11](https://miro.medium.com/v2/resize:fit:1184/format/webp/1*EQqoG-r2DfU-AUsNQaGGUQ.png)


## Download and Install EJBCA
Next, download the EJBCA package:
```bash
sudo wget https://sourceforge.net/projects/ejbca/files/ejbca7/ejbca_ce_7_4_3_2.zip
```
Rename the package for convenience.

Then, edit the EJBCA setup script:
```bash
sudo nano /opt/ejbca/bin/extra/ejbca-setup.sh
```
Modify the following values:
```bash
# Server name (the URL you will use to access the PKI):
httpsserver_hostname="root-ca.coom.lan"
# MariaDB server IP (do not modify if following this guide):
database_host="localhost"
# Database name
database_name="ejbca"
# Database username
database_username="ejbca"
# Database password
database_password="123"
# Distinguished Name of the Management PKI
BASE_DN="O=I-Avenger Studio,C=IDN"
```
*Note: Adjust the database name, password, base DN, and database host as needed.*

After making changes, save the file and execute the setup script:
```bash
cd /opt
/opt/ejbca/bin/extra/ejbca-setup.sh
```
The installation will begin. You will need to confirm by typing "YES" for 1 to 3 questions.

If the installation is successful, you should see the following message at the end:
```bash
*******************************
* SUCCESS *
********************************
You can now install the superadmin.p12 keystore, from /opt/ejbca/p12, in your web browser, using the password 531adeac084a9b08b9881f2b9c367561e3eac4fd
```

## Importing the P12 Certificate
Move the `p12` file from the host server to your local directory using Termius.

Then, import the `p12` file into Mozilla Firefox:
1. Open **Settings** > **Privacy & Security** > **Certificates**.
2. Click **View Certificates** > **Import**.
3. Select the `p12` file that was moved to your local directory.
4. Enter the **superadmin.p12 password** from the installation log.

## Accessing the EJBCA Web Interface
To access the EJBCA web interface, enter your server's IP address in your browser.

If you see a security warning:
1. Click **Advanced**.
2. Accept the risk and proceed.

If the installation was successful, the EJBCA dashboard should appear.

---

This concludes the installation of EJBCA on Ubuntu Server 22.04 LTS using VMWare Fusion.


