---
title: "Sudden failure on Synology NAS DSM login"
date: 2021-03-02T20:57:21-06:00
draft: false
tags:
 - synology
 - login
 - 2fa
 - failure

categories:
 - Hard learned lessons
---
Out of the sudden I was unable to authenticate to my Synology NAS via the Web UI (DSM).

I was able to enter username & password, but the system was unable to verify my 2FA token.

I made sure my credentials were right (I use a password manager, so manual error was unlikely).

I SSHd into the NAS using password authentication, it worked! my credentials were right!.

From a quick glance via SSH things looked ok, my initial suspicion was time drift causing issues with my 
time-based 2FA token, but was able to verify this was not the case.

Ended up resetting Administrator access using the steps [here](https://www.synology.com/en-us/knowledgebase/DSM/tutorial/General_Setup/How_to_reset_my_Synology_NAS).

After doing so, I regained access via the UI, and browsed to the Control Panel, there I noticed a warning that the 
system was out of space.

I knew I had several terabytes left in the NAS.

Via SSH I logged in and found that indeed `/dev/md0` was at 100%

Turns out there was a big log file from a long running task that I kicked off the night before.
It was big enough to fill `/dev/md0` which is Synology DSM's partition.
Because it was out of space, it was causing all sorts of issues.

The actual drives still had terabytes left, but `/dev/md0` was full.

## Lesson learned:
`/dev/md0` is a small partition, ensure it doesn't get full otherwise expect very erratic behavior by Synology's DSM.

After knowing what to look for in Google, clearly I was not the first one to encountered the same 
[problem](https://www.reddit.com/r/synology/comments/i8gpbe/help_nas_acting_up_devmd0_full/).
