# `Домашнее задание к занятию: "10.4 Резервное копирование"` - `Болдыш Леонид`
### Задание 1

- `Полное резервное копирование подразумевает копирование всех имеющихся на требуемом носителе/разделе/директории данных`
- `При дифференциальном копировании в первый раз выполняется полное копирование всех данных (бэкап), а в последующем копируются лишь изменные данные и информация относительно сделанного бэкапа`
- `При инкрементном копировании выполняется копирование всех измененных данных относительного последнего бэкапа, т.е. периодически выполняется полное копирование данных` 

### Задание 2

`В представленном ниже примере организовано локальное резервирование директории.`

`Конфигурация bacula-dir`
```
Director {                            # define myself
  Name = bacula-server-dir
  DIRport = 9101                # where we listen for UA connections
  QueryFile = "/etc/bacula/scripts/query.sql"
  WorkingDirectory = "/var/lib/bacula"
  PidDirectory = "/run/bacula"
  Maximum Concurrent Jobs = 20
  Password = "bacula"         # Console password
  Messages = Daemon
  DirAddress = 127.0.0.1
}

JobDefs {
  Name = "DefaultJob"
  Type = Backup
  Level = Incremental
  Client = bacula-server-fd
  FileSet = "Local-configuration"
  Schedule = "LocalDaily"
  Storage = bacula-server-sd
  Messages = Standard
  Pool = LocalPool
  SpoolAttributes = yes
  Priority = 10
  Write Bootstrap = "/var/lib/bacula/%c.bsr"
}

Catalog {
  Name = MyCatalog
  dbname = "bacula"; dbuser = "bacula"; dbpassword = "bacula"; DB Address = "127.0.0.1"
}

Messages {
  Name = Standard
  mailcommand = "/usr/sbin/bsmtp -h localhost -f \"\(Bacula\) \<%r\>\" -s \"Bacula: %t %e of %c %l\" %r"
  operatorcommand = "/usr/sbin/bsmtp -h localhost -f \"\(Bacula\) \<%r\>\" -s \"Bacula: Intervention needed for %j\" %r"
  mail = root = all, !skipped
  operator = root = mount
  console = all, !skipped, !saved
  append = "/var/log/bacula/bacula.log" = all, !skipped
  catalog = all
}

Messages {
  Name = Daemon
  mailcommand = "/usr/sbin/bsmtp -h localhost -f \"\(Bacula\) \<%r\>\" -s \"Bacula daemon message\" %r"
  mail = root = all, !skipped
  console = all, !skipped, !saved
  append = "/var/log/bacula/bacula.log" = all, !skipped
}

Console {
  Name = bacula-server-mon
  Password = "bacula"
  CommandACL = status, .status
}

Pool {
  Name = LocalPool
  Pool Type = Backup
  Recycle = yes                       # Bacula can automatically recycle Volumes
  AutoPrune = yes                     # Prune expired volumes
  Volume Retention = 365 days         # one year
  Maximum Volume Bytes = 50G          # Limit Volume size to something reasonable
  Maximum Volumes = 100               # Limit number of Volumes in Pool
  Label Format = "Local-"               # Auto label
}

Client {
  Name = bacula-server-fd
  Address = 127.0.0.1
  FDPort = 9102
  Catalog = MyCatalog
  Password = "bacula"          # password for FileDaemon
  File Retention = 60 days            # 60 days
  Job Retention = 6 months            # six months
  AutoPrune = yes                     # Prune expired Jobs/Files
}

FileSet {
  Name = "Local-configuration"
  Include {
    Options {
      signature = MD5
    }
    File = "/etc/postgresql"
  }
    Exclude {
    File = /var/lib/bacula
    File = /nonexistant/path/to/file/archive/dir
    File = /proc
    File = /tmp
    File = /sys
    File = /.journal
    File = /.fsck
  }
}

Schedule {
  Name = "LocalDaily"
  Run = Full sun-sat at 23:10
}

Job {
  Name = "LocalBackup"
  JobDefs = "DefaultJob"
  Enabled = yes
  Level = Full
  FileSet="Local-configuration"
  Schedule = "LocalDaily"
  Storage = bacula-server-sd
  Pool = "LocalPool"
}

Storage {
  Name = bacula-server-sd
  Address = 127.0.0.1                # N.B. Use a fully qualified name here
  SDPort = 9103
  Password = "bacula"
  Device = Local-Device
  Media Type = File
  Maximum Concurrent Jobs = 10        # run up to 10 jobs a the same time
}

```
`Конфигураци bacula-fd`

```
Director {
  Name = bacula-server-dir
  Password = "bacula"
}

Director {
  Name = bacula-server-mon
  Password = "bacula"
  Monitor = yes
}

FileDaemon {                          # this is me
  Name = bacula-server-fd
  FDport = 9102                  # where we listen for the director
  WorkingDirectory = /var/lib/bacula
  Pid Directory = /run/bacula
  Maximum Concurrent Jobs = 20
  Plugin Directory = /usr/lib/bacula
  FDAddress = 127.0.0.1
}

# Send all messages except skipped files back to Director
Messages {
  Name = Standard
  director = bacula-server-dir = all, !skipped, !restored
}

```

`Конфигурация bacula-sd`

```
Storage {                             # definition of myself
  Name = bacula-server-sd
  SDPort = 9103                  # Director's port
  WorkingDirectory = "/var/lib/bacula"
  Pid Directory = "/run/bacula"
  Maximum Concurrent Jobs = 20
  SDAddress = 127.0.0.1
}

Director {
  Name = bacula-server-dir
  Password = "bacula"
}

Director {
  Name = bacula-server-mon
  Password = "bacula"
  Monitor = yes
}

Device {
  Name = Local-Device
  Media Type = File
  Archive Device = /backup
  LabelMedia = yes;                   # lets Bacula label unlabeled media
  Random Access = Yes;
  AutomaticMount = yes;               # when device opened, read it
  RemovableMedia = no;
  AlwaysOpen = no;
  Maximum Concurrent Jobs = 5
}

Messages {
  Name = Standard
  director = bacula-server-dir = all
}

```
---

### Задание 3

`Rsync host1`

```
pid file = /var/run/rsyncd.pid
lock file = /var/run/rsync.lock
log file = /var/log/rsync.log
[share]
path = /tmp/share/
hosts allow = 192.168.56.102
hosts deny = *
list = true
uid = root
gid = root
read only = false

```

`Rsync host2`

```
pid file = /var/run/rsyncd.pid
lock file = /var/run/rsync.lock
log file = /var/log/rsync.log
[share]
path = /tmp/share/
hosts allow = 192.168.56.101
hosts deny = *
list = true
uid = root
gid = root
read only = false

```
### Задание 4

`bacula-dir.conf`

```
Director {                            # define myself
  Name = bacula-server-dir
  DIRport = 9101                # where we listen for UA connections
  QueryFile = "/etc/bacula/scripts/query.sql"
  WorkingDirectory = "/var/lib/bacula"
  PidDirectory = "/run/bacula"
  Maximum Concurrent Jobs = 20
  Password = "bacula"         # Console password
  Messages = Daemon
  DirAddress = 127.0.0.1
}

Catalog {
  Name = MyCatalog
  dbname = "bacula"; dbuser = "bacula"; dbpassword = "bacula"; DB PORT = 5432; DB Address = 127.0.0.1
}

Messages {
  Name = Daemon
  mailcommand = "/usr/sbin/bsmtp -h localhost -f \"\(Bacula\) \<%r\>\" -s \"Bacula daemon message\" %r"
  mail = root = all, !skipped
  console = all, !skipped, !saved
  append = "/var/log/bacula/bacula.log" = all, !skipped
}

Messages {
  Name = Standard
  mailcommand = "/usr/sbin/bsmtp -h localhost -f \"\(Bacula\) \<%r\>\" -s \"Bacula: %t %e of %c %l\" %r"
  operatorcommand = "/usr/sbin/bsmtp -h localhost -f \"\(Bacula\) \<%r\>\" -s \"Bacula: Intervention needed for %j\" %r"
  mail = root = all, !skipped
  operator = root = mount
  console = all, !skipped, !saved
  append = "/var/log/bacula/bacula.log" = all, !skipped
  catalog = all
}

Console {
  Name = bacula-server-mon
  Password = "bacula"
  CommandACL = status, .status
}

Pool {
  Name = LocalPool
  Pool Type = Backup
  Recycle = yes                       # Bacula can automatically recycle Volumes
  AutoPrune = yes                     # Prune expired volumes
  Volume Retention = 365 days         # one year
  Maximum Volume Bytes = 50G          # Limit Volume size to something reasonable
  Maximum Volumes = 100               # Limit number of Volumes in Pool
  Storage = bacula-server-sd
}

Client {
  Name = Database-fd
  Address = 192.168.17.102
  FDPort = 9102
  Catalog = MyCatalog
  Password = "bacula"          # password for FileDaemon
  File Retention = 60 days            # 60 days
  Job Retention = 6 months            # six months
  AutoPrune = yes                     # Prune expired Jobs/Files
}

Client {
  Name = WebServer-fd
  Address = 192.168.17.103
  FDPort = 9102
  Catalog = MyCatalog
  Password = "bacula"          # password for FileDaemon
  File Retention = 60 days            # 60 days
  Job Retention = 6 months            # six months
  AutoPrune = yes                     # Prune expired Jobs/Files
}

FileSet {
  Name = "Local-configuration"
  Include {
    Options {
      signature = MD5
    }
    File = "/etc/postgresql"
  }
}

FileSet {
  Name = "Local-configuration2"
  Include {
    Options {
      signature = MD5
    }
    File = "/etc/nginx"
  }
}  

Schedule {
  Name = "LocalDaily"
  Run = Full sun-sat at 23:10
}

Job {
  Name = "LocalBackup"
  Type = Backup
  Enabled = yes
  Level = Full
  Client = Database-fd
  FileSet="Local-configuration"
  Schedule = "LocalDaily"
  Storage = bacula-server-sd
  Pool = "LocalPool"
  SpoolAttributes = yes
  Messages = Standard
  Write Bootstrap = "/var/lib/bacula/%c.bsr"
}

Job {
  Name = "LocalBackup2"
  Type = Backup
  Enabled = yes
  Level = Full
  Client = WebServer-fd
  FileSet="Local-configuration2"
  Schedule = "LocalDaily"
  Storage = bacula-server-sd
  Pool = "LocalPool"
  SpoolAttributes = yes
  Messages = Standard
  Write Bootstrap = "/var/lib/bacula/%c.bsr"
}

Storage {
  Name = bacula-server-sd
  Address = 127.0.0.1                 # N.B. Use a fully qualified name here
  SDPort = 9103
  Password = "bacula"
  Device = local-dev
  Media Type = File
  Maximum Concurrent Jobs = 10        # run up to 10 jobs a the same time
}

```

`bacula-sd.conf`

```
Storage {                             # definition of myself
  Name = bacula-server-sd
  SDPort = 9103                  # Director's port
  WorkingDirectory = "/var/lib/bacula"
  Pid Directory = "/run/bacula"
  Maximum Concurrent Jobs = 20
  SDAddress = 127.0.0.1
}

Director {
  Name = bacula-server-dir
  Password = "bacula"
}

Director {
  Name = bacula-server-mon
  Password = "bacula"
  Monitor = yes
}

Device {
  Name = local-dev
  Media Type = File
  Archive Device = /home/bacula
  LabelMedia = yes;                   # lets Bacula label unlabeled media
  Random Access = Yes;
  AutomaticMount = yes;               # when device opened, read it
  RemovableMedia = no;
  AlwaysOpen = no;
  Maximum Concurrent Jobs = 5
}

Messages {
  Name = Standard
  director = bacula-server-dir = all
}

```

`WebServer - bacula-fd.conf`

```
Director {
  Name = bacula-server-dir
  Password = "bacula"
}

Director {
  Name = bacula-server-mon
  Password = "bacula"
  Monitor = yes
}

FileDaemon {                          # this is me
  Name = WebServer-fd
  FDport = 9102                  # where we listen for the director
  WorkingDirectory = /var/lib/bacula
  Pid Directory = /run/bacula
  Maximum Concurrent Jobs = 20
  Plugin Directory = /usr/lib/bacula
  FDAddress = 192.168.17.103
}

Messages {
  Name = Standard
  director = WebServer-dir = all, !skipped, !restored
}

```

`PostgreSQL - bacula-fd.conf`

```
Director {
  Name = bacula-server-dir
  Password = "bacula"
}

Director {
  Name = bacula-server-mon
  Password = "bacula"
  Monitor = yes
}

FileDaemon {                          # this is me
  Name = Database-fd
  FDport = 9102                  # where we listen for the director
  WorkingDirectory = /var/lib/bacula
  Pid Directory = /run/bacula
  Maximum Concurrent Jobs = 20
  Plugin Directory = /usr/lib/bacula
  FDAddress = 192.168.17.102
}

Messages {
  Name = Standard
  director = Database-dir = all, !skipped, !restored
}

```