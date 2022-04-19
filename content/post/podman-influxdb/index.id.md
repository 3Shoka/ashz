---
title: "Podman InfluxDB"
date: 2022-04-19T10:05:48+07:00
description: "Membuat container InfluxDB di podman menggunakan fedora cockpit"
categories: ["local env"]
tags: ["dev","container", "podman", "influxdb"]
---

Membuat container InfluxDB di podman menggunakan fedora cockpit
- Masuk menu podman container
- Create container, isi field berikut
  - **Name** : flux2
  - **Owner** : User:ashoka
  - **Image** : docker.io/library/influxdb
  - **Command** : influxd [tanpa terminal]
  - **Port mapping**:
      - host:8086, container:8086 tcp
  - **Volume**:
      - /home/ashoka/Apps/podman/flux2/data -> /var/lib/influxdb2 writable shared
      - /home/ashoka/Apps/podman/flux2/config -> /etc/influxdb2 writable shared

- Environment variabel:
  - DOCKER_INFLUXDB_INIT_MODE=setup
  - DOCKER_INFLUXDB_INIT_USERNAME=ashoka
  - DOCKER_INFLUXDB_INIT_PASSWORD=Ashokani
  - DOCKER_INFLUXDB_INIT_ORG=lokal
  - DOCKER_INFLUXDB_INIT_BUCKET=test

- Buat folder berikut:
```
.
└── flux2
    ├── config
    └── data
```

- **Create and run**