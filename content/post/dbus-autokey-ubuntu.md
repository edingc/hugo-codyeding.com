+++
Categories = ["Quick Notes"]
Description = "Create a DBUS service file to remedy AutoKey pasting blank lines."
Tags = ["Linux","Ubuntu","AutoKey","D-BUS"]
date = "2016-09-08T08:34:21-06:00"
title = "Fix AutoKey pasting blank lines in Ubuntu"
aliases = [
    "/dbus-autokey-ubuntu/"
]
+++



I have been using [AutoKey](https://code.google.com/archive/p/autokey/) on my Linux desktop to provide automated text insertion capabilities similar to what I get from [AutoHotKey](https://autohotkey.com/) on my Windows systems. I prefer to keep things like my email and ticket signatures stored as hotkeys so I can more granularly choose when to use them.

The version of AutoKey included in the Ubuntu repositories has an issue where blank lines are inserted rather than the expected text. 

Googling yielded the solution from an [AskUbuntu post](http://askubuntu.com/questions/331207/autokey-inserts-blank-lines).

To fix the blank line issue, create a file named `org.autokey.service` at `/usr/share/dbus-1/services` with the following content:

```
[D-BUS Service] 
Name=org.autokey.service 
Exec=/usr/bin/autokey
```

After creating the file with the content above, kill and restart the AutoKey process or just reboot your computer. AutoKey should now properly insert text instead of blank lines!

D-BUS handles all of the communication between different processes running on the Linux system. It appears the D-BUS service for AutoKey is not configured correctly upon installation.
