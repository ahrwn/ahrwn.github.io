---
title: Using Gitea as Your Docker Hub
date: 2023-01-25
teaser: Forget the bloated whale we are adding docker containers to gitea
slug: 2023-01-25-gitea-docker
---

I have a bit of an unconventional way to deploy docker containers - I saw that Docker Hub wanted to charge montly fees when I am simply pushing and pulling images infrequently, so I decided to do this via my Gitea instance.

Create your Dockerfile in your project to properly deploy the app.

```
docker build -t app-name .
```

login to Docker and push the image:

```
docker login git.yoursite.com
docker tag app-name:latest git.yoursite.com/username/app-name:latest
docker push git.yoursite.com/username/app-name:latest
```

On your server:

```
docker pull git.yoursite.com/username/app-name:latest
docker run -d --name app-name -p 42069:42069 git.yoursite.com/username/app-name:latest
```

From there, you can configure nginx as a reverse proxy to the port you chose to run the app, set up a domain and your ssl certs, and you are off to the races.
