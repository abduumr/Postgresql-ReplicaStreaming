# Postgresql-ReplicaStreaming

# Postgresql
Instalasi Postgresql on Oracle Linux 8

```
https://www.postgresql.org/download/linux/redhat/
```
```
[root@node01 ~]# hostnamectl
   Static hostname: node01
         Icon name: computer-vm
           Chassis: vm
        Machine ID: 9b488e669f9a407c974df87ba9433126
           Boot ID: 37d1d3f1bab044c195fb2ad8ebf980cf
    Virtualization: vmware
  Operating System: Oracle Linux Server 8.8
       CPE OS Name: cpe:/o:oracle:linux:8:8:server
            Kernel: Linux 5.15.0-101.103.2.1.el8uek.x86_64
      Architecture: x86-64
[root@node01 ~]#
```
![image alt](https://github.com/abduumr/Postgresql/blob/main/postgres/1.png?raw=true)

```
[root@node01 ~]# lsblk
NAME        MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
sda           8:0    0   150G  0 disk
├─sda1        8:1    0   600M  0 part /boot/efi
├─sda2        8:2    0     1G  0 part /boot
└─sda3        8:3    0 148.4G  0 part
  ├─ol-root 252:0    0    70G  0 lvm  /
  ├─ol-swap 252:1    0   7.9G  0 lvm  [SWAP]
  └─ol-home 252:2    0  70.6G  0 lvm  /home
sdb           8:16   0    50G  0 disk
sr0          11:0    1  11.6G  0 rom
[root@node01 ~]#
```
![image alt](https://github.com/abduumr/Postgresql/blob/main/postgres/2.png?raw=true)

```
[root@node01 ~]# df -h
Filesystem           Size  Used Avail Use% Mounted on
devtmpfs             3.7G     0  3.7G   0% /dev
tmpfs                3.8G   16K  3.8G   1% /dev/shm
tmpfs                3.8G   17M  3.7G   1% /run
tmpfs                3.8G     0  3.8G   0% /sys/fs/cgroup
/dev/mapper/ol-root   70G   17G   54G  24% /
/dev/sda2           1014M  276M  739M  28% /boot
/dev/sda1            599M  5.1M  594M   1% /boot/efi
/dev/mapper/ol-home   71G  536M   70G   1% /home
tmpfs                762M     0  762M   0% /run/user/0
[root@node01 ~]#
```
![image alt](https://github.com/abduumr/Postgresql/blob/main/postgres/3.png?raw=true)

```
[root@node01 ~]# free -h
              total        used        free      shared  buff/cache   available
Mem:          7.4Gi       500Mi       6.0Gi        28Mi       986Mi       6.8Gi
Swap:         7.9Gi          0B       7.9Gi
[root@node01 ~]#
```
![image alt](https://github.com/abduumr/Postgresql/blob/main/postgres/4.png?raw=true)

```
[root@node01 ~]# fdisk /dev/sdb

Welcome to fdisk (util-linux 2.32.1).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.

Device does not contain a recognized partition table.
Created a new DOS disklabel with disk identifier 0xb6da0b3d.

Command (m for help): n
Partition type
   p   primary (0 primary, 0 extended, 4 free)
   e   extended (container for logical partitions)
Select (default p): p
Partition number (1-4, default 1): 1
First sector (2048-104857599, default 2048):
Last sector, +sectors or +size{K,M,G,T,P} (2048-104857599, default 104857599):

Created a new partition 1 of type 'Linux' and of size 50 GiB.

Command (m for help): p
Disk /dev/sdb: 50 GiB, 53687091200 bytes, 104857600 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0xb6da0b3d

Device     Boot Start       End   Sectors Size Id Type
/dev/sdb1        2048 104857599 104855552  50G 83 Linux

Command (m for help): w
The partition table has been altered.
Calling ioctl() to re-read partition table.
Syncing disks.
```
![image alt](https://github.com/abduumr/Postgresql/blob/main/postgres/5.png?raw=true)

```
[root@node01 ~]# pvcreate /dev/sdb1
  Physical volume "/dev/sdb1" successfully created.
```
![image alt](https://github.com/abduumr/Postgresql/blob/main/postgres/6.png?raw=true)

```
[root@node01 ~]# vgcreate database_vg /dev/sdb1
  Volume group "database_vg" successfully created
```
![image alt](https://github.com/abduumr/Postgresql/blob/main/postgres/7.png?raw=true)

```
[root@node01 ~]# lvcreate -l 100%FREE -n database_lv database_vg
  Logical volume "database_lv" created.
```
![image alt](https://github.com/abduumr/Postgresql/blob/main/postgres/8.png?raw=true)

```
[root@node01 ~]# mkfs.ext4 /dev/database_vg/database_lv

mke2fs 1.45.6 (20-Mar-2020)
Discarding device blocks: done
Creating filesystem with 13106176 4k blocks and 3276800 inodes
Filesystem UUID: a2473092-77ca-45c5-8dfe-693e5531de89
Superblock backups stored on blocks:
        32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208,
        4096000, 7962624, 11239424

Allocating group tables: done
Writing inode tables: done
Creating journal (65536 blocks): done
Writing superblocks and filesystem accounting information: done
```
![image alt](https://github.com/abduumr/Postgresql/blob/main/postgres/9.png?raw=true)

```
[root@node01 ~]# mkdir /database/
[root@node01 ~]# mount /dev/database_vg/database_lv /database
```
![image alt](https://github.com/abduumr/Postgresql/blob/main/postgres/10.png?raw=true)

```
[root@node01 ~]# vi /etc/fstab
/dev/database_vg/database_lv /database  ext4 defaults 0 0
```
![image alt](https://github.com/abduumr/Postgresql/blob/main/postgres/11.png?raw=true)

```
[root@node01 ~]# lsblk
NAME                        MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
sda                           8:0    0   150G  0 disk
├─sda1                        8:1    0   600M  0 part /boot/efi
├─sda2                        8:2    0     1G  0 part /boot
└─sda3                        8:3    0 148.4G  0 part
  ├─ol-root                 252:0    0    70G  0 lvm  /
  ├─ol-swap                 252:1    0   7.9G  0 lvm  [SWAP]
  └─ol-home                 252:2    0  70.6G  0 lvm  /home
sdb                           8:16   0    50G  0 disk
└─sdb1                        8:17   0    50G  0 part
  └─database_vg-database_lv 252:3    0    50G  0 lvm  /database
sr0                          11:0    1  11.6G  0 rom
```
![image alt](https://github.com/abduumr/Postgresql/blob/main/postgres/12.png?raw=true)

```
# Install the repository RPM:
[root@node01 ~]# sudo dnf install -y https://download.postgresql.org/pub/repos/yum/reporpms/EL-8-x86_64/pgdg-redhat-repo-latest.noarch.rpm
```
![image alt](https://github.com/abduumr/Postgresql/blob/main/postgres/13.png?raw=true)

```
# Disable the built-in PostgreSQL module:
[root@node01 ~]# sudo dnf -qy module disable postgresql
```
![image alt](https://github.com/abduumr/Postgresql/blob/main/postgres/14.png?raw=true)

```
# Install PostgreSQL:
[root@node01 ~]# sudo dnf install -y postgresql16-server
```
![image alt](https://github.com/abduumr/Postgresql/blob/main/postgres/15.png?raw=true)

```
# Create New Location
[root@node01 ~]# mkdir -p /database/pgdata/data
[root@node01 ~]# chown -R postgres:postgres /database
[root@node01 ~]# chmod -R 700 /database/pgdata
```
![image alt](https://github.com/abduumr/Postgresql/blob/main/postgres/16.png?raw=true)

```
# Edit the Postgres Systemd service (ALL)
[root@node01 ~]# vi  /lib/systemd/system/postgresql-16.service
Environment=PGDATA=/database/pgdata/data/
```
![image alt](https://github.com/abduumr/Postgresql/blob/main/postgres/17.png?raw=true)

```
[root@node01 ~]# systemctl daemon-reload
```
```
[root@node01 ~]# cd /database/pgdata/data/
[root@node01 data]# /usr/pgsql-16/bin/postgresql-16-setup initdb
```
![image alt](https://github.com/abduumr/Postgresql/blob/main/postgres/18.png?raw=true)
```
[root@node01 data]# systemctl enable --now postgresql-16
[root@node01 data]# systemctl start postgresql-16.service
[root@node01 data]# systemctl status postgresql-16.service
```
![image alt](https://github.com/abduumr/Postgresql/blob/main/postgres/19.png?raw=true)
```
[root@node01 data]# ls -lah
[root@node01 data]# su - postgres
[postgres@node01 ~]$ psql
postgres=# \l
```
![image alt](https://github.com/abduumr/Postgresql/blob/main/postgres/20.png?raw=true)

# Create Replica Streaming
```
[root@node02 ~]#  hostnamectl
   Static hostname: node02
         Icon name: computer-vm
           Chassis: vm
        Machine ID: de1d50fa62a54f67ab572c79f4395a8e
           Boot ID: 759d8733d621456cb17ad6092a0a511a
    Virtualization: vmware
  Operating System: Oracle Linux Server 8.8
       CPE OS Name: cpe:/o:oracle:linux:8:8:server
            Kernel: Linux 5.15.0-101.103.2.1.el8uek.x86_64
      Architecture: x86-64
[root@node02 ~]#
```
![image alt](https://github.com/abduumr/Postgresql/blob/main/postgres/21.png?raw=true)

```
[root@node02 ~]# lsblk
NAME        MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
sda           8:0    0   150G  0 disk
├─sda1        8:1    0   600M  0 part /boot/efi
├─sda2        8:2    0     1G  0 part /boot
└─sda3        8:3    0 148.4G  0 part
  ├─ol-root 252:0    0    70G  0 lvm  /
  ├─ol-swap 252:1    0   7.9G  0 lvm  [SWAP]
  └─ol-home 252:2    0  70.6G  0 lvm  /home
sdb           8:16   0    50G  0 disk
sr0          11:0    1  11.6G  0 rom
[root@node02 ~]#
```
![image alt](https://github.com/abduumr/Postgresql/blob/main/postgres/22.png?raw=true)

```
[root@node02 ~]# df -h
Filesystem           Size  Used Avail Use% Mounted on
devtmpfs             3.7G     0  3.7G   0% /dev
tmpfs                3.8G   16K  3.8G   1% /dev/shm
tmpfs                3.8G   17M  3.7G   1% /run
tmpfs                3.8G     0  3.8G   0% /sys/fs/cgroup
/dev/mapper/ol-root   70G   17G   54G  24% /
/dev/sda2           1014M  276M  739M  28% /boot
/dev/sda1            599M  5.1M  594M   1% /boot/efi
/dev/mapper/ol-home   71G  536M   70G   1% /home
tmpfs                762M     0  762M   0% /run/user/0
[root@node01 ~]#
```
![image alt](https://github.com/abduumr/Postgresql/blob/main/postgres/23.png?raw=true)

```
[root@node02 ~]# free -h
              total        used        free      shared  buff/cache   available
Mem:          7.4Gi       500Mi       6.0Gi        28Mi       986Mi       6.8Gi
Swap:         7.9Gi          0B       7.9Gi
[root@node01 ~]#
```
![image alt](https://github.com/abduumr/Postgresql/blob/main/postgres/24.png?raw=true)

```
[root@node02 ~]# fdisk /dev/sdb

Welcome to fdisk (util-linux 2.32.1).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.

Device does not contain a recognized partition table.
Created a new DOS disklabel with disk identifier 0xb6da0b3d.

Command (m for help): n
Partition type
   p   primary (0 primary, 0 extended, 4 free)
   e   extended (container for logical partitions)
Select (default p): p
Partition number (1-4, default 1): 1
First sector (2048-104857599, default 2048):
Last sector, +sectors or +size{K,M,G,T,P} (2048-104857599, default 104857599):

Created a new partition 1 of type 'Linux' and of size 50 GiB.

Command (m for help): p
Disk /dev/sdb: 50 GiB, 53687091200 bytes, 104857600 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0xb6da0b3d

Device     Boot Start       End   Sectors Size Id Type
/dev/sdb1        2048 104857599 104855552  50G 83 Linux

Command (m for help): w
The partition table has been altered.
Calling ioctl() to re-read partition table.
Syncing disks.
```
![image alt](https://github.com/abduumr/Postgresql/blob/main/postgres/25.png?raw=true)

```
[root@node02 ~]# pvcreate /dev/sdb1
  Physical volume "/dev/sdb1" successfully created.
```
![image alt](https://github.com/abduumr/Postgresql/blob/main/postgres/26.png?raw=true)

```
[root@node02 ~]# vgcreate database_vg /dev/sdb1
  Volume group "database_vg" successfully created
```
![image alt](https://github.com/abduumr/Postgresql/blob/main/postgres/27.png?raw=true)

```
[root@node02 ~]# lvcreate -l 100%FREE -n database_lv database_vg
  Logical volume "database_lv" created.
```
![image alt](https://github.com/abduumr/Postgresql/blob/main/postgres/28.png?raw=true)

```
[root@node02 ~]# mkfs.ext4 /dev/database_vg/database_lv

mke2fs 1.45.6 (20-Mar-2020)
Discarding device blocks: done
Creating filesystem with 13106176 4k blocks and 3276800 inodes
Filesystem UUID: a2473092-77ca-45c5-8dfe-693e5531de89
Superblock backups stored on blocks:
        32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208,
        4096000, 7962624, 11239424

Allocating group tables: done
Writing inode tables: done
Creating journal (65536 blocks): done
Writing superblocks and filesystem accounting information: done
```
![image alt](https://github.com/abduumr/Postgresql/blob/main/postgres/29.png?raw=true)

```
[root@node02 ~]# mkdir /database/
[root@node02 ~]# mount /dev/database_vg/database_lv /database
```
![image alt](https://github.com/abduumr/Postgresql/blob/main/postgres/30.png?raw=true)

```
[root@node02 ~]# vi /etc/fstab
/dev/database_vg/database_lv /database  ext4 defaults 0 0
```
![image alt](https://github.com/abduumr/Postgresql/blob/main/postgres/31.png?raw=true)

```
[root@node02 ~]# lsblk
NAME                        MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
sda                           8:0    0   150G  0 disk
├─sda1                        8:1    0   600M  0 part /boot/efi
├─sda2                        8:2    0     1G  0 part /boot
└─sda3                        8:3    0 148.4G  0 part
  ├─ol-root                 252:0    0    70G  0 lvm  /
  ├─ol-swap                 252:1    0   7.9G  0 lvm  [SWAP]
  └─ol-home                 252:2    0  70.6G  0 lvm  /home
sdb                           8:16   0    50G  0 disk
└─sdb1                        8:17   0    50G  0 part
  └─database_vg-database_lv 252:3    0    50G  0 lvm  /database
sr0                          11:0    1  11.6G  0 rom
```
![image alt](https://github.com/abduumr/Postgresql/blob/main/postgres/32.png?raw=true)

```
# Install the repository RPM:
[root@node02 ~]# sudo dnf install -y https://download.postgresql.org/pub/repos/yum/reporpms/EL-8-x86_64/pgdg-redhat-repo-latest.noarch.rpm
```
![image alt](https://github.com/abduumr/Postgresql/blob/main/postgres/33.png?raw=true)

```
# Disable the built-in PostgreSQL module:
[root@node02 ~]# sudo dnf -qy module disable postgresql
```
![image alt](https://github.com/abduumr/Postgresql/blob/main/postgres/34.png?raw=true)

```
# Install PostgreSQL:
[root@node02 ~]# sudo dnf install -y postgresql16-server
```
![image alt](https://github.com/abduumr/Postgresql/blob/main/postgres/35.png?raw=true)

```
# Create New Location
[root@node02 ~]# mkdir -p /database/pgdata/data
[root@node02 ~]# chown -R postgres:postgres /database
[root@node02 ~]# chmod -R 700 /database/pgdata
```
![image alt](https://github.com/abduumr/Postgresql/blob/main/postgres/36.png?raw=true)

```
# Edit the Postgres Systemd service (ALL)
[root@node02 ~]# vi  /lib/systemd/system/postgresql-16.service
Environment=PGDATA=/database/pgdata/data/
```
![image alt](https://github.com/abduumr/Postgresql/blob/main/postgres/37.png?raw=true)

```
[root@node02 ~]# systemctl daemon-reload
```

```
Setting replica Primary - Standby
IP primary / node01  = 192.168.242.8/24
IP standby / node02 = 192.168.242.9/24
```
```
[postgres@node01 ~]$ psql
psql (16.6)
Type "help" for help.

postgres=# CREATE USER replicator WITH REPLICATION ENCRYPTED PASSWORD 'password';
CREATE ROLE
postgres=# CREATE DATABASE replication;
CREATE DATABASE
postgres=#
```
![image alt](https://github.com/abduumr/Postgresql/blob/main/postgres/38.png?raw=true)

```
[postgres@node01 ~]$ vi /database/pgdata/data/postgresql.conf

listen_addresses = '*'
```
![image alt](https://github.com/abduumr/Postgresql/blob/main/postgres/39.png?raw=true)

```
[postgres@node01 ~]$ vi /database/pgdata/data/pg_hba.conf

```
![image alt](https://github.com/abduumr/Postgresql/blob/main/postgres/40.png?raw=true)

```
[root@node01 data]# systemctl restart postgresql-16
[root@node01 data]# systemctl status postgresql-16

```
![image alt](https://github.com/abduumr/Postgresql/blob/main/postgres/41.png?raw=true)

```
[postgres@node02 ~]$ pg_basebackup -h 192.168.242.8 -D /database/pgdata/data/ -U replicator -P -v -R -X stream -C -S slaveslot1
```
![image alt](https://github.com/abduumr/Postgresql/blob/main/postgres/42.png?raw=true)

```
[root@node02 ~]# systemctl start postgresql-16
[root@node02 ~]# systemctl status postgresql-16
```
![image alt](https://github.com/abduumr/Postgresql/blob/main/postgres/43.png?raw=true)

```
[root@node01 data]# su - postgres
Last login: Sat Jan 18 14:51:43 WIB 2025 on pts/0
[postgres@node01 ~]$ psql
psql (16.6)
Type "help" for help.

postgres=# SELECT slot_name, slot_type, active, restart_lsn FROM pg_replication_slots;
 slot_name  | slot_type | active | restart_lsn
------------+-----------+--------+-------------
 slaveslot1 | physical  | t      | 0/3000060
(1 row)

postgres=#

``````
![image alt](https://github.com/abduumr/Postgresql/blob/main/postgres/44.png?raw=true)

```
```
isi
```
```
isi
```
```
isi
``````
isi
```
```
isi
```
