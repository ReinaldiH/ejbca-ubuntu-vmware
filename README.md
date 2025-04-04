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

![MariaDB Server](https://github.com/ReinaldiH/ejbca-ubuntu-vmware/blob/69fa0d1a6ccf52941ba8cf0090add41b8f800e4b/maria%20db%20servcer.webp?raw=true)

   To check the MariaDB status:
   ```bash
   sudo systemctl status mariadb
   ```

![Status MariaDB](https://github.com/ReinaldiH/ejbca-ubuntu-vmware/blob/69fa0d1a6ccf52941ba8cf0090add41b8f800e4b/status%20maria%20db.webp?raw=true)

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

![User Account](https://github.com/ReinaldiH/ejbca-ubuntu-vmware/blob/17d766410ae69346648a59a047ea62d6724a384c/User%20Account.webp?raw=true)

## Database Configuration
After installation, secure your database using MySQL configuration:
```bash
sudo mysql_secure_installation
```

![Sudo MySQL Install](https://github.com/ReinaldiH/ejbca-ubuntu-vmware/blob/ff1de418906875646ef5e8ca0b8e0d1ed63ce6ab/sudo%20mysql%20install.webp?raw=true)


EJBCA requires a MySQL (MariaDB) database. To log in as the root user:
```bash
mysql -u root -p
```

![MySQL Root](https://github.com/ReinaldiH/ejbca-ubuntu-vmware/blob/ff1de418906875646ef5e8ca0b8e0d1ed63ce6ab/mysql%20root.webp?raw=true)


Create a database, user, and password for EJBCA:
```sql
CREATE DATABASE ejbca CHARACTER SET utf8 COLLATE utf8_general_ci;
GRANT ALL PRIVILEGES ON ejbca.* TO 'ejbca'@'localhost' IDENTIFIED BY 'my-password';
FLUSH PRIVILEGES;
exit;
```
*Note: Replace `my-password` with a secure password of your choice.*

![Create Database](https://github.com/ReinaldiH/ejbca-ubuntu-vmware/blob/ff1de418906875646ef5e8ca0b8e0d1ed63ce6ab/create%20database.webp?raw=true)

![Flush Privilege](https://github.com/ReinaldiH/ejbca-ubuntu-vmware/blob/ff1de418906875646ef5e8ca0b8e0d1ed63ce6ab/flush%20privelege.webp?raw=true)


## Download and Install EJBCA
Next, download the EJBCA package:
```bash
sudo wget https://sourceforge.net/projects/ejbca/files/ejbca7/ejbca_ce_7_4_3_2.zip
```

![Wget EJBCA](https://github.com/ReinaldiH/ejbca-ubuntu-vmware/blob/09e9bf172dc9d700be18e707e1cdca71424aed2c/wget%20ejbca.webp?raw=true)

Rename the package for convenience.

![Rename EJBCA](https://github.com/ReinaldiH/ejbca-ubuntu-vmware/blob/09e9bf172dc9d700be18e707e1cdca71424aed2c/rename%20ejbca.webp?raw=true)


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

![Change Value Database EJBCA](https://github.com/ReinaldiH/ejbca-ubuntu-vmware/blob/09e9bf172dc9d700be18e707e1cdca71424aed2c/change%20value%20database%20ejbca.webp?raw=true)


After making changes, save the file and execute the setup script:
```bash
cd /opt
/opt/ejbca/bin/extra/ejbca-setup.sh
```
The installation will begin. You will need to confirm by typing "YES" for 1 to 3 questions.

![Start Installation](https://github.com/ReinaldiH/ejbca-ubuntu-vmware/blob/09e9bf172dc9d700be18e707e1cdca71424aed2c/start%20installtion.webp?raw=true)

![Success Installation EJBCA](https://github.com/ReinaldiH/ejbca-ubuntu-vmware/blob/09e9bf172dc9d700be18e707e1cdca71424aed2c/succes%20instalation%20ejbca.webp?raw=true)


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

![Termius](https://github.com/ReinaldiH/ejbca-ubuntu-vmware/blob/09e9bf172dc9d700be18e707e1cdca71424aed2c/termius.webp?raw=true)


## Accessing the EJBCA Web Interface
To access the EJBCA web interface, enter your server's IP address in your browser.

If you see a security warning:
1. Click **Advanced**.
2. Accept the risk and proceed.

If the installation was successful, the EJBCA dashboard should appear.

![EJBCA Web](https://github.com/ReinaldiH/ejbca-ubuntu-vmware/blob/1fc7f4ca2226d2364cc015f1d68d7e4603a71f70/EJBCA%20WEB.webp?raw=true)

![EJBCA Web 2](https://github.com/ReinaldiH/ejbca-ubuntu-vmware/blob/1fc7f4ca2226d2364cc015f1d68d7e4603a71f70/EJBCA%20WEB%202.webp?raw=true)

---

This concludes the installation of EJBCA on Ubuntu Server 22.04 LTS using VMWare Fusion.


