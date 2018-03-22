---
layout: default
title: "Docker Intro with Tony"
---

## Informal Workshop: Docker (Tony)

Docker is a lightweight containerisation application ecosystem, a Virtual-Machine style of separating an OS for different purposes, such as to run applications or dependences on the same OS but in separate containers that don't interfere with each other. I'd used Docker a little before joining MYOB but never really understood their true purpose and usages.

Key learnings:
- On Mac, to get Docker: Install with Cask, extension from Home-brew
- Docker doesn’t run natively on Mac OS X, it is designed for linux LXC containers
- Docker does what a VM does, but not the whole thing. Isolates apps from each other. Lightweight - virtualises from the OS onwards.
- Each container should run one app
- Typical size is usually in low 100mb’s, since it doesn’t need to be huge because it’s not the whole OS.
- Run Docker now from Mac OS. It’s now a client VM that’s running for you. Linux VM on my Mac.
- Docker server and client you can see by doing ‘docker version’. Connect via client that goes INTO server. This is ‘Docker Engine’. Other docker tools too. Also: Docker Hub - like GitHub for docker images. There’s a whole ecosystem, it’s not just containers.
- Building an image: refer to Tony's blog - https://tsukiyo.io/posts/what-is-docker/
- Next steps: Installing Node
- Then: docker build -t tests-image .
- If you want to make changes to your app thats running in the container, you would usually remove that container, make changes, then re-build a new container and use that new one.

***

[[Top]](#top)
