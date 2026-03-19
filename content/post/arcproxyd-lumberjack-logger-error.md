+++
Categories = ["Quick Notes"]
Description = "Fix  Unable to set up lumberjack logger: can't open file: can't open new logfile: open /var/opt/azcmagent/log/arcproxy.log: permission denied."
Tags = ["Linux","Ubuntu","Azure ARC"]
date = "2026-03-19T07:28:21-06:00"
title = "Fix arcproxyd lumberjack logger error: can't open file"
+++

The Azure ARC client on a few of our Linux systems was spamming syslog:

```
Mar 19 07:12:03 www2 systemd[1]: Started Azure Arc Proxy.
Mar 19 07:12:03 www2 arcproxy[1660872]: Unable to set up lumberjack logger: can't open file: can't open new logfile: open /var/opt/azcmagent/log/arcproxy.log: permission denied
Mar 19 07:12:03 www2 systemd[1]: arcproxyd.service: Main process exited, code=exited, status=1/FAILURE
Mar 19 07:12:03 www2 systemd[1]: arcproxyd.service: Failed with result 'exit-code'.
Mar 19 07:12:08 www2 systemd[1]: arcproxyd.service: Scheduled restart job, restart counter is at 116663.
Mar 19 07:12:08 www2 systemd[1]: Stopped Azure Arc Proxy.
```
The ARC client's `arcproxyd` service was failing and restarting every five seconds. Looking at the service file, the service runs as:

```
User=arcproxy
Group=himds
```
`/var/opt/azcmagent/log` was not writeable by the `himds` group.

Fixing the directory group permissions and restarting the `arcproxyd` service resolved the issue:

```
chmod 775 /var/opt/azcmagent/log 
sudo systemctl restart arcproxyd
```
