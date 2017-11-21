Title: Copying time machine backups to a new disk
Category: Tips
Date: 2017-03-12

Copying a Time Machine backup from one disk to another is difficult.  The official instructions say to copy using Finder. While this may work for a small time machine backup, it does not work well if you have been using time machine for several computers for several years.  In particular, Finder does not give any feedback on how it is progressing.

The next thing I tried was `cp`. The only problem is that `cp` copies hard-linked files as if they were separate files.  Time Machine uses hard links to dedupe files that exist in multiple backups, so without the hard links, the backups will be much larger.

I ended up using rsync.  The version of rsync that comes with Mac OS does not handle extended attributes correctly, so you will need to install rsync version 3:

    :::bash
    brew install homebrew/dupes/rsync

Assuming your old backup is on a disk named `delorean` and your new disk is `tardis`:

    :::bash
    sudo /System/Library/Extensions/TMSafetyNet.kext/Contents/Helpers/bypass rsync -vaPHAXEN --fileflags /Volumes/delorean/Backups.backupdb /Volumes/tardis/Backups.backupdb


    sent 1,073,178,499,541 bytes  received 113,970,649 bytes  42,031,386.51 bytes/sec
    total size is 1,316,614,962,045  speedup is 1.23
    rsync error: some files/attrs were not transferred (see previous errors) (code 23) at main.c(1249) [sender=3.1.2]


