Title: Running Screen in a Sudo Session
Category: Tips
Date: 2012-07-07

If you use `sudo su` to become a different user, and then try to create a
screen session for the new user, it gives this error message:

    Cannot open your terminal '/dev/pts/1' - please check.

The problem is that your original user still owns the pseudo terminal.  The
best solution I've seen is a hack, but it works:

    :::bash
    sudo su - notjeff
    script /dev/null
    screen

