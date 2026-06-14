## Node Setup

| Server Name             | IP Internal | IP External | CPU, RAM | Storage                     |
|--------------------------|-------------|-------------|----------|-----------------------------|
| Controller.homelab.com   | 172.26.0.21 | 172.26.1.21 | 4, 8 Gb  | 100Gb, 40Gb (SSD), 100Gb    |
| Compute.homelab.com      | 172.26.0.22 | 172.26.1.22 | 4, 8 Gb  | 100Gb + 100Gb               |
| Storage.homelab.com      | 172.26.0.23 | 172.26.1.23 | 4, 8 Gb  | 100Gb + 200Gb               |

### Controller Node Disk Layout setup
sda (100 GB) → OS root (/)
sdb (40 GB) → MariaDB, RabbitMQ, Etcd
sdc (100 GB) → Glance images + logs

**1. Verify Disks**  

```bash
lsblk
```
**2. Partition and Format sdb (40 GB)**  
   sdb1 → MariaDB (/var/lib/mysql) ~20 GB  
   sdb2 → RabbitMQ (/var/lib/rabbitmq) ~10 GB  
   sdb3 → Etcd (/var/lib/etcd) ~10 GB  

Partition:
```bash
sudo parted /dev/sdb --script mklabel gpt
sudo parted /dev/sdb --script mkpart primary ext4 0% 20GB
sudo parted /dev/sdb --script mkpart primary ext4 20GB 30GB
sudo parted /dev/sdb --script mkpart primary ext4 30GB 100%
```
Format:
```bash
sudo mkfs.ext4 /dev/sdb1
sudo mkfs.ext4 /dev/sdb2
sudo mkfs.ext4 /dev/sdb3
```

Create mount points:
```bash
sudo mkdir -p /var/lib/mysql /var/lib/rabbitmq /var/lib/etcd
```

Mount:
```bash
echo "/dev/sdb1 /var/lib/mysql ext4 defaults 0 2" | sudo tee -a /etc/fstab
echo "/dev/sdb2 /var/lib/rabbitmq ext4 defaults 0 2" | sudo tee -a /etc/fstab
echo "/dev/sdb3 /var/lib/etcd ext4 defaults 0 2" | sudo tee -a /etc/fstab
sudo mount -a
```

**3. Partition and Format sdc (100 GB)**  
Split into two partitions:  
sdc1 → Glance images (/var/lib/glance/images) ~70 GB  
sdc2 → Logs (/var/log) ~30 GB  

Partition:
```bash
sudo parted /dev/sdc --script mklabel gpt
sudo parted /dev/sdc --script mkpart primary ext4 0% 70GB
sudo parted /dev/sdc --script mkpart primary ext4 70GB 100%
```

Format:
```bash
sudo mkfs.ext4 /dev/sdc1
sudo mkfs.ext4 /dev/sdc2
```

Create mount points:
```bash
sudo mkdir -p /var/lib/glance/images /var/log
```

Mount:
```bash
echo "/dev/sdc1 /var/lib/glance/images ext4 defaults 0 2" | sudo tee -a /etc/fstab
echo "/dev/sdc2 /var/log ext4 defaults 0 2" | sudo tee -a /etc/fstab
sudo mount -a
```

**4. Verify Mounts**  
```bash
   df -h
```

Expected result:
<img width="1167" height="156" alt="image" src="https://github.com/user-attachments/assets/ae74afd1-c17c-4045-926c-4b7596793306" />

### Compute Node Disk Layout setup

sda (100 GB) → OS root (/) — already used during Ubuntu installation  
sdb (200 GB) → Nova instances (/var/lib/nova/instances)  
sdc (20 GB) → Logs (/var/log)  

**1.Verify Disks**  
```bash
lsblk
```
**2. Partition and Format sdb (200 GB for Nova)**  
```bash
sudo parted /dev/sdb --script mklabel gpt
sudo parted /dev/sdb --script mkpart primary ext4 0% 100%
sudo mkfs.ext4 /dev/sdb1
```
Create mount point:
```bash
sudo mkdir -p /var/lib/nova/instances
```
Add to /etc/fstab:
```bash
echo "/dev/sdb1 /var/lib/nova/instances ext4 defaults 0 2" | sudo tee -a /etc/fstab
```
Mount:
```bash
sudo mount -a
```
**3. Partition and Format sdc (20 GB for Logs)**  
```bash
sudo parted /dev/sdc --script mklabel gpt
sudo parted /dev/sdc --script mkpart primary ext4 0% 100%
sudo mkfs.ext4 /dev/sdc1
```
Create mount point:
```bash
sudo mkdir -p /var/log
```
Add to /etc/fstab:
```bash
echo "/dev/sdc1 /var/log ext4 defaults 0 2" | sudo tee -a /etc/fstab
```
Mount:
```bash
sudo mount -a
```
**4. Verify Mounts**
```bash
df -h
```
Expected Result:
<img width="1136" height="77" alt="image" src="https://github.com/user-attachments/assets/cf4de3e0-c35f-43ef-93e9-e5d222bbe1d6" />

### Storage Node Disk Layout

sda (100 GB) → OS root (/) — already used during Ubuntu installation  
sdb (20 GB) → Cinder metadata (/var/lib/cinder/volumes)  
sdc (200 GB) → Dedicated LVM PV for cinder-volumes VG (actual block storage pool)  
sdd (20 GB) → Logs (/var/log)  

**1. Verify Disks**
```bash
lsblk
```
**2. Prepare sdb (20 GB for Cinder metadata)**
```bash
sudo parted /dev/sdb --script mklabel gpt
sudo parted /dev/sdb --script mkpart primary ext4 0% 100%
sudo mkfs.ext4 /dev/sdb1
```
Create mount point:
```bash
sudo mkdir -p /var/lib/cinder/volumes
```
Add to /etc/fstab:
```bash
echo "/dev/sdb1 /var/lib/cinder/volumes ext4 defaults 0 2" | sudo tee -a /etc/fstab
```
Mount:
```bash
sudo mount -a
```
**3. Prepare sdc (200 GB for Cinder volumes VG)**  
```bash
sudo parted /dev/sdc --script mklabel gpt
sudo parted /dev/sdc --script mkpart primary 0% 100%
sudo pvcreate /dev/sdc1
sudo vgcreate cinder-volumes /dev/sdc1
```
Verify:
```bash
sudo vgs
```
Expected Result:
<img width="1148" height="95" alt="image" src="https://github.com/user-attachments/assets/86b60375-6eaf-43ea-8bb8-ed67f08fe69a" />

**4. Prepare sdd (20 GB for Logs)**  
```bash
sudo parted /dev/sdd --script mklabel gpt
sudo parted /dev/sdd --script mkpart primary ext4 0% 100%
sudo mkfs.ext4 /dev/sdd1
```
Create mount point:
```bash
sudo mkdir -p /var/log
```
Add to /etc/fstab:  
```bash
echo "/dev/sdd1 /var/log ext4 defaults 0 2" | sudo tee -a /etc/fstab
```
Mount:
```bash
sudo mount -a
```
**5. Verify Mounts**  
```bash
df -h
```
Expected Result:
<img width="1161" height="89" alt="image" src="https://github.com/user-attachments/assets/7a6c35de-c73e-49ee-aed6-c76baa3e37fc" />

And check LVM:  
```bash
sudo vgs
sudo lvs
```
Expected Result:
<img width="1167" height="204" alt="image" src="https://github.com/user-attachments/assets/51376efb-da57-47ac-94dd-18c77b6267b9" />

## Base OS, networking, and time sync

**1.Set hostname**
o	Ensure the hostname matches the FQDN and resolvable via dc.homelab.com..  
```bash
sudo hostnamectl set-hostname controller.homelab.com  # Run on controller
sudo hostnamectl set-hostname compute.homelab.com     # Run on compute
sudo hostnamectl set-hostname storage.homelab.com     # Run on storage
```
**2. Verify DNS resolution**  
```bash
getent hosts controller.homelab.com
getent hosts compute.homelab.com
getent hosts storage.homelab.com
getent hosts dc.homelab.com
```
**3. Disable swap (recommended for services and libvirt)**  
```bash
sudo sed -i.bak '/ swap / s/^/#/' /etc/fstab
sudo swapoff -a
```
**4.Time sync — Controller syncs to dc.homelab.com; others sync to Controller**  
On Controller: chrony  
```bash
sudo apt update
sudo apt install -y chrony

sudo tee /etc/chrony/chrony.conf >/dev/null <<'EOF'
server dc.homelab.com iburst
bindaddress 172.26.0.21
allow 172.26.0.0/24
local stratum 10
log measurements statistics tracking
EOF

sudo systemctl enable --now chrony
sudo systemctl restart chrony
```

On Compute and Storage: crony  
```bash
sudo apt update
sudo apt install -y chrony

sudo tee /etc/chrony/chrony.conf >/dev/null <<'EOF'
server controller.homelab.com iburst
bindaddress 172.26.0.23
allow 172.26.0.0/24
log measurements statistics tracking
EOF

sudo systemctl enable --now chrony
sudo systemctl restart chrony
```
Validation:  
```bash
Chronyc sources -v
Chronyc tracking
Chronyc clients
```
Expected Result:  
<img width="864" height="655" alt="image" src="https://github.com/user-attachments/assets/c1e092cd-bead-45a6-a98e-eb876b20fdb3" />

## Enable Flamingo packages and base dependencies (Controller)  

**Cloud Archive and system dependencies**  
```bash
sudo apt update
sudo apt install -y software-properties-common
sudo add-apt-repository -y cloud-archive:flamingo
sudo apt update
sudo apt -y dist-upgrade
sudo reboot
```
### Core services on Controller** **(MariaDB, RabbitMQ, Memcached, Etcd, keystone, Apache, Python, OpenStack clients)**  
```bash
sudo apt install -y mariadb-server mariadb-client
sudo apt install -y rabbitmq-server
sudo apt install -y memcached
sudo apt install -y etcd-server etcd-client
sudo apt install -y keystone apache2 libapache2-mod-wsgi-py3
sudo apt install -y python3-pymysql python3-openstackclient
```
### MariaDB setup
Update configuration file and start MariaDB  
```bash
sudo tee /etc/mysql/mariadb.conf.d/99-openstack.cnf >/dev/null <<'EOF'
[mysqld]
bind-address = 172.26.0.21
default-storage-engine = innodb
innodb_file_per_table = 1
max_connections = 4096
collation-server = utf8mb4_general_ci
character-set-server = utf8mb4
EOF
sudo systemctl restart mariadb
```
Secure and create DB admin
```bash
sudo mysql -u root <<'SQL'
CREATE USER 'openstack'@'%' IDENTIFIED BY 'Passw0rd';
GRANT ALL PRIVILEGES ON *.* TO 'openstack'@'%' WITH GRANT OPTION;
FLUSH PRIVILEGES;
SQL
sudo mysql_secure_installation
```
Validation
```bash
mysql -h controller.homelab.com -u openstack -p -e "SHOW DATABASES;"
```
Expected result:  
<img width="647" height="183" alt="image" src="https://github.com/user-attachments/assets/88701466-c906-4512-95e4-600287399893" />

### RabbitMQ setup

Generate CA signed certificate for etcd to use https  

**1. Certificate folder and permissions**  
```bash
sudo mkdir -p /etc/ssl/openstack/rabbitmq
sudo groupadd openstackssl
sudo usermod -aG openstackssl rabbitmq
sudo chown root:openstackssl /etc/ssl/openstack
sudo chmod 750 /etc/ssl/openstack
sudo chown -R root:openstackssl /etc/ssl/openstack/rabbitmq
sudo chmod 750 /etc/ssl/openstack/rabbitmq
```
**2. Generate CSR on Linux (controller node)**  
```bash
cd /etc/ssl/openstack/etcd
openssl genrsa -out rabbitmq.key 4096
```
**3. Create csr.conf**  
```bash
[ req ]
default_bits       = 4096
prompt             = no
default_md         = sha256
req_extensions     = req_ext
distinguished_name = dn

[ dn ]
CN = controller.homelab.com

[ req_ext ]
subjectAltName = @alt_names

[ alt_names ]
DNS.1 = controller.homelab.com
IP.1  = 172.26.0.21
```
**4. Generate CSR:**  
```bash
openssl req -new -key rabbitmq.key -out rabbitmq.csr -config rabbitmq.conf
```
**Generate CA certificate from dc.homelab.com ,reffer etcd certificate generation steps below.**  
**5. Copy rabbitmq.cer into /etc/ssl/openstack/rabbitmq and rename the file to .crt**   
```bash
mv /etc/ssl/openstack/rabbitmq/rabbitmq.cer  /etc/ssl/openstack/rabbitmq/rabbitmq.crt
```
**6. Set permission for the files**  
```bash
sudo chmod 640 /etc/ssl/openstack/rabbitmq/rabbitmq.key
sudo chmod 644 /etc/ssl/openstack/rabbitmq/rabbitmq.crt
sudo chown root:openstackssl /etc/ssl/openstack/rabbitmq/rabbitmq.crt /etc/ssl/openstack/rabbitmq/rabbitmq.key
```
**7. Create RabbitMQ configuration file /etc/rabbitmq/rabbitmq.conf**  
```bash
# Listen on TLS port
listeners.ssl.default = 5671

# Certificates
ssl_options.cacertfile = /etc/ssl/openstack/ca.crt
ssl_options.certfile   = /etc/ssl/openstack/rabbitmq/rabbitmq.crt
ssl_options.keyfile    = /etc/ssl/openstack/rabbitmq/rabbitmq.key

# Enforce peer verification
ssl_options.verify = verify_peer
ssl_options.fail_if_no_peer_cert = true
```
**8. Restart RabbitMQ**  
```bash
sudo systemctl restart rabbitmq-server
```
**9. Create RabbitMQ application user**  
```bash
sudo rabbitmqctl add_user openstack Passw0rd
sudo rabbitmqctl set_permissions openstack ".*" ".*" ".*"
sudo rabbitmqctl set_policy ha-all "^" '{"ha-mode":"all"}'
sudo rabbitmqctl set_user_tags openstack administrator
```
**10. Validation**  
```bash
rabbitmqctl list_users
rabbitmqctl list_permissions -p /
```
**Expected Result:**  
<img width="1156" height="255" alt="image" src="https://github.com/user-attachments/assets/918c28f4-3a9c-46bb-b37e-c08b545de06b" />

### Memcached setup  

**1. Certificate folder and permissions**  
```bash
sudo mkdir -p /etc/ssl/openstack/memcached
sudo usermod -aG openstackssl memcache
sudo chown -R root:openstackssl /etc/ssl/openstack/memcached
sudo chmod 750 /etc/ssl/openstack/memcached
```
**2. Generate CSR on Linux (controller node)**  
```bash
cd /etc/ssl/openstack/memcached
openssl genrsa -out memcached.key 4096
```
**3. Create csr.conf**  
```bash
[ req ]
default_bits       = 4096
prompt             = no
default_md         = sha256
req_extensions     = req_ext
distinguished_name = dn

[ dn ]
CN = controller.homelab.com

[ req_ext ]
subjectAltName = @alt_names

[ alt_names ]
DNS.1 = controller.homelab.com
IP.1  = 172.26.0.21
```
**4. Generate CSR:**  
```bash
openssl req -new -key memcached.key -out memcached.csr -config memcached.conf
```
**Generate CA certificate from dc.homelab.com reffer same step as etcd certificate generation.**  
**5. Copy memcached.cer into /etc/ssl/openstack/memcached and rename the file to .crt**  
```bash
mv /etc/ssl/openstack/memcached/memcached.cer  /etc/ssl/openstack/memcached/memcached.crt
```
**6. Set permission for the files**  
```bash
sudo chmod 640 /etc/ssl/openstack/memcached/memcached.key
sudo chmod 644 /etc/ssl/openstack/memcached/memcached.crt
sudo chown root:openstackssl /etc/ssl/openstack/memcached/memcached.crt /etc/ssl/openstack/memcached/memcached.key
```
**7. Edit the file as below /etc/memcached.conf**  
```bash
# memcached default config file
# Debian/Ubuntu package

# Run as a daemon
-d

# Log output
logfile /var/log/memcached.log

# Memory allocation (MB)
-m 64

# Secure port for TLS connections
-p 11214

# Run as the memcache system user
-u memcache

# Bind to internal IP
-l 172.26.0.21

# Limit simultaneous connections
-c 1024

# Enable TLS/SSL
-Z

# TLS options: server cert, private key, CA cert, minimum TLS version
-o ssl_chain_cert=/etc/ssl/openstack/memcached/memcached.crt,ssl_key=/etc/ssl/openstack/memcached/memcached.key,ssl_ca_cert=/etc/ssl/openstack/ca.crt,ssl_min_version=2

# Use a pidfile
-P /var/run/memcached/memcached.pid
```
**8. Restart services**  
```bash
sudo systemctl daemon-reexec 
sudo systemctl restart memcached
```
**Validation**  
```bash
sudo ss -ltnp | grep 11214
openssl s_client -connect controller.homelab.com:11214 -CAfile /etc/ssl/openstack/ca.crt
openstack token issue
```
### Etcd setup

**1. Certificate folder and permissions**  
```bash
sudo mkdir -p /etc/ssl/openstack/etcd
sudo groupadd openstackssl
sudo usermod -aG openstackssl etcd
sudo chown root:openstackssl /etc/ssl/openstack
sudo chmod 750 /etc/ssl/openstack
sudo chown -R root:openstackssl /etc/ssl/openstack/etcd 
sudo chmod 750 /etc/ssl/openstack/etcd
```
**2. Generate CSR on Linux (controller node)**  
```bash
cd /etc/ssl/openstack/etcd
openssl genrsa -out etcd.key 4096
```
**3. Create csr.conf**  
```bash
[ req ]
default_bits       = 4096
prompt             = no
default_md         = sha256
req_extensions     = req_ext
distinguished_name = dn

[ dn ]
CN = controller.homelab.com

[ req_ext ]
subjectAltName = @alt_names

[ alt_names ]
DNS.1 = controller.homelab.com
IP.1  = 172.26.0.21
```
**4. Generate CSR:**  
```bash
openssl req -new -key etcd.key -out etcd.csr -config csr.conf
```
**5. Transfer CSR to Windows AD CS server**  
```bash
scp /etc/ssl/openstack/etcd/etcd.csr administrator@dc.homelab.com:C:\Temp\
```
**6. Enabling a Certificate Template in AD**  

Open the Certificate Templates Console
•	Log in to your AD CS server (dc.homelab.com) with Domain Admin rights.
•	Press Win + R, type mmc, and hit Enter.
•	In the MMC console, go to File → Add/Remove Snap in.
•	Select Certificate Templates and click Add → OK.
•	This opens the Certificate Templates management console.

Duplicate an Existing Template  
•	In the Certificate Templates console, right click a template (e.g., Computer or Web Server) and choose Duplicate Template.
•	This creates a new template you can customize.
•	Give it a descriptive name (e.g., EtcdServerAuth).

Configure Template Properties  
•	General Tab: Set the template display name and validity period (e.g., 1 year).
•	Request Handling Tab: Ensure “Allow private key to be exported” if you need to move keys.
•	Subject Name Tab: Choose “Supply in the request” if you’ll generate CSRs on Linux.
•	Extensions Tab: Ensure Server Authentication EKU is present. Add Client Authentication if etcd peers will mutually authenticate.
•	Security Tab: Grant Enroll and Autoenroll permissions to the groups or users who will request the certificate (e.g., Domain Admins, or a service account).

Publish the Template
•	Open the Certification Authority console (certsrv.msc).
•	Expand your CA → Certificate Templates.
•	Right click Certificate Templates → New → Certificate Template to Issue.
•	Select your new template (EtcdServerAuth) and click OK.
•	The template is now enabled and available for requests.

Request a Certificate Using the Template  
•	From the AD CS web enrollment (http://dc.homelab.com/certsrv), choose Advanced Certificate Request.
•	Paste your CSR content (etcd.csr from Linux).
•	Select your new template (EtcdServerAuth) from the dropdown.
•	Submit → Download the issued certificate in Base64 format.
•	Also click on certificate chain option to download P7B file and open it to download CA certificate

<img width="764" height="255" alt="image" src="https://github.com/user-attachments/assets/0cfb77d7-062a-4bbe-9755-1d09b4fc7c2e" />

<img width="759" height="317" alt="image" src="https://github.com/user-attachments/assets/2d920087-e8eb-4142-ac59-f69efaa3b54d" />

```bash
Copy etcd.cer into controller at /etc/ssl/openstack/etcd
Copy ca.cer into controller at /etc/ssl/openstack/
```

**7. Move the file to .crt **  
```bash
mv /etc/ssl/openstack/etcd/etcd.cer  /etc/ssl/openstack/etcd/etcd.crt
mv /etc/ssl/openstack/ca.cer  /etc/ssl/openstack/ca.crt
```
**8. Set permission for the files**  
```bash
sudo chmod 640 /etc/ssl/openstack/etcd/etcd.key
sudo chmod 644 /etc/ssl/openstack/etcd/etcd.crt /etc/ssl/openstack/ca.crt
sudo chown root:openstackssl /etc/ssl/openstack/etcd/etcd.crt /etc/ssl/openstack/etcd/etcd.key
sudo chown root:root /etc/ssl/openstack/ca.crt
```
**9. Validate file hashes and that should match**  
```bash
openssl x509 -noout -modulus -in /etc/ssl/openstack/etcd/etcd.crt | openssl md5 
openssl rsa -noout -modulus -in /etc/ssl/openstack/etcd/etcd.key | openssl md5
```
**Expected result:**  
<img width="1117" height="97" alt="image" src="https://github.com/user-attachments/assets/69389eb7-6d65-4a49-a8a4-5ea029e41ef7" />

**10. Add Ubuntu trust store so all services trust it:**  
```bash
sudo cp ca.crt /usr/local/share/ca-certificates/dc-homelab-ca.crt
sudo update-ca-certificates
```
**11. Setup writable data directory for etcd**  
Check directory existence
```bash
ls -ld /var/lib/etcd/default
```
If its missing, create it  
```bash
sudo mkdir -p /var/lib/etcd/default
sudo chown etcd:etcd /var/lib/etcd/default
sudo chmod 700 /var/lib/etcd/default
```
Clean up old/corrupted data (if this is a fresh setup)  
If you don’t need existing cluster data:  
```bash
sudo systemctl stop etcd
sudo rm -rf /var/lib/etcd/default/*
sudo chown -R etcd:etcd /var/lib/etcd
```
**12. Verify etcd user permissions **   
```bash
ps -u etcd
```
**13. Confirm systemd unit**    
Check /lib/systemd/system/etcd.service or /etc/systemd/system/etcd.service.d/override.conf for:  
```bash
ExecStart=/usr/bin/etcd \
  --data-dir=/var/lib/etcd/default \
```
**14. Edit /etc/default/etcd and insert the following lines:**  
```bash
ETCD_LISTEN_PEER_URLS="https://172.26.0.21:2380"
ETCD_LISTEN_CLIENT_URLS="https://172.26.0.21:2379"
ETCD_INITIAL_ADVERTISE_PEER_URLS="https://controller.homelab.com:2380"
ETCD_ADVERTISE_CLIENT_URLS="https://controller.homelab.com:2379"
ETCD_INITIAL_CLUSTER="controller=https://controller.homelab.com:2380"
ETCD_NAME="controller"
ETCD_CERT_FILE="/etc/ssl/openstack/etcd/etcd.crt"
ETCD_KEY_FILE="/etc/ssl/openstack/etcd/etcd.key"
ETCD_TRUSTED_CA_FILE="/etc/ssl/openstack/ca.crt"
ETCD_PEER_CERT_FILE="/etc/ssl/openstack/etcd/etcd.crt"
ETCD_PEER_KEY_FILE="/etc/ssl/openstack/etcd/etcd.key"
ETCD_PEER_TRUSTED_CA_FILE="/etc/ssl/openstack/ca.crt"
```
**15. Restart etcd **  
```bash
sudo systemctl daemon-reexec
sudo systemctl restart etcd
sudo systemctl status etcd
```
**16. Validate with etcdctl**  
```bash
ETCDCTL_API=3 etcdctl \
  --endpoints="https://controller.homelab.com:2379" \
  --cacert=/etc/ssl/openstack/ca.crt \
  --cert=/etc/ssl/openstack/etcd/etcd.crt \
  --key=/etc/ssl/openstack/etcd/etcd.key \
  endpoint health
```
```bash
ETCDCTL_API=3 etcdctl \
  --endpoints="https://controller.homelab.com:2379" \
  --cacert=/etc/ssl/openstack/ca.crt \
  --cert=/etc/ssl/openstack/etcd/etcd.crt \
  --key=/etc/ssl/openstack/etcd/etcd.key \
  member list
```
```bash
ETCDCTL_API=3 etcdctl \
  --endpoints="https://controller.homelab.com:2379" \
  --cacert=/etc/ssl/openstack/ca.crt \
  --cert=/etc/ssl/openstack/etcd/etcd.crt \
  --key=/etc/ssl/openstack/etcd/etcd.key \
  endpoint status --write-out=table
```
**Expected result**  
<img width="1134" height="256" alt="image" src="https://github.com/user-attachments/assets/7305e90d-0a81-4d29-a936-214f3cfc84a4" />

<img width="1200" height="23" alt="image" src="https://github.com/user-attachments/assets/f3ef0163-d599-499c-ae5d-a3b2549c7320" />

## Keystone on Controller (with Flamingo WSGI wrapper)  

**1.DNS entry creation **  
```bash
pubkey-controller.homelab.com to resolve external access ip 172.26.1.21
intkey-controller.homelb.com to resolve internal service access ip 172.26.0.21
```
**2. Certificate folder and permissions**  
```bash
sudo mkdir -p /etc/ssl/openstack/keystone
sudo usermod -aG openstackssl keystone
Note: openstackssl group already exist and set the permission for the folder /etc/ssl/openstack

sudo chown -R root:openstackssl /etc/ssl/openstack/keystone
sudo chmod 750 /etc/ssl/openstack/keystone
```
**3. Generate privatekey**  
```bash
cd /etc/ssl/openstack/keystone
openssl genrsa -out keystone.key 4096
```
**4. Create csr.conf file using below content**  
```bash   
[ req ]
default_bits       = 4096
prompt             = no
default_md         = sha256
req_extensions     = req_ext
distinguished_name = dn

[ dn ]
CN = controller.homelab.com

[ req_ext ]
subjectAltName = @alt_names

[ alt_names ]
DNS.1 = controller.homelab.com
DNS.2 = pubkey-controller.homelab.com
DNS.3 = intkey-controller.homelab.com
DNS.4 = controller
IP.1  = 172.26.0.21
IP.2  = 172.26.1.21
```
**5. Generate CSR**  
```bash  
openssl req -new -key keystone.key -out keystone.csr -config csr.conf
```
**Generate CA certificate from dc.homelab.com by following the same step as etcd certificate generation.**  
Copy keystone.cer into /etc/ssl/openstack/keystone and rename the file to .crt
```bash
mv /etc/ssl/openstack/keystone/keystone.cer  /etc/ssl/openstack/keystone/keystone.crt
```
**6. Set permission for the files**  
```bash
sudo chmod 640 /etc/ssl/openstack/keystone/keystone.key
sudo chmod 644 /etc/ssl/openstack/keystone/keystone.crt
sudo chown root:openstackssl /etc/ssl/openstack/keystone/keystone.crt /etc/ssl/openstack/keystone/keystone.key
```
**7. Validate file hashes and that should match.**  
```bash
openssl x509 -noout -modulus -in /etc/ssl/openstack/keystone/keystone.crt | openssl md5 
openssl rsa -noout -modulus -in /etc/ssl/openstack/keystone/keystone.key | openssl md5
```
<img width="1142" height="98" alt="image" src="https://github.com/user-attachments/assets/25665ee1-bc75-4bce-bacd-3e7253dbe3cf" />

**8. DB configuration for keystone**  
```bash
sudo mysql -u root -p <<'SQL'
CREATE DATABASE keystone;

CREATE USER 'keystone'@'controller.homelab.com' IDENTIFIED BY 'Passw0rd';
GRANT ALL PRIVILEGES ON keystone.* TO 'keystone'@'controller.homelab.com' WITH GRANT OPTION;

FLUSH PRIVILEGES;
SQL
```
**9. Update keystone.conf file**  
```bash
sudo tee /etc/keystone/keystone.conf >/dev/null <<'EOF'
[database]
connection = mysql+pymysql://keystone:Passw0rd@controller.homelab.com/keystone

[token]
provider = fernet

[cache]
backend = oslo_cache.memcache_pool
enabled = true
memcache_servers = controller.homelab.com:11214
use_ssl = true
tls_ca = /etc/ssl/openstack/ca.crt
tls_cert = /etc/ssl/openstack/keystone/keystone.crt 
tls_key = /etc/ssl/openstack/keystone/keystone.key

[DEFAULT]
debug = false
EOF
```
**10. Create the file /etc/apache2/sites-available/keystone-ssl.conf**  
```bash
<VirtualHost *:5000>
    ServerName pubkey-controller.homelab.com
    SSLEngine on
    SSLCertificateFile /etc/ssl/openstack/keystone/keystone.crt
    SSLCertificateKeyFile /etc/ssl/openstack/keystone/keystone.key
    SSLCertificateChainFile /etc/ssl/openstack/ca.crt
    WSGIDaemonProcess keystone-public processes=5 threads=1 user=keystone group=keystone display-name=%{GROUP}
    WSGIProcessGroup keystone-public
    WSGIScriptAlias / /usr/bin/keystone-wsgi-public
    <Directory /usr/bin>
        Require all granted
    </Directory>
</VirtualHost>

<VirtualHost *:5001>
    ServerName intkey-controller.homelab.com
    SSLEngine on
    SSLCertificateFile /etc/ssl/openstack/keystone/keystone.crt
    SSLCertificateKeyFile /etc/ssl/openstack/keystone/keystone.key
    SSLCertificateChainFile /etc/ssl/openstack/ca.crt
    WSGIDaemonProcess keystone-internal processes=5 threads=1 user=keystone group=keystone display-name=%{GROUP}
    WSGIProcessGroup keystone-internal
    WSGIScriptAlias / /usr/bin/keystone-wsgi-admin
    <Directory /usr/bin>
        Require all granted
    </Directory>
</VirtualHost>
```
**11. Edit the file /etc/apache2/ports.conf and add following ports**  
```bash
Listen 5000
Listen 5001
```
**12. Disable default keystone site and enavle ssl modules**  
```bash
sudo a2dissite keystone.conf
sudo a2enmod ssl  headers
```
**13. Permission for the file **  
```bash
sudo chown root:root /etc/apache2/sites-available/keystone-ssl.conf 
sudo chmod 644 /etc/apache2/sites-available/keystone-ssl.conf
```
**14. Create file /usr/bin/keystone-wsgi-admin**  
```bash
#!/usr/bin/python3
from keystone.server import wsgi as keystone_wsgi
application = keystone_wsgi.initialize_admin_application()
```
**15. Create file /usr/bin/keystone-wsgi-public**  
```bash
#!/usr/bin/python3
from keystone.server import wsgi as keystone_wsgi
application = keystone_wsgi.initialize_public_application()
```
**16. Set permission for the file **  
```bash
sudo chmod +x /usr/bin/keystone-wsgi-public
sudo chmod +x /usr/bin/keystone-wsgi-admin
```
**17. Run database migrations & Fernet and credential setup**  
```bash
sudo su -s /bin/sh -c "keystone-manage db_sync" keystone
sudo keystone-manage fernet_setup --keystone-user keystone --keystone-group keystone
sudo keystone-manage credential_setup --keystone-user keystone --keystone-group keystone
```
**18. Bootstrap Keystone**  
```bash
sudo keystone-manage bootstrap \
  --bootstrap-password Passw0rd \
  --bootstrap-admin-url https://intkey-controller.homelab.com:5001/v3/ \
  --bootstrap-public-url https://pubkey-controller.homelab.com:5000/v3/ \
  --bootstrap-internal-url https://intkey-controller.homelab.com:5001/v3/ \
  --bootstrap-region-id RegionOne
```
**19. Create ~/admin-openrc.sh with following content**  
```bash
export OS_PROJECT_DOMAIN_NAME=Default
export OS_USER_DOMAIN_NAME=Default
export OS_PROJECT_NAME=admin
export OS_USERNAME=admin
export OS_PASSWORD=Passw0rd
export OS_AUTH_URL=https://intkey-controller.homelab.com:5001/v3
export OS_IDENTITY_API_VERSION=3
export OS_IMAGE_API_VERSION=2
export OS_INTERFACE=public
export OS_REGION_NAME=RegionOne
```
**20. export the variable**  
```bash
source ~/admin-openrc.sh
```
**21. Validation for keystone**  
```bash
curl -k https://pubkey-controller.homelab.com:5000/v3/
curl -k https://intkey-controller.homelab.com:5001/v3/
```
**Expected Result:**  
<img width="1150" height="103" alt="image" src="https://github.com/user-attachments/assets/0e2e1e01-32b9-4a64-b468-aa55b3aa02fd" />

<img width="1153" height="164" alt="image" src="https://github.com/user-attachments/assets/167f9f9d-5961-4d2a-a040-d6a3e47d9d57" />

<img width="1200" height="20" alt="image" src="https://github.com/user-attachments/assets/6841b68a-2801-46f9-8843-b46b515b793a" />

### Placement Installation

**1. Install placement packages**  
```bash
sudo apt update
sudo apt install -y placement-api placement-common
```
**2. Create directory for certificates**    
```bash
mkdir -p /etc/ssl/openstack/placement
```
**3. Add placement user to openstackssl group**  
```bash
sudo usermod -aG openstackssl placement
```
**4. Set folder permission**  
```bash
chown root:openstackssl /etc/ssl/openstack/placement
chmod 750 /etc/ssl/openstack/placement
```
**5. Generate private key**  
```bash
cd /etc/ssl/openstack/placement
openssl genrsa -out placement.key 4096
```
**6. Make dns aliase to resolve placement api address to internal ip 172.26.0.21**  
```bash
intplace-controller.homelab.com  172.26.0.21
pubplace-controller.homelab.com  172.26.1.21
```
**7. Create csr.conf file** 
```bash
[ req ]
default_bits       = 4096
prompt             = no
default_md         = sha256
req_extensions     = req_ext
distinguished_name = dn

[ dn ]
CN = controller.homelab.com

[ req_ext ]
subjectAltName = @alt_names

[ alt_names ]
DNS.1 = controller.homelab.com
DNS.2 = intplace-controller.homelab.com
DNS.3 = controller
DNS.4 = pubplace-controller.homelab.com
IP.1  = 172.26.0.21
IP.2  = 172.26.1.21
```
**8. Generate CSR **  
```bash
openssl req -new -key placement.key -out placement.csr -config csr.conf
```
**Follow same process that is used in etcd setup to generate placement.cer file from AD.**  
**9. Copy it back to /etc/ssl/openstack/placement folder and Rename the file to .crt**  
```bash
mv placement.cer placement.crt
```
**10. Set file permissions**  
```bash
sudo chmod 640 /etc/ssl/openstack/placement/placement.key
sudo chmod 644 /etc/ssl/openstack/placement/placement.crt
sudo chown root:openstackssl /etc/ssl/openstack/placement/placement.crt /etc/ssl/openstack/placement/placement.key
```
**11. Create the placement database and grants**  
```bash
sudo mysql -u root -p <<'SQL'
CREATE DATABASE placement;
CREATE USER 'placement'@'controller.homelab.com' IDENTIFIED BY 'Passw0rd';
GRANT ALL PRIVILEGES ON placement.* TO 'placement'@'controller.homelab.com' WITH GRANT OPTION;
FLUSH PRIVILEGES;
SQL
```
**12. Create config directories if missing**  
```bash
sudo install -d -m 0755 /etc/placement 
sudo install -d -m 0755 /etc/placement/uwsgi
```
**13. Update placement.conf file**  
```bash
sudo tee /etc/placement/placement.conf > /dev/null <<'EOF'
[DEFAULT]
debug = False
log_dir = /var/log/placement

[api]
# Leave defaults unless customizing request limits or workers

[placement_database]
connection = mysql+pymysql://placement:Passw0rd@controller.homelab.com/placement?charset=utf8mb4

[keystone_authtoken]
www_authenticate_uri = https://intkey-controller.homelab.com:5001
auth_url = https://intkey-controller.homelab.com:5001
memcached_servers = controller.homelab.com:11214
memcache_use_ssl = true
memcache_tls_ca = /etc/ssl/openstack/ca.crt
memcache_tls_cert = /etc/ssl/openstack/placement/placement.crt
memcache_tls_key  = /etc/ssl/openstack/placement/placement.key
auth_type = password
project_domain_name = Default
user_domain_name = Default
project_name = service
username = placement
password = Passw0rd
verify = True
cafile = /etc/ssl/openstack/ca.crt

[oslo_policy]
policy_file = /etc/placement/policy.yaml
EOF
```
**14. Update policy.yml file**  
```bash
sudo tee /etc/placement/policy.yaml > /dev/null <<'EOF'
# Use default policies; add overrides as needed.
{}
EOF
```
**15. Set folder permissions**  
```bash
sudo chown -R placement:placement /etc/placement
sudo chmod 0640 /etc/placement/placement.conf
```
**Register placement in Keystone with HTTPS endpoints**  
**16. Export variables **
```bash
source ~/admin-openrc.sh
```
**17. Create user ,service and grant role**  
```bash
openstack user create --domain Default --password Passw0rd placement
openstack project show service || openstack project create --domain Default --description "Service Project" service
openstack role add --project service --user placement admin
```
**Expected output:**  
<img width="1158" height="322" alt="image" src="https://github.com/user-attachments/assets/c419b550-3eae-438c-868d-0ff45eefff95" />

**18. Create service entry**  
```bash
openstack service create --name placement --description "OpenStack Placement API" placement
```
**Expected Result:**  
<img width="1156" height="208" alt="image" src="https://github.com/user-attachments/assets/fe8231db-d5b5-4659-8262-686d62a458ac" />

**19. Endpoint registration**  
```bash
openstack endpoint create --region RegionOne placement public https://pubplace-controller.homelab.com:8778 
openstack endpoint create --region RegionOne placement internal https://intplace-controller.homelab.com:8779
openstack endpoint create --region RegionOne placement admin https://intplace-controller.homelab.com:8779
```
**Expected Result:**  
<img width="1173" height="884" alt="image" src="https://github.com/user-attachments/assets/d2ce4e46-cf08-4add-9d0f-7391e87bfbdf" />

**20. Update Apache port configuration**  
```bash
Edit the file /etc/apache2/ports.conf and add following ports

Listen 8778
Listen 8779
```

**21. Apache configuration for placement**  
```bash
sudo tee /etc/apache2/sites-available/placement-ssl.conf > /dev/null <<'EOF' 
# Public Placement endpoint
<VirtualHost *:8778>
    ServerName pubplace-controller.homelab.com
    ServerAlias pubplace-controller.homelab.com

    SSLEngine on
    SSLCertificateFile      /etc/ssl/openstack/placement/placement.crt
    SSLCertificateKeyFile   /etc/ssl/openstack/placement/placement.key
    SSLCertificateChainFile /etc/ssl/openstack/ca.crt

    # TLS hardening
    SSLProtocol all -SSLv3 -TLSv1 -TLSv1.1
    SSLCipherSuite HIGH:!aNULL:!MD5
    SSLHonorCipherOrder on
    Header always set Strict-Transport-Security "max-age=31536000; includeSubDomains"

    WSGIDaemonProcess placement-public processes=2 threads=16 user=placement group=placement \
        display-name=%{GROUP}
    WSGIProcessGroup placement-public
    WSGIScriptAlias / /usr/bin/placement-api

    SetEnv PLACEMENT_CONFIG /etc/placement/placement.conf

    <Directory /usr/bin>
        Require all granted
    </Directory>

    ErrorLog  /var/log/apache2/placement-public-error.log
    CustomLog /var/log/apache2/placement-public-access.log combined
</VirtualHost>

# Internal/Admin Placement endpoint
<VirtualHost *:8779>
    ServerName intplace-controller.homelab.com
    ServerAlias intplace-controller.homelab.com

    SSLEngine on
    SSLCertificateFile      /etc/ssl/openstack/placement/placement.crt
    SSLCertificateKeyFile   /etc/ssl/openstack/placement/placement.key
    SSLCertificateChainFile /etc/ssl/openstack/ca.crt

    SSLProtocol all -SSLv3 -TLSv1 -TLSv1.1
    SSLCipherSuite HIGH:!aNULL:!MD5
    SSLHonorCipherOrder on
    Header always set Strict-Transport-Security "max-age=31536000; includeSubDomains"

    WSGIDaemonProcess placement-internal processes=2 threads=16 user=placement group=placement \
        display-name=%{GROUP}
    WSGIProcessGroup placement-internal
    WSGIScriptAlias / /usr/bin/placement-api

    SetEnv PLACEMENT_CONFIG /etc/placement/placement.conf

    <Directory /usr/bin>
        Require all granted
    </Directory>

    ErrorLog  /var/log/apache2/placement-internal-error.log
    CustomLog /var/log/apache2/placement-internal-access.log combined
</VirtualHost>
EOF
```

**22. Disable default apache placement site and enable ssl one**  
```bash
sudo a2dissite placement-api.conf 
sudo a2ensite placement-ssl.conf
```

**23. Populate placement database**  
```bash
sudo -u placement placement-manage db sync
```

**24. Reload and restart apache**  
```bash
sudo systemctl reload apache2
sudo systemctl restart apache2
```

**25. Validation **  
**Basic API check (ensure your CA chain is trusted) **  
```bash
curl -sS --cacert /etc/ssl/openstack/ca.crt https://pubplace-controller.homelab.com:8778/ | jq
curl -sS --cacert /etc/ssl/openstack/ca.crt https://intplace-controller.homelab.com:8779/ | jq 
```
**Expected Result:**  
<img width="1181" height="665" alt="image" src="https://github.com/user-attachments/assets/dcb51b1b-d40b-4f9e-913a-93ec0ae4e83c" />

**Verify no error for the below command**
```bash
openstack resource provider list
```
<img width="1200" height="22" alt="image" src="https://github.com/user-attachments/assets/4b1468d5-fc2c-4fc5-9c6d-5b27cb1cbcba" />

### Glance Deployment on Controller

**1. Create the Glance database and user**  
```bash
sudo mysql -u root -p
CREATE DATABASE glance;
CREATE USER 'glance'@'controller.homelab.com' IDENTIFIED BY 'Passw0rd';
GRANT ALL PRIVILEGES ON glance.* TO 'glance'@'controller.homelab.com' IDENTIFIED BY ' Passw0rd';
FLUSH PRIVILEGES;
```
**Validation**  
```bash
mysql -u glance -pPassw0rd -h controller.homelab.com -e "SHOW DATABASES;"
```
**Expected Result:**  
<img width="1167" height="181" alt="image" src="https://github.com/user-attachments/assets/46da24b8-f0a8-48b9-8f22-16d4c99b391a" />

**Keystone User & Service**  

**2. Create Glance user, assign role, and register service.**  
```bash
openstack user create --domain Default --password Passw0rd glance
openstack role add --project service --user glance admin
openstack service create --name glance --description "OpenStack Image Service" image
```

**3. Install Glance packages**  
```bash
sudo apt update
sudo apt install -y glance-api python3-glanceclient
```

**4. Create directory for certificates**  
```bash
mkdir -p /etc/ssl/openstack/glance
```

**5. Add placement user to openstackssl group**  
```bash
sudo usermod -aG openstackssl glance
```

**6. Set folder permission**  
```bash
chown root:openstackssl /etc/ssl/openstack/glance
chmod 750 /etc/ssl/openstack/glance
```

**7. Generate private key**  
```bash
cd /etc/ssl/openstack/glance
openssl genrsa -out glance.key 4096
```

**8. Make dns aliase to resolve placement api address to internal ip 172.26.0.21**  
```bash
pubglance-controller.homelab.com  172.26.1.21
intglance-controller.homelab.com  172.26.0.21
```

**9. Create csr.conf file**  
```bash
[ req ]
default_bits       = 4096
prompt             = no
default_md         = sha256
req_extensions     = req_ext
distinguished_name = dn

[ dn ]
CN = controller.homelab.com

[ req_ext ]
subjectAltName = @alt_names

[ alt_names ]
DNS.1 = controller.homelab.com
DNS.2 = intglance-controller.homelab.com
DNS.3 = controller
DNS.4 = pubglance-controller.homelab.com
IP.1  = 172.26.0.21
IP.2  = 172.26.1.21
```

**10. Generate CSR**  
```bash
openssl req -new -key glance.key -out glance.csr -config csr.conf
```

**Follow same process that is used in etcd setup to generate glance.cer file from AD.**  
**Copy it back to /etc/ssl/openstack/glance folder**  

**11. Rename the file to .crt**  
```bash
mv glance.cer glance.crt
```

**12. Set file permissions**  
```bash
sudo chmod 640 /etc/ssl/openstack/glance/glance.key
sudo chmod 644 /etc/ssl/openstack/glance/glance.crt
sudo chown root:openstackssl /etc/ssl/openstack/glance/glance.crt /etc/ssl/openstack/glance/glance.key
```

**13. Endpoint registration**  
```bash
sudo chmod 640 /etc/ssl/openstack/glance/glance.key
sudo chmod 644 /etc/ssl/openstack/glance/glance.crt
sudo chown root:openstackssl /etc/ssl/openstack/glance/glance.crt /etc/ssl/openstack/glance/glance.key
```
**Expected Result:**  
<img width="1145" height="794" alt="image" src="https://github.com/user-attachments/assets/08703efd-84cd-4d31-8ea8-1d17c61aecd9" />

**14. Validation:**  

```bash
openstack endpoint list
```
**Expected Result:**  
<img width="1161" height="220" alt="image" src="https://github.com/user-attachments/assets/b390b07b-4a25-4a04-8da5-ac583b1384e4" />

**15. Move glance-api.conf to back_ glance-api.conf**  
```bash
mv /etc/glance/glance-api.conf /etc/glance/back_glance-api.conf
```

**16. Create new glance-api.conf file and insert the following in it.**  
```bash
[DEFAULT]
debug = False
log_dir = /var/log/glance
#bind_host = 0.0.0.0
#bind_port = 9292
# Enable TLS at the API layer
use_ssl = false
#cert_file = /etc/ssl/openstack/glance/glance.crt
#key_file  = /etc/ssl/openstack/glance/glance.key

[glance_store]
stores = file
default_store = file
filesystem_store_datadir = /var/lib/glance/images

[database]
connection = mysql+pymysql://glance:Passw0rd@controller.homelab.com/glance?charset=utf8mb4

[keystone_authtoken]
www_authenticate_uri = https://intkey-controller.homelab.com:5001
auth_url = https://intkey-controller.homelab.com:5001
memcached_servers = controller.homelab.com:11214
memcache_use_ssl = true
memcache_tls_ca = /etc/ssl/openstack/ca.crt
# Only if Memcached enforces mutual TLS:
memcache_tls_cert = /etc/ssl/openstack/glance/glance.crt
memcache_tls_key  = /etc/ssl/openstack/glance/glance.key

auth_type = password
project_domain_name = Default
user_domain_name = Default
project_name = service
username = glance
password = Passw0rd
cafile = /etc/ssl/openstack/ca.crt

[paste_deploy]
flavor = keystone
```

**17. Sync the database**  
```bash
sudo su -s /bin/sh -c "glance-manage db_sync" glance
```

**18. Start and enable Glance**  
```bash
sudo systemctl enable glance-api
sudo systemctl restart glance-api
sudo systemctl status glance-api
```

**19. Create file glance-ssl.conf under the path with the following content /etc/apache2/sites-available**  
```bash
# Public Glance endpoint
<VirtualHost *:9292>
    ServerName pubglance-controller.homelab.com
    ServerAlias pubglance-controller.homelab.com

    SSLEngine on
    SSLCertificateFile      /etc/ssl/openstack/glance/glance.crt
    SSLCertificateKeyFile   /etc/ssl/openstack/glance/glance.key
    SSLCertificateChainFile /etc/ssl/openstack/ca.crt

    # TLS hardening
    SSLProtocol all -SSLv3 -TLSv1 -TLSv1.1
    SSLCipherSuite HIGH:!aNULL:!MD5
    SSLHonorCipherOrder on
    Header always set Strict-Transport-Security "max-age=31536000; includeSubDomains"

    WSGIDaemonProcess glance-public processes=2 threads=16 user=glance group=glance \
        display-name=%{GROUP}
    WSGIProcessGroup glance-public
    WSGIScriptAlias / /usr/bin/glance-wsgi-api

    SetEnv GLANCE_CONFIG /etc/glance/glance-api.conf

    <Directory /usr/bin>
        Require all granted
    </Directory>

    ErrorLog  /var/log/apache2/glance-public-error.log
    CustomLog /var/log/apache2/glance-public-access.log combined
</VirtualHost>

# Internal/Admin Glance endpoint
<VirtualHost *:9392>
    ServerName intglance-controller.homelab.com
    ServerAlias intglance-controller.homelab.com

    SSLEngine on
    SSLCertificateFile      /etc/ssl/openstack/glance/glance.crt
    SSLCertificateKeyFile   /etc/ssl/openstack/glance/glance.key
    SSLCertificateChainFile /etc/ssl/openstack/ca.crt

    SSLProtocol all -SSLv3 -TLSv1 -TLSv1.1
    SSLCipherSuite HIGH:!aNULL:!MD5
    SSLHonorCipherOrder on
    Header always set Strict-Transport-Security "max-age=31536000; includeSubDomains"

    WSGIDaemonProcess glance-internal processes=2 threads=16 user=glance group=glance \
        display-name=%{GROUP}
    WSGIProcessGroup glance-internal
    WSGIScriptAlias / /usr/bin/glance-wsgi-api

    SetEnv GLANCE_CONFIG /etc/glance/glance-api.conf

    <Directory /usr/bin>
        Require all granted
    </Directory>

    ErrorLog  /var/log/apache2/glance-internal-error.log
    CustomLog /var/log/apache2/glance-internal-access.log combined
</VirtualHost>
```

**20. Update Apache port configuration**  
```bash
Edit the file /etc/apache2/ports.conf and add following ports
Listen 9292
Listen 9392
```

**21. Permission for the file**  
```bash
sudo chown root:root /etc/apache2/sites-available/glance-ssl.conf
sudo chmod 644 /etc/apache2/sites-available/glance-ssl.conf
```

**22. Disable default apache conf and enable ssl one**  
```bash
sudo a2dissite glance-api.conf 
sudo a2ensite glance-ssl.conf
```

**23. Reload and restart apache**  
```bash
sudo systemctl reload apache2
sudo systemctl restart apache2
```

**24. Validation**  

**Validate ports 9292 & 9293 are listening **  
```bash
ss -tlnp | grep apache2
```
```bash
curl -sS --cacert /etc/ssl/openstack/ca.crt https://pubglance-controller.homelab.com:9292/ | jq
curl -sS --cacert /etc/ssl/openstack/ca.crt https://intglance-controller.homelab.com:9392| jq
```

**Download cirros image**  
```bash
cd /tmp
wget https://download.cirros-cloud.net/0.6.2/cirros-0.6.2-x86_64-disk.img
```

**Import the image to glance**  
```bash
openstack image create \
  --file /tmp/cirros-0.6.2-x86_64-disk.img \
  --disk-format qcow2 \
  --container-format bare \
  --public \
  cirros
```

**Verify the image**  
```bash
openstack image list
```
<img width="870" height="150" alt="image" src="https://github.com/user-attachments/assets/1180aafc-f657-4b7b-b469-2384abad97ae" />

<img width="1200" height="22" alt="image" src="https://github.com/user-attachments/assets/a255c957-504d-47e9-a443-e0a8e6862535" />

### Nova setup on Controller

**1. Database Setup**  
```bash
sudo mysql -u root -p
CREATE DATABASE nova_api;
CREATE DATABASE nova;
GRANT ALL PRIVILEGES ON nova_api.* TO 'nova'@'controller.homelab.com'  IDENTIFIED BY 'Passw0rd'; 
GRANT ALL PRIVILEGES ON nova.* TO 'nova'@'controller.homelab.com'  IDENTIFIED BY 'Passw0rd'; 
CREATE DATABASE nova_cell0;
GRANT ALL PRIVILEGES ON nova_cell0.* TO 'nova'@'controller.homelab.com' IDENTIFIED BY 'Passw0rd';
FLUSH PRIVILEGES;
```

**Validation**  
```bash
mysql -u nova -pPassw0rd -h controller.homelab.com -e "SHOW DATABASES;"
mysql -u nova -p -h controller.homelab.com nova_cell0
```
<img width="1170" height="159" alt="image" src="https://github.com/user-attachments/assets/86a3bd95-ca56-4663-919f-0f245c68f2ba" />

**2. Keystone Service Setup**  
```bash
openstack user create --domain default --project service --password Passw0rd nova
openstack role add --project service --user nova admin
openstack service create --name nova --description "OpenStack Compute" compute
```
<img width="1191" height="476" alt="image" src="https://github.com/user-attachments/assets/2758b9e1-3c93-478b-83e0-ed0181c5f6f9" />

**Validation**  
```bash
openstack user list | grep nova
openstack role assignment list --user nova --project service
openstack service list | grep compute
```

**3. Install nova packages**  
```bash
sudo apt update
apt install nova-api nova-scheduler nova-conductor nova-novncproxy nova-compute
```

**4. Create directory for certificates**  
```bash
mkdir -p /etc/ssl/openstack/nova
```

5. Add nova user to openstackssl group              
```bash
sudo usermod -aG openstackssl nova
```

**6. Set folder permission**  
```bash
chown root:openstackssl /etc/ssl/openstack/nova
chmod 750 /etc/ssl/openstack/nova
```

**7. Generate private key**  
```bash
cd /etc/ssl/openstack/nova
openssl genrsa -out nova.key 4096
```

**8. Make dns aliase to resolve nova api address to internal ip 172.26.0.21**  
```bash
pubnova-controller.homelab.com  172.26.1.21
intnova-controller.homelab.com  172.26.0.21
```

**9. Create csr.conf file**  
```bash
[ req ]
default_bits       = 4096
prompt             = no
default_md         = sha256
req_extensions     = req_ext
distinguished_name = dn

[ dn ]
CN = controller.homelab.com

[ req_ext ]
subjectAltName = @alt_names

[ alt_names ]
DNS.1 = controller
DNS.2 = controller.homelab.com
DNS.3= intnova-controller.homelab.com
DNS.4 = pubnova-controller.homelab.com
DNS.5 = controller-mgmt
DNS.6 = controller-mgmt.homelab.com
IP.1  = 172.26.0.21
IP.2  = 172.26.1.21
```

**10. Generate CSR**  
```bash
openssl req -new -key nova.key -out nova.csr -config csr.conf
```

**Follow same process that is used in etcd setup to generate nova.cer file from AD.**  
**Copy it back to /etc/ssl/openstack/nova folder**  

**11. Rename the file to .crt**  
```bash
mv nova.cer nova.crt
```

**12. Set file permissions**  
```bash
sudo chmod 640 /etc/ssl/openstack/nova/nova.key
sudo chmod 644 /etc/ssl/openstack/nova/nova.crt
sudo chown root:openstackssl /etc/ssl/openstack/nova/nova.crt /etc/ssl/openstack/nova/nova.key
```

**13. Endpoint registration**  
```bash
openstack endpoint create --region RegionOne compute public   https://pubnova-controller.homelab.com:8774/v2.1
openstack endpoint create --region RegionOne compute internal https://intnova-controller.homelab.com:8775/v2.1
openstack endpoint create --region RegionOne compute admin    https://intnova-controller.homelab.com:8775/v2.1
```

<img width="1152" height="605" alt="image" src="https://github.com/user-attachments/assets/8de3cc2a-259f-48e2-856d-daeda74c996e" />

**Validation**  
```bash
openstack endpoint list |grep -i nova
```
<img width="1191" height="83" alt="image" src="https://github.com/user-attachments/assets/15310bde-6e0d-460c-b044-ab4fd738e12a" />  

**Backup current configuration file /etc/nova/nova.conf and delete the original one.**  

**14. Create new nova.conf file with following content**  
```bash
[DEFAULT]
transport_url = rabbit://openstack:Passw0rd@controller.homelab.com:5671?ssl=1
auth_strategy = keystone
enabled_apis = osapi_compute,metadata
my_ip = 172.26.0.21
log_dir = /var/log/nova
lock_path = /var/lock/nova
state_path = /var/lib/nova

[api_database]
connection = mysql+pymysql://nova:Passw0rd@controller.homelab.com/nova_api

[database]
connection = mysql+pymysql://nova:Passw0rd@controller.homelab.com/nova

[keystone_authtoken]
www_authenticate_uri = https://pubkey-controller.homelab.com:5000
auth_url = https://intkey-controller.homelab.com:5001
memcached_servers = controller:11214
memcache_use_ssl = true
memcache_secret_key = Passw0rd
auth_type = password
project_domain_name = Default
user_domain_name = Default
project_name = service
username = nova
password = Passw0rd

[glance]
api_servers = https://intglance-controller.homelab.com:9392

[placement]
auth_url = https://intplace-controller.homelab.com:8779
project_domain_name = Default
user_domain_name = Default
project_name = service
username = placement
password = Passw0rd
region_name = RegionOne

[libvirt]
virt_type = kvm

[oslo_messaging_rabbit]
ssl = True
ssl_ca_file = /etc/ssl/openstack/ca/ca.crt
ssl_cert_file = /etc/ssl/openstack/nova/nova.crt
ssl_key_file = /etc/ssl/openstack/nova/nova.key
```

**15. Create /etc/apache2/sites-available/nova-ssl.conf**  
```bash
# Public Nova endpoint
<VirtualHost *:8774>
    ServerName pubnova-controller.homelab.com
    ServerAlias pubnova-controller.homelab.com

    SSLEngine on
    SSLCertificateFile      /etc/ssl/openstack/nova/nova.crt
    SSLCertificateKeyFile   /etc/ssl/openstack/nova/nova.key
    SSLCertificateChainFile /etc/ssl/openstack/ca.crt

    SSLProtocol all -SSLv3 -TLSv1 -TLSv1.1
    SSLCipherSuite HIGH:!aNULL:!MD5
    SSLHonorCipherOrder on
    Header always set Strict-Transport-Security "max-age=31536000; includeSubDomains"

    WSGIDaemonProcess nova-public processes=2 threads=16 user=nova group=nova display-name=%{GROUP}
    WSGIProcessGroup nova-public
    WSGIApplicationGroup %{GLOBAL}
    WSGIScriptAlias / /usr/bin/nova-api-wsgi

    SetEnv NOVA_CONFIG /etc/nova/nova.conf

    <Directory /usr/bin>
        Require all granted
    </Directory>

    ErrorLog  /var/log/apache2/nova-public-error.log
    CustomLog /var/log/apache2/nova-public-access.log combined
</VirtualHost>

# Internal/Admin Nova endpoint
<VirtualHost *:8775>
    ServerName intnova-controller.homelab.com
    ServerAlias intnova-controller.homelab.com

    SSLEngine on
    SSLCertificateFile      /etc/ssl/openstack/nova/nova.crt
    SSLCertificateKeyFile   /etc/ssl/openstack/nova/nova.key
    SSLCertificateChainFile /etc/ssl/openstack/ca.crt

    SSLProtocol all -SSLv3 -TLSv1 -TLSv1.1
    SSLCipherSuite HIGH:!aNULL:!MD5
    SSLHonorCipherOrder on
    Header always set Strict-Transport-Security "max-age=31536000; includeSubDomains"

    WSGIDaemonProcess nova-internal processes=2 threads=16 user=nova group=nova display-name=%{GROUP}
    WSGIProcessGroup nova-internal
    WSGIApplicationGroup %{GLOBAL}
    WSGIScriptAlias / /usr/bin/nova-api-wsgi

    SetEnv NOVA_CONFIG /etc/nova/nova.conf

    <Directory /usr/bin>
        Require all granted
    </Directory>

    ErrorLog  /var/log/apache2/nova-internal-error.log
    CustomLog /var/log/apache2/nova-internal-access.log combined
</VirtualHost>
```

**16. Set file permissions**  
```bash
sudo chown -R nova:nova /etc/nova 
sudo chmod 640 /etc/nova/nova.conf

sudo chown root:root /etc/apache2/sites-available/nova-ssl.conf 
sudo chmod 644 /etc/apache2/sites-available/nova-ssl.conf
```

**17. Sync the database**  
```bash
su -s /bin/sh -c "nova-manage cell_v2 map_cell0" nova
su -s /bin/sh -c "nova-manage cell_v2 create_cell --name=cell1" nova
su -s /bin/sh -c "nova-manage api_db sync" nova
su -s /bin/sh -c "nova-manage db sync" nova
```

**Validation**  
```bash
su -s /bin/sh -c "nova-manage cell_v2 list_cells" nova
```

**18. Enable ssl site and disable default one**  
```bash
a2ensite nova-ssl.conf
a2dissite nova-api.conf
```

**19. Reload and restart apache**  
```bash
sudo systemctl reload apache2
sudo systemctl restart apache2
systemctl restart nova-scheduler nova-conductor nova-novncproxy
systemctl restart nova-compute
```

**Validation**  

**Validate ports 9292 & 9293 are listening **  

```bash
ss -tlnp | grep apache2
curl -vk https://pubnova-controller.homelab.com:8774/
```

<img width="1191" height="330" alt="image" src="https://github.com/user-attachments/assets/e4d17add-f0f6-46c7-b9ea-3c9c5234a536" />  

<img width="1200" height="26" alt="image" src="https://github.com/user-attachments/assets/78e36a77-ce7f-4074-9eac-a0d944c76e0d" />

### Neutron setup on Controller

**1. Database Setup** 
```bash
sudo mysql -u root -p
CREATE DATABASE neutron;
GRANT ALL PRIVILEGES ON neutron.* TO 'neutron'@'controller.homelab.com' IDENTIFIED BY 'Passw0rd';
FLUSH PRIVILEGES;
```
**Validation**
```bash
mysql -u neutron -pPassw0rd -h controller.homelab.com -e "SHOW DATABASES;"
```

**2. Keystone Service Setup**
```bash
openstack user create --domain Default --password Passw0rd neutron
openstack role add --project service --user neutron admin
openstack service create --name neutron --description "OpenStack Networking" network
```
**Validation**
```bash
openstack user list | grep neutron 
openstack role assignment list --user neutron --project service
openstack service list | grep network
```

**3. Install neutron packages**
```bash
sudo apt update
sudo apt install -y neutron-server neutron-plugin-ml2 neutron-openvswitch-agent neutron-dhcp-agent neutron-metadata-agent neutron-l3-agent python3-neutronclient
```

**4. SSL Certificate generation**  
**Create directory for certificates**
```bash
mkdir -p /etc/ssl/openstack/neutron
```
**Add neutron user to openstackssl group**              
```bash
sudo usermod -aG openstackssl neutron
```
**Set folder permission**
```bash
chown root:openstackssl /etc/ssl/openstack/neutron
chmod 750 /etc/ssl/openstack/neutron
```
**Generate private key**
```bash
cd /etc/ssl/openstack/neutron
openssl genrsa -out neutron.key 4096
```
**Make dns aliase to resolve neutron api address to internal ip 172.26.0.21**
```bash
pubneutron-controller.homelab.com 172.26.1.21
intneutron-controller.homelab.com  172.26.0.21
```
**Create csr.conf file**
```bash
[ req ]
default_bits       = 4096
prompt             = no
default_md         = sha256
req_extensions     = req_ext
distinguished_name = dn

[ dn ]
CN = controller.homelab.com

[ req_ext ]
subjectAltName = @alt_names

[ alt_names ]
DNS.1 = controller
DNS.2 = controller.homelab.com
DNS.3= intneutron-controller.homelab.com  
DNS.4 = pubneutron-controller.homelab.com
DNS.5 = controller-mgmt
DNS.6 = controller-mgmt.homelab.com
IP.1  = 172.26.0.21
IP.2  = 172.26.1.21
```
**Generate CSR**
```bash
openssl req -new -key neutron.key -out neutron.csr -config csr.conf
```
Follow same process that is used in etcd setup to generate neutron.cer file from AD.  
Copy it back to /etc/ssl/openstack/neutron folder

**Rename the file to .crt**
```bash
mv neutron.cer neutron.crt
```
**Set file permissions**
```bash
sudo chmod 640 /etc/ssl/openstack/neutron/neutron.key
sudo chmod 644 /etc/ssl/openstack/neutron/neutron.crt
sudo chown root:openstackssl /etc/ssl/openstack/neutron/neutron.crt /etc/ssl/openstack/ neutron/neutron.key
```

**5. Endpoint registration** ** 
```bash
openstack endpoint create --region RegionOne network public   https://pubneutron-controller.homelab.com:9696
openstack endpoint create --region RegionOne network internal https://intneutron-controller.homelab.com:9697
openstack endpoint create --region RegionOne network admin    https://intneutron-controller.homelab.com:9697
```
**Validation**
```bash
openstack endpoint list |grep -i neutron
```
<img width="1139" height="65" alt="image" src="https://github.com/user-attachments/assets/2a1b3c2c-bfcd-4bdd-90c8-58e3d2593f60" />

**6. Take backup of /etc/neutron/neutron.conf**
```bash
mv /etc/neutron/neutron.conf /etc/neutron/Back_neutron.conf
```

**7. Create new /etc/neutron/neutron.conf file**
```bash
[DEFAULT]
core_plugin = ml2
service_plugins = router
allow_overlapping_ips = true
transport_url = rabbit://openstack:Passw0rd@controller.homelab.com:5671?ssl=1
auth_strategy = keystone
log_dir = /var/log/neutron

[database]
connection = mysql+pymysql://neutron:Passw0rd@controller.homelab.com/neutron

[keystone_authtoken]
www_authenticate_uri = https://pubkey-controller.homelab.com:5000
auth_url = https://intkey-controller.homelab.com:5001
memcached_servers = controller.homelab.com:11214
memcache_use_ssl = true
memcache_tls_ca = /etc/ssl/openstack/ca.crt
auth_type = password
project_domain_name = Default
user_domain_name = Default
project_name = service
username = neutron
password = Passw0rd
cafile = /etc/ssl/openstack/ca.crt

[oslo_messaging_rabbit]
ssl = True
ssl_ca_file = /etc/ssl/openstack/ca/ca.crt
ssl_cert_file = /etc/ssl/openstack/neutron/neutron.crt
ssl_key_file = /etc/ssl/openstack/neutron/neutron.key
```

**8. Set file/folder permission**
```bash
sudo chown -R neutron:neutron /etc/neutron
sudo chmod 640 /etc/neutron/neutron.conf
```

**9. Take backup for following file:**
```bash
mv /etc/neutron/plugins/ml2/ml2_conf.ini /etc/neutron/plugins/ml2/back_ml2_conf.ini
mv /etc/neutron/plugins/ml2/openvswitch_agent.ini /etc/neutron/plugins/ml2/back_openvswitch_agent.ini
```

**10. Update following entries in ml2_conf.ini**
```bash
[ml2]
type_drivers = flat,vlan,vxlan
tenant_network_types = vxlan
mechanism_drivers = openvswitch,l2population
extension_drivers = port_security

[ml2_type_flat]
flat_networks = provider

[ml2_type_vlan]
network_vlan_ranges = provider:100:200

[ml2_type_vxlan]
vni_ranges = 1:1000

[securitygroup]
enable_ipset = true
```

**11. Update following entries in openvswitch_agent.ini**
```bash
[ovs]
local_ip = 172.26.0.21
bridge_mappings = provider:br-provider

[agent]
tunnel_types = vxlan
l2_population = true
prevent_arp_spoofing = true

[securitygroup]
firewall_driver = iptables_hybrid
```

**12. Create apache SSL Site (/etc/apache2/sites-available/neutron-ssl.conf)**
```bash
<VirtualHost *:9696>
    ServerName pubneutron-controller.homelab.com
    SSLEngine on
    SSLCertificateFile      /etc/ssl/openstack/neutron/neutron.crt
    SSLCertificateKeyFile   /etc/ssl/openstack/neutron/neutron.key
    SSLCertificateChainFile /etc/ssl/openstack/ca.crt
    SSLProtocol all -SSLv3 -TLSv1 -TLSv1.1
    SSLCipherSuite HIGH:!aNULL:!MD5
    SSLHonorCipherOrder on
    Header always set Strict-Transport-Security "max-age=31536000; includeSubDomains"

    WSGIDaemonProcess neutron-public processes=2 threads=16 user=neutron group=neutron
    WSGIProcessGroup neutron-public
    WSGIScriptAlias / /usr/bin/neutron-api
    SetEnv NEUTRON_CONFIG /etc/neutron/neutron.conf

    <Directory /usr/bin>
        Require all granted
    </Directory>

    ErrorLog  /var/log/apache2/neutron-public-error.log
    CustomLog /var/log/apache2/neutron-public-access.log combined
</VirtualHost>

<VirtualHost *:9697>
    ServerName intneutron-controller.homelab.com
    SSLEngine on
    SSLCertificateFile      /etc/ssl/openstack/neutron/neutron.crt
    SSLCertificateKeyFile   /etc/ssl/openstack/neutron/neutron.key
    SSLCertificateChainFile /etc/ssl/openstack/ca.crt
    SSLProtocol all -SSLv3 -TLSv1 -TLSv1.1
    SSLCipherSuite HIGH:!aNULL:!MD5
    SSLHonorCipherOrder on
    Header always set Strict-Transport-Security "max-age=31536000; includeSubDomains"

    WSGIDaemonProcess neutron-internal processes=2 threads=16 user=neutron group=neutron
    WSGIProcessGroup neutron-internal
    WSGIScriptAlias / /usr/bin/neutron-api
    SetEnv NEUTRON_CONFIG /etc/neutron/neutron.conf

    <Directory /usr/bin>
        Require all granted
    </Directory>

    ErrorLog  /var/log/apache2/neutron-internal-error.log
    CustomLog /var/log/apache2/neutron-internal-access.log combined
</VirtualHost>
```

**13. Update Apache Ports in /etc/apache2/port.conf**
```bash
Listen 9696
Listen 9697
```

**14. Create the log directory and file with correct ownership**
```bash
sudo mkdir -p /var/log/neutron
sudo touch /var/log/neutron/neutron-api.log
sudo chown -R neutron:neutron /var/log/neutron
sudo chmod 750 /var/log/neutron
sudo chmod 640 /var/log/neutron/neutron-api.log
```

**15. Update permission**
```bash
chown root:root /etc/apache2/sites-available/neutron-ssl.conf
chmod 644 /etc/apache2/sites-available/neutron-ssl.conf
```

**16. Disable original site and enable new one**
```bash
sudo a2ensite neutron-ssl.conf
sudo a2dissite neutron-api.conf
```

**17. Reload and restart apache**
```bash
sudo systemctl reload apache2
sudo systemctl restart apache2
```

**18. Database Migration**
```bash
sudo su -s /bin/sh -c "neutron-db-manage upgrade heads" neutron
sudo su -s /bin/sh -c "neutron-db-manage --config-file /etc/neutron/neutron.conf \
  --config-file /etc/neutron/plugins/ml2/ml2_conf.ini upgrade heads" neutron
```

**19. Verify Database Tables**
```bash
mysql -u neutron -pPassw0rd -h controller.homelab.com neutron -e "SHOW TABLES LIKE 'ml2%';"
```
**Expected result**

root@controller://etc/ssl/openstack/neutron# mysql -u neutron -pPassw0rd -h controller.homelab.com neutron -e "SHOW TABLES LIKE 'ml2%';"  
+-------------------------------+  
| Tables_in_neutron (ml2%)      |  
+-------------------------------+  
| ml2_distributed_port_bindings |  
| ml2_flat_allocations          |  
| ml2_geneve_allocations        |  
| ml2_geneve_endpoints          |  
| ml2_gre_allocations           |  
| ml2_gre_endpoints             |  
| ml2_port_binding_levels       |  
| ml2_port_bindings             |  
| ml2_vlan_allocations          |  
| ml2_vxlan_allocations         |  
| ml2_vxlan_endpoints           |  
+-------------------------------+  

**20. Start Services**
```bash
sudo systemctl restart neutron-openvswitch-agent neutron-dhcp-agent neutron-metadata-agent neutron-l3-agent
sudo systemctl enable neutron-openvswitch-agent neutron-dhcp-agent neutron-metadata-agent neutron-l3-agent
```
**Validation**
```bash
ss -tlnp | grep apache2
openstack network agent list
curl -sS --cacert /etc/ssl/openstack/ca.crt https://pubneutron-controller.homelab.com:9696/ | jq
curl -sS --cacert /etc/ssl/openstack/ca.crt https://intneutron-controller.homelab.com:9697/ | jq
```
**Expected result**
<img width="1150" height="414" alt="image" src="https://github.com/user-attachments/assets/91db06b6-f1a2-4c99-8d8d-9cef87ea7d0d" />

<img width="1200" height="36" alt="image" src="https://github.com/user-attachments/assets/4b8cafd3-873f-4478-a34c-ff261abf4ef1" />

### Horizon (Dashboard) on Controller

**Summary**
```bash
Public URL: https://pubhorizon-controller.homelab.com/
Internal health URL (optional): https://inthorizon-controller.homelab.com:8443/
Keystone internal URL: https://intkey-controller.homelab.com:5001/v3
Memcached (TLS): controller.homelab.com:11214
WSGI path (Flamingo): /usr/lib/python3/dist-packages/openstack_dashboard/wsgi.py
WEBROOT: / (Horizon mounted at root)
```

**1. Create DNS entries**
```bash
pubhorizon-controller.homelab.com  → 172.26.1.21  (public/browser)
inthorizon-controller.homelab.com → 172.26.0.21  (internal/health)
```

**2. Create Horizon certificate (AD CS method)**

**Create directory & permissions**
```bash
sudo mkdir -p /etc/ssl/openstack/horizon
sudo usermod -aG openstackssl horizon
sudo chown -R root:openstackssl /etc/ssl/openstack/horizon
sudo chmod 750 /etc/ssl/openstack/horizon
```

**Generate key & CSR**
```bash
cd /etc/ssl/openstack/horizon
openssl genrsa -out horizon.key 4096

cat > csr.conf << 'EOF'
[ req ]
default_bits       = 4096
prompt             = no
default_md         = sha256
req_extensions     = req_ext
distinguished_name = dn

[ dn ]
CN = controller.homelab.com

[ req_ext ]
subjectAltName = @alt_names

[ alt_names ]
DNS.1 = controller.homelab.com
DNS.2 = pubhorizon-controller.homelab.com
DNS.3 = inthorizon-controller.homelab.com
DNS.4 = controller
IP.1  = 172.26.0.21
IP.2  = 172.26.1.21
EOF

openssl req -new -key horizon.key -out horizon.csr -config csr.conf
```

**Sign via AD CS (same template/process as other services)**

Go to http://dc.homelab.com/certsrv → Advanced request  
Paste CSR → choose your server auth template → download Base64 cert as horizon.cer  
Download CA chain and place as /etc/ssl/openstack/ca.crt  

**Install, secure**
```bash
mv /etc/ssl/openstack/horizon/horizon.cer /etc/ssl/openstack/horizon/horizon.crt
chmod 640 /etc/ssl/openstack/horizon/horizon.key
chmod 644 /etc/ssl/openstack/horizon/horizon.crt
chown root:openstackssl /etc/ssl/openstack/horizon/horizon.{crt,key}

# Optional: publish CA into Ubuntu trust store
sudo cp /etc/ssl/openstack/ca.crt /usr/local/share/ca-certificates/dc-homelab-ca.crt
sudo update-ca-certificates
```

**3. Install Horizon packages**
```bash
sudo apt update
sudo apt install -y openstack-dashboard python3-django-horizon
```

**4. Prepare package local path & symlink to your config**

The Horizon package imports settings via the Python package path openstack_dashboard.local.local_settings. Ensure it points to /etc/openstack-dashboard/local_settings.py.
```bash
sudo mkdir -p /usr/lib/python3/dist-packages/openstack_dashboard/local/enabled
sudo install -m 0644 /dev/null /usr/lib/python3/dist-packages/openstack_dashboard/local/__init__.py

sudo ln -sf /etc/openstack-dashboard/local_settings.py \
  /usr/lib/python3/dist-packages/openstack_dashboard/local/local_settings.py

# Clean Python caches
sudo find /usr/lib/python3/dist-packages/openstack_dashboard -name "__pycache__" -type d -exec rm -rf {} +
sudo find /usr/lib/python3/dist-packages/openstack_dashboard -name "*.pyc" -delete
```

**5. Configure Horizon — /etc/openstack-dashboard/local_settings.py**

Paste the file below exactly (this is your working, production configuration; do not trim):
```bash
# Horizon Configuration for Homelab Flamingo

import os
import ssl
from horizon.utils import secret_key

DEBUG = False
WEBROOT = '/'

ALLOWED_HOSTS = [
    'pubhorizon-controller.homelab.com',
    'inthorizon-controller.homelab.com',
    'localhost',
    '127.0.0.1',
]

CSRF_TRUSTED_ORIGINS = [
    'https://pubhorizon-controller.homelab.com',
]

SESSION_COOKIE_SECURE = True
CSRF_COOKIE_SECURE = True
SECURE_PROXY_SSL_HEADER = ('HTTP_X_FORWARDED_PROTO', 'https')

TIME_ZONE = "UTC"

SECRET_KEY = secret_key.generate_or_read_from_file(
    '/var/lib/openstack-dashboard/secret_key'
)

# ----------------------------------------------
# TLS MEMCACHED
# ----------------------------------------------
TLS_CTX = ssl.create_default_context(cafile='/etc/ssl/openstack/ca.crt')
TLS_CTX.load_cert_chain(
    certfile='/etc/ssl/openstack/memcached/memcached.crt',
    keyfile='/etc/ssl/openstack/memcached/memcached.key'
)

CACHES = {
    'default': {
        'BACKEND': 'django.core.cache.backends.memcached.PyMemcacheCache',
        'LOCATION': 'controller.homelab.com:11214',
        'TIMEOUT': 300,
        'OPTIONS': {
            'no_delay': True,
            'ignore_exc': True,
            'tls_context': TLS_CTX,
        }
    }
}

SESSION_ENGINE = 'django.contrib.sessions.backends.cache'

# ----------------------------------------------
# KEYSTONE
# ----------------------------------------------
OPENSTACK_HOST = "intkey-controller.homelab.com"
OPENSTACK_KEYSTONE_URL = "https://intkey-controller.homelab.com:5001/v3"
OPENSTACK_KEYSTONE_DEFAULT_DOMAIN = "Default"
OPENSTACK_KEYSTONE_DEFAULT_ROLE = "user"

OPENSTACK_ENDPOINT_TYPE = "internalURL"
SECONDARY_ENDPOINT_TYPE = "ANY"

OPENSTACK_API_VERSIONS = {
    "identity": 3,
    "image": 2,
    "volume": 3,
    "compute": 2,
    "network": 2,
    "placement": 1,
}

OPENSTACK_SSL_NO_VERIFY = False
OPENSTACK_SSL_CACERT = '/etc/ssl/openstack/ca.crt'

# ----------------------------------------------
# GLANCE
# ----------------------------------------------
HORIZON_IMAGES_ALLOW_UPLOAD = True

# ----------------------------------------------
# NEUTRON
# ----------------------------------------------
OPENSTACK_NEUTRON_NETWORK = {
    'enable_router': True,
    'enable_quotas': True,
    'enable_ipv6': True,
    'enable_ha_router': True,
    'enable_fip_topology_check': True,
    'enable_floating_ip': True,
    'enable_lb': False,
    'enable_firewall': False,
    'enable_vpn': False,
    'supported_provider_types': ['vxlan', 'vlan', 'flat', 'gre'],
    'supported_vnic_types': ['normal', 'direct'],
}

CONSOLE_TYPE = 'AUTO'
```

**Permissions**
```bash
sudo chown root:horizon /etc/openstack-dashboard/local_settings.py
sudo chmod 640 /etc/openstack-dashboard/local_settings.py

sudo chown horizon:horizon /var/lib/openstack-dashboard/secret_key
sudo chmod 600 /var/lib/openstack-dashboard/secret_key
```

**6. Apache vhost — /etc/apache2/sites-available/horizon-ssl.conf**

Paste the file below exactly (your verified working vhost; mounts Horizon at /):
```bash
<VirtualHost *:443>
    ServerName pubhorizon-controller.homelab.com

    SSLEngine on
    SSLCertificateFile      /etc/ssl/openstack/horizon/horizon.crt
    SSLCertificateKeyFile   /etc/ssl/openstack/horizon/horizon.key
    SSLCertificateChainFile /etc/ssl/openstack/ca.crt

    # Unique daemon name for public vhost
    WSGIDaemonProcess horizon-public user=horizon group=horizon processes=2 threads=20 display-name=%{GROUP}
    WSGIProcessGroup horizon-public

    # 👇 Use the Flamingo WSGI entry point
    WSGIScriptAlias / /usr/lib/python3/dist-packages/openstack_dashboard/wsgi.py

    Alias /static /var/lib/openstack-dashboard/static
    <Directory /var/lib/openstack-dashboard/static>
        Require all granted
    </Directory>

    <Directory /usr/lib/python3/dist-packages/openstack_dashboard>
        Require all granted
    </Directory>

    ErrorLog  /var/log/apache2/horizon-public-error.log
    CustomLog /var/log/apache2/horizon-public-access.log combined
</VirtualHost>

<VirtualHost *:8443>
    ServerName inthorizon-controller.homelab.com

    SSLEngine on
    SSLCertificateFile      /etc/ssl/openstack/horizon/horizon.crt
    SSLCertificateKeyFile   /etc/ssl/openstack/horizon/horizon.key
    SSLCertificateChainFile /etc/ssl/openstack/ca.crt

    # Unique daemon name for internal vhost
    WSGIDaemonProcess horizon-internal user=horizon group=horizon processes=1 threads=10 display-name=%{GROUP}
    WSGIProcessGroup horizon-internal

    # 👇 Use the Flamingo WSGI entry point
    WSGIScriptAlias / /usr/lib/python3/dist-packages/openstack_dashboard/wsgi.py

    Alias /static /var/lib/openstack-dashboard/static
    <Directory /var/lib/openstack-dashboard/static>
        Require all granted
    </Directory>

    <Directory /usr/lib/python3/dist-packages/openstack_dashboard>
        Require all granted
    </Directory>

    ErrorLog  /var/log/apache2/horizon-internal-error.log
    CustomLog /var/log/apache2/horizon-internal-access.log combined
</VirtualHost>
```

**Enable site & modules**
```bash

sudo a2enmod ssl headers rewrite proxy proxy_http wsgi
sudo a2ensite horizon-ssl.conf

# Ensure ports are present once in ports.conf (no duplicates):
# 443 and 8443 must exist only once
sudo apachectl configtest
sudo systemctl restart apache2
```
Tip: Keep only one Listen 443 in /etc/apache2/ports.conf. Remove duplicates inside <IfModule ssl_module> and <IfModule mod_gnutls.c> blocks if present.

**7. SSL / group permissions for Horizon to read CA & memcached TLS**
```bash
# Horizon process must read CA and memcached client TLS
sudo usermod -aG openstackssl horizon

sudo chgrp openstackssl /etc/ssl/openstack
sudo chmod 750 /etc/ssl/openstack

sudo chgrp openstackssl /etc/ssl/openstack/ca.crt
sudo chmod 640 /etc/ssl/openstack/ca.crt

sudo chgrp -R openstackssl /etc/ssl/openstack/memcached
sudo chmod 750 /etc/ssl/openstack/memcached
sudo chmod 644 /etc/ssl/openstack/memcached/memcached.crt
sudo chmod 640 /etc/ssl/openstack/memcached/memcached.key

sudo systemctl restart apache2
```

**8. If AppArmor is strict on your host, allow Apache to read the custom SSL path:**
```bash
echo "  /etc/ssl/openstack/** r," | sudo tee -a /etc/apparmor.d/local/usr.sbin.apache2
sudo systemctl reload apparmor
```

**Validation**

**Apache vhosts bound correctly**
```bash
apache2ctl -S
ss -tlnp | egrep ':(443|8443)\s'
```

**Memcached TLS handshake**
```bash
openssl s_client -connect controller.homelab.com:11214 -CAfile /etc/ssl/openstack/ca.crt </dev/null | egrep 'Protocol|Cipher'
```

**Horizon login page**
```bash
curl -sk https://pubhorizon-controller.homelab.com/auth/login/ | head
```

**Browser**
```bash
Navigate to **https://pubhorizon-controller.homelab.com/** → login as admin / Passw0rd.
```

**Expected Result**

<img width="1652" height="616" alt="image" src="https://github.com/user-attachments/assets/8c5f73f2-8c0a-4944-983c-3b7d456cd4c6" />

<img width="1826" height="948" alt="image" src="https://github.com/user-attachments/assets/36dd0dfa-5710-43ac-b664-16f3766874f6" />

<img width="1200" height="29" alt="image" src="https://github.com/user-attachments/assets/6f1fb348-dfd1-4c8d-9856-27989eb55bcc" />



   


