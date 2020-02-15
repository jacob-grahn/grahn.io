---
title: "Hosting Ghost With Docker"
date: "2017-10-24T20:05:08-04:00"
author: "Jacob Grahn"
lastmod: "2020-02-13T19:10:15-05:00"
---

# Ghost

Ghost is open source blogging sofware that kind-of sort-of competes with WordPress, which is open source *world domination* software.

# Let someone else host it

It is absolutely worth the extra bit of money to have someone else go through the trouble of hosting Ghost for you. For those of you who are smart (unlike myself), here are a couple of great options.

- [ghost.org](https://ghost.org/)
Hosting by the people who wrote Ghost. Cool! Their cheapest plan is $29 a month, or $19 a month if you pay yearly.
- [runkite.com](https://runkite.com)
Hosting by some people who like kites. Cool! Their cheapest plan is $5 a month. As of right now they are running an old version of Ghost. This is a great cheap option if you don't share my phobia of outdated software.

# Host it yourself

Ghost's [installation instructions](https://docs.ghost.org/v1/docs/install) aren't too bad if you're used to doing this sort of thing. But it's not exactly the easist thing ever.

Instead, a simpler solution is to install docker, and then run the ghost image.
```
docker run --name ghost -d \
-p 80:3268 \
-v /data/ghost:/var/lib/ghost/content \
ghost:alpine
```

The final step is to set this image up to run whenever the server reboots. I just added the command to my crontab with @reboot as the timing.

Have fun Ghosting!
