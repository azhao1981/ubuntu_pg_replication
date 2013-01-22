# PostgreSQL 9.2 One Server only

1. OOM Killer:
  * First we want to turn off OOM killer. This is a highly discussed topic in the world of PostgreSQL admins. They go 
    as far as saying "OOM Killer is a bug, not a feature (on PostgreSQL servers)". You also might want to watch
    see https://www.youtube.com/watch?v=k4f24zn5D4s#t=36m22s:

  ```
  administrator@pg:~$ sudo -s
  root@pg:~# sysctl -w vm.overcommit_ratio=100
  root@pg:~# sysctl -w vm.overcommit_memory=2
  ```
  * Next, let's install PostgreSQL & Ruby (we need it for the config file script)
  
  ```
  root@pg:~# apt-get install postgresql-9.2
  root@pg:~# rvm install 1.9.3

2. postgresql:

  postgres@pg:~$ git clone https://github.com/sarmiena/ubuntu_pg_replication.git
  postgres@pg:~$ ./ubuntu_pg_replication/config_generator --help; # -m and -f are required
  postgres@pg:~$ ./ubuntu_pg_replication/config_generator --memory 2048 --file /etc/postgresql/9.2/main/postgresql.conf
  ```
3. BOTH MACHINES (Very important step -- otheriwse PostgreSQL will not start)
  * We need to set SHMMAX and SHMALL to allow PostgreSQL to load shared memory correctly. *IMPORTANT*: You must read your
    /etc/postgresql/9.2/main/postgresql.conf file and reference the shared_buffer parameter for calculating these kernel
    settings:
  
  ```
  root@pg:~# vi /etc/sysctl.d/*postgresql-shm.conf

  # Maximum size of shared memory segment in bytes
  # /!\ IMPORTANT /!\ - Only expecting PostgreSQL to be running on this server!
  # shared_buffer (from /etc/postgresql/9.2/main/postgresql.conf) + 20%
  kernel.shmmax = 514641100 # CHANGE ME

  # Maximum total size of shared memory in pages (normally 4096 bytes)
  # shmmax/4096
  kernel.shmall = 104704 # CHANGE ME
  
  root@pg:~# sysctl -p /etc/sysctl.d/30-postgresql-shm.conf
  root@pg:~# su - postgres
  