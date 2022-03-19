# Issues


## Docker breaks existing bridge I use for KVM/QEMU [Solved]

When I start the Docker service, my VM no longer has network connectivity ([https://bbs.archlinux.org/viewtopic.php?id=233727](https://bbs.archlinux.org/viewtopic.php?id=233727)).

1. Cal activar el service de iptables:  `sudo systemctl start iptables`.
2. Cal editar el docker.service: `EDITOR=vim sudo -E systemctl edit docker.service`
3. Incloure aquestes dues línies:

  ``` shell
  #[Service]
  ExecStartPre=/usr/bin/iptables -D FORWARD -p all -i br0 -j ACCEPT
  ExecStartPre=/usr/bin/iptables -A FORWARD -p all -i br0 -j ACCEPT
  ```

4. Restart docker:

  ``` shell
  sudo systemctl daemon-reload
  sudo systemctl stop docker
  sudo systemctl start docker
  ```

And it works!

## Docker-compose: must disable firewall and restart docker service when docker-compuse up [Solved]

https://stackoverflow.com/questions/60554679/docker-compose-must-disable-firewall-and-restart-docker-service-when-docker-com

Everytime I reboot my computer, I got this message when I do a docker-compose up:

    Creating network "dockerlemp_default" with the default driver

    ERROR: Failed to program FILTER chain: iptables failed: iptables --wait -I FORWARD -o br-74bd0d3628e8 -j DOCKER: iptables v1.6.1: Couldn't load target `DOCKER':No such file or directory

    Try `iptables -h' or 'iptables --help' for more information.

 docker-compose up fails if network attached to container is removed #5745: https://github.com/docker/compose/issues/5745

 Solved:

sudo  iptables -N DOCKER
