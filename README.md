# Server Setup Guide in Ubuntu

This guide covers the installation and configuration of the following services:

1. Samba
2. FTP
3. DNS
4. Squid
5. SSH

## 1. Samba Setup

### Step 1: Install Samba

sudo apt update
sudo apt install samba

### Step 2: Check Installation

whereis samba

### Step 3: Create a Directory for Sharing

mkdir /home/<username>/sambashare/

### Step 4: Configure Samba

sudo nano /etc/samba/smb.conf

Add the following:

[sambashare]
comment = Samba on Ubuntu
path = /home/username/sambashare
read only = no
browsable = yes

### Step 5: Restart Samba

sudo service smbd restart

### Step 6: Update Firewall Rules

sudo ufw allow samba

### Step 7: Set Samba Password

sudo smbpasswd -a username

### Step 8: Connect to Share

On Ubuntu, open File Manager, click "Connect to Server", and enter:

smb://<IP-Address-of-Ubuntu>/sambashare

---

## 2. FTP Server Setup

### Step 1: Install FTP Server (vsftpd)

sudo apt update
sudo apt install vsftpd

### Step 2: Enable Anonymous Download (Optional)

Edit the configuration file:

sudo nano /etc/vsftpd.conf

Set:

anonymous_enable=YES

### Step 3: Change FTP Directory (Optional)

Create a new directory for FTP:

sudo mkdir -p /srv/files/ftp
sudo usermod -d /srv/files/ftp ftp

### Step 4: Restart FTP Server

sudo service vsftpd restart

### Step 5: Enable Uploading

In the configuration file `/etc/vsftpd.conf`, set:

write_enable=YES

Then restart:

sudo service vsftpd restart

### Step 6: Enable Security (Optional)

Set these options in the configuration file:

chroot_local_user=YES
chroot_list_enable=YES
chroot_list_file=/etc/vsftpd.chroot_list

Create the chroot list:

sudo nano /etc/vsftpd.chroot_list

### Step 7: Enable SSL/TLS for FTPS (Optional)

Add to the config:

ssl_enable=YES

### Step 8: Check vsftpd Status

sudo service vsftpd status

### Step 9: Connect to FTP Server

ftp -p <Server-IP-Address>

---

## 3. DNS Server Setup

### Step 1: Install DNS Server (BIND9)

sudo apt update
sudo apt install bind9 dnsutils

### Step 2: Configure Caching DNS

Edit the configuration file:

sudo nano /etc/bind/named.conf.options

Uncomment and add your ISP's DNS server IPs.

### Step 3: Restart DNS Service

sudo systemctl restart bind9

### Step 4: Setup Forward Zone

Edit:

sudo nano /etc/bind/named.conf.local

Add:

zone "example.com" {
type master;
file "/etc/bind/db.example.com";
};

### Step 5: Setup Reverse Zone

Copy and edit the reverse zone file:

sudo cp /etc/bind/db.127 /etc/bind/db.192
sudo nano /etc/bind/db.192

### Step 6: Restart DNS and Check

sudo systemctl restart bind9
sudo service bind9 status

### Step 7: Test DNS Resolution

nslookup example.com
dig example.com

---

## 4. Squid Proxy Server Setup

### Step 1: Install Squid

sudo apt update
sudo apt install squid

### Step 2: Configure Squid

Edit the configuration file:

sudo nano /etc/squid/squid.conf

Add access control:

acl localnet src <your-ip-address>
acl blocksite dstdomain "/etc/squid/blocksite"
http_access deny blocksite
http_access allow localnet

### Step 3: Block Websites

Create the block list:

sudo nano /etc/squid/blocksite

Add websites like:

.facebook.com
.youtube.com

### Step 4: Configure Browser Proxy

Set your HTTP Proxy in the browser to `<IP-Address-of-Ubuntu>` with port `3128`.

### Step 5: Test Access

Try accessing blocked websites, access should be denied.

---

## 5. SSH Setup

### Step 1: Install OpenSSH

sudo apt update
sudo apt install openssh-server

### Step 2: Check SSH Status

sudo service ssh status

### Step 3: Configure SSH

Edit the SSH config:

sudo nano /etc/ssh/sshd_config

Set:

PasswordAuthentication yes
KbdInteractiveAuthentication no

### Step 4: Restart SSH Service

sudo service ssh restart

### Step 5: Generate SSH Key (Client Side)

ssh-keygen

### Step 6: Copy Key to Server

ssh-copy-id user@<server-ip>

### Step 7: Connect to SSH Server

ssh user@<server-ip>
