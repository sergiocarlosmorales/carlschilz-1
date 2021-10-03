---
title: "Pi-hole unable to start as container"
date: 2021-10-03T14:57:21-06:00
draft: false
tags:
 - synology
 - container
 - docker
 - apt-get

categories:
 - Hard learned lessons
---
While running Pi-hole in a container, suddenly it was unable to start successfully. I was attempting to start it 
from Synology's Docker UI.

It would go into a loop, crashing upon startup. Logs would show:
`Error: Unable to update package cache. Please try "apt-get update"[cont-init.d] 20-start.sh: exited 1.`

Nothing had changed (that I'm aware of) in the network or host. Updated the image to the latest, but error remained.

Not much help from Google, most of the results talked about this error during installation but didn't seemed to 
apply in my context.

Using the terminal, I found the container ID with: `sudo docker container ls --all` (I have a separate user 
for docker, creatively named `docker` so that's why I had to `sudo`).

Then I removed the container with: `sudo docker container 6d3f61095572 remove`. Replace the ID with the one you got 
from the previous step.

Re-ran the same command to start up the container as I was doing before (that was failing) and this time it worked 
like a charm.

## Lesson learned:
None, I don't know what caused it. 

I was confident in deleting the container because as part of the command to  start it up I provide volumes to store 
its configuration. So I know I could re-create it easily.

You know what they say: treat modern IT infrastructure more like cattle, instead of pets.