---
title: "Dev Help Queue"
layout: post
date: 2021-05-15 15:06
tag:
    - ReactJS
    - SpringBoot
    - MySQL
    - Docker
    - Kubernetes
image: /assets/images/portfolio/icons/default.png
headerImage: true
projects: true
hidden: false
description: "A dockerized help desk application"
jemoji: '<img class="emoji" title="plant-id" alt="plant-id" src="/assets/images/portfolio/icons/default.png" height="20" width="20" align="absmiddle">'
author: johndoe
externalLink: false
---

{:refdef: style="text-align: center;"}
![Plant ID](/assets/images/portfolio/dev-help-queue.png)
{: refdef}

Dev Help Queue was more of an experimental project where the client wanted to test the proof of concept for a microservices architecture using different technologies to build a CRUD application.

The project was dockerized to host Frontend, Backend and Database as separate containers that can be orchestrated with either Docker Compose or a more scalable K8s cluster.

## Dependencies

- **Frontend:** [ReactJS](https://reactjs.org/)
- **Material UI:** [React Material UI](https://material-ui.com/)
- **API:** [Spring Boot](https://spring.io/projects/spring-boot)
- **Database:** [MySQL](https://www.mysql.com/)
- **Micro-services:** [Docker](https://www.docker.com/) with [Kubernetes](https://kubernetes.io/)
