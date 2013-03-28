---
layout: default
title: Developing
---

Running SayIt
=============

Notes on running the SayIt code.

Management commands
-------------------

There is only one management command so far, `populatespeakers` which is given
a popit instance, hostname, and API version, and a SayIt instance label, and
imports all the speakers from that popit instance into the sayit instance.

This overwrites any speakers currently in the db matching the data it gets from
PopIt, and adds any new ones it finds too. It doesn't delete any speakers.

You need to run this command manually whenever you add a new speaker that you
want to appear on the site, as it doesn't run on cron yet. It gives you a
little print out of what it did with each speaker on the command line.

Celery
------

There is code included in `conf/spoke-celery-daemon.ugly` to run a celery
worker as a daemon.

    Usage: /etc/init.d/celeryd {start|stop|force-reload|restart|try-restart|status}

Replace celeryd with the name you give your daemon.

Logfiles
--------

The main site log files are in `path to vhost/logs`, celery logs are in `path
to vhost/celery.w1.log`

