---
title: "Podman Node-RED"
date: 2022-04-19T10:05:48+07:00
description: "Membuat container Node-RED di podman menggunakan fedora cockpit"
categories: ["local env"]
tags: ["dev","container", "podman", "node-red"]
---

Membuat container Node-RED di podman menggunakan fedora cockpit
- Masuk menu podman container
- Create container, isi field berikut
  - **Name** : nodered
  - **Owner** : User:ashoka
  - **Image** : docker.io/nodered/node-red
  - **Command** : bash [tanpa terminal]
  - **Port mapping**:
      - host:1880, container:1880 tcp

- Environment variabel:
  - NODE_RED_CREDENTIAL_SECRET=nganu

- **Create and run**
