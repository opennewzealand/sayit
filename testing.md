---
layout: default
title: Testing
---

Testing Django
==============
To run tests on the Django side of SayIt, simple run:

    ./manage.py test speeches instances login_token

From within the Python virtualenv.

Some of the tests use a Selenium driven browser; these Selenium tests currently
use Firefox, so make sure you have Firefox installed.

If you're on a headless server, eg: in a vagrant box, you'll need to install
the iceweasel and xvfb packages to make this work (see the commented out
section of `/conf/packages` for the packages you'll need to install)

After installing them, start Xvfb with:

    Xvfb :99 -ac &

And export your display variable:

    export DISPLAY=:99

You might want to make that happen at every startup with the appropriates lines in
`/etc/rc.local` and `~/.bashrc`
