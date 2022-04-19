---
title: "Podman Mysql 8"
date: 2022-04-18T11:35:57+07:00
description: "Membuat container MySQL 8 di podman menggunakan fedora cockpit"
categories: ["local env"]
tags: ["dev","container", "podman", "mysql"]
---

Membuat container MySQL 8 di podman menggunakan fedora cockpit
- Masuk menu podman container
- Create container, isi field berikut
  - **Name** : mysql8
  - **Owner** : User:ashoka
  - **Image** : docker.io/mysql/mysql-server
  - **Command** : mysqld [tanpa terminal]
  - **Port mapping**:
      - host:3306 container 3306 tcp
  - **Volume**:
       - /home/ashoka/Apps/podman/mysql8/custom.cnf -> /etc/my.cnf.d/custom.cnf writable shared
       - /home/ashoka/Apps/podman/mysql8/initdb -> /docker-entrypoint-initdb.d writable shared
       - /home/ashoka/Apps/podman/mysql8/data -> /var/lib/mysql writable shared

- Environment variabel:
  - MYSQL_ROOT_PASSWORD : lahacia
  - MYSQL_USER : ashoka
  - MYSQL_PASSWORD : haha

- Buat file dan folder berikut:
```
.
└── mysql8
    ├── custom.cnf
    ├── data
    └── initdb
        └── grant.sql
```

file ~/A/podman►cat mysql8/custom.cnf 
``` 
[mysqld]
bind-address = 0.0.0.0
```

file ~/A/podman►cat mysql8/initdb/grant.sql 
```
GRANT ALL PRIVILEGES ON *.* TO 'ashoka'@'%' WITH GRANT OPTION;
```

- **Create and run**

