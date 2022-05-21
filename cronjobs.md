# Cronjobs

References:

- [cronjobs](https://docs.linuxgsm.com/configuration/cronjobs)
- [cron - arch docs](https://wiki.archlinux.org/title/cron#Installation)

Installation:

``` shell
sudo pacman -S cronie
sudo systemctl enable cronie.service
sudo systemctl start cronie.service
```

For editing:

``` shell
EDITOR=nano crontab -e
```

For listing:
``` shell
crontab -l
```

Dummy example for Arma 3:

``` shell
*/5 * * * * /usr/local/bin/yarcon -d -H 0.0.0.0 -p 2301 -P password -c "say -1 5 minutes before server will restart" -b > /dev/null 2>&1
*/8 * * * * /usr/local/bin/yarcon -d -H 0.0.0.0 -p 2301 -P password -c "say -1 2 minutes before server will restart" -b > /dev/null 2>&1
*/9 * * * * /usr/local/bin/yarcon -d -H 0.0.0.0 -p 2301 -P password -c "say -1 1 minute before server will restart" -b > /dev/null 2>&1
*/10 * * * * /usr/local/bin/yarcon -d -H 0.0.0.0 -p 2301 -P password -c \#restart -b > /dev/null 2>&1
```

TODO:

Execute a script that checks if gameserver is up and running. Send messages if there're players.