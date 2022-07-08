# Systemd Timers

At the moment we have only timer: `pzserver00`. You can find it at `.local/share/systemd/user/` (create it if this directory doesn't exist). On this directory you can find two files related to service `pzserver00`:

``` shell
.  ..  pzserver00.service  pzserver00.timer
```
This is the contents of `pzserver00.service`:

``` shell
[Unit]
Wants=pzserver00.timer

[Service]
Type=oneshot
ExecStart=/home/rotter/scripts/pzserver_message.sh

[Install]
WantedBy=multi-user.target
```

And this is for `pzserver00.timer` where you can set the schedule in a easy way:

``` shell
[Unit]
Description=Project Zomboid Restart Schedule
Requires=pzserver00.service

[Timer]
Unit=pzserver00.service
OnCalendar=*-*-* 23,03,07,11,15,19:30,45,55,59:00
OnCalendar=*-*-* 00,04,08,12,16,20:00:00
AccuracySec=1us
RandomizedDelaySec=0

[Install]
WantedBy=timers.target
```

For running this service just only execute this command:

``` shell
$ systemctl --user start  pzserver00 --all  --full
```

Check it out if everything is running okay as expected:

``` shell
$ journalctl --user -f -u pzserver00
Jun 28 07:59:15 bunkergames pzserver_message.sh[13741]: Don't do anything...(241 minutes before 12:00)
Jun 28 07:59:15 bunkergames pzserver_message.sh[13741]: Don't do anything...(481 minutes before 16:00)
Jun 28 07:59:15 bunkergames pzserver_message.sh[13741]: Don't do anything...(721 minutes before 20:00)
Jun 28 07:59:15 bunkergames systemd[1218]: Finished pzserver00.service.
Jun 28 08:00:10 bunkergames systemd[1218]: Starting pzserver00.service...
Jun 28 08:00:16 bunkergames pzserver_message.sh[15647]: Info: Game server is restarting
Jun 28 08:00:16 bunkergames pzserver_message.sh[15647]: Don't do anything...(240 minutes before 12:00)
Jun 28 08:00:16 bunkergames pzserver_message.sh[15647]: Don't do anything...(480 minutes before 16:00)
Jun 28 08:00:16 bunkergames pzserver_message.sh[15647]: Don't do anything...(720 minutes before 20:00)
Jun 28 08:00:16 bunkergames systemd[1218]: Finished pzserver00.service.
```

Yep! it works! Remember you need to put your schedule script (`pzserver_message.sh` in this example) below the service section of `pzserver00.service`.

If you make changes, remember to stop first the service, make changes and finally:

``` shell
$ systemctl --user daemon-reload
```

Finally if you want to check if systemd timer is active:

``` shell
$ systemctl --user list-timers
NEXT                         LEFT         LAST                         PASSED    UNIT             ACTIVATES
Tue 2022-06-28 11:30:00 CEST 3h 2min left Tue 2022-06-28 08:00:10 CEST 27min ago pzserver00.timer pzserver00.service

1 timers listed.
```

Use `systemctl --user list-units --type=target` to list the targets for the user systemd.

Also, be sure to run `loginctl enable-linger USERNAME`, otherwise the services with will be started on user login instead of boot.

## References

- [systemd/Timers](https://wiki.archlinux.org/title/Systemd/Timers)
- [How to run systemctl service as user](https://superuser.com/questions/1215631/how-to-run-systemctl-service-as-user)
- [How to watch output from systemd service?](https://unix.stackexchange.com/questions/475292/how-to-watch-output-from-systemd-service)
- [[Solved] systemd timers do not auto start as user services](https://bbs.archlinux.org/viewtopic.php?id=213968)
- [Systemd timer scheduled for first start without manually starting or rebooting the server](https://stackoverflow.com/questions/70348320/systemd-timer-scheduled-for-first-start-without-manually-starting-or-rebooting-t)
- [Systemd: how to start service after another service started](https://serverfault.com/questions/1053369/systemd-how-to-start-service-after-another-service-startedurl)
- [Why doesn't my systemd user unit start at boot?](https://unix.stackexchange.com/questions/251211/why-doesnt-my-systemd-user-unit-start-at-boot)
- [loginctl enable-linger/disable-linger ... but reading linger-status?](https://serverfault.com/questions/846441/loginctl-enable-linger-disable-linger-but-reading-linger-status)
