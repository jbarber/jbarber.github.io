+++
title = 'journalctl && date'
date = 2024-09-27T11:23:51+01:00
draft = false
+++
After years of running things in various PaaS (Heroku, GCP, AWS) where
log capture and viewing is handled by some web service, I've just started
hacking on some projects where I want to host them on linux, start them using
`systemd`, and so the logs are all available via the `journalctl` command.

I stopped being a full-time unix sysadmin back in 2015 - before `systemd` became
more mainstream - so it's interesting learning this new system.

One thing I wanted to do was to view all the logs for a service during a
time range. `journalctl` lets you do this with the `-S` (or
`--since` argument) which takes an ISO formatted timestamp. I didn't want to do the
thinking about how long ago something happened and having to turn it into the
right format, so I was wondering if it was possible to translate a human
formatted relative time into an absolute timestamp on the command line.

Turns out it is, with the `date` command (well, at least with the GNU version of `date`):
```
journalctl --unit foobar --since $(date -d "20 minutes ago" -I)
```

This was surprising to me, as I assumed `date` would want the time to be
given in a `sprintf` compatible way - but no!

The other `date` command I perennially use is `date +%s` to give the time since
the start of the unix epoch in seconds - going the other way is somewhat weird
because you have preceed the value with an `@`:
```
$ date -d '20 minutes ago' +%s
1727433670
$ date -d @1727433670
Fri 27 Sep 11:41:10 WEST 2024
```
