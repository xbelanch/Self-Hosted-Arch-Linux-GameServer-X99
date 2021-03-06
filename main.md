# Arch Linux Self Hosted Game Server Guide

## Server Specs

* Barcelona, Europe
* Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz - 2t/12c - 3.1GHz
* 32GB DDR4 2400 MHz
* 1x512gb NVME

This machine hosts:

* [Rust Legacy](https://d3adwolf.github.io/rustlegacy/index-english.html)

<!-- * [Project Zomboid] -->

### General

- [Which operating system should I use for a game server?](https://www.reddit.com/r/selfhosted/comments/qzgfco/which_operating_system_should_i_use_for_a_game/)
- [Hosting game servers from home, all one one OS or multiple VM's?](https://www.reddit.com/r/HomeServer/comments/ou4pyt/hosting_game_servers_from_home_all_one_one_os_or/)
- [What are good OS that can host gaming servers that isn’t just lines of code?](https://www.reddit.com/r/homelab/comments/8xszvf/what_are_good_os_that_can_host_gaming_servers/)

## X99 Chinese Motherboards

## Bios TCO Intel watchdog [Need to be tested]

http://www.madore.org/~david/linux/iTCO-wdt-test.html

## Installing Arch Linux

### Configure SSH


### Qemu

* [VGA and other display devices in qemu](https://www.kraxel.org/blog/2019/09/display-devices-in-qemu/)
* [Host mouse pointer not aligned with guest mouse pointer in Qemu VNC](https://unix.stackexchange.com/questions/555082/host-mouse-pointer-not-aligned-with-guest-mouse-pointer-in-qemu-vnc)

####  Docker breaks existing bridge I use for KVM/QEMU

How to Force Docker to release storage space after manual delete of file in volumes and containers? https://stackoverflow.com/questions/44288901/how-to-force-docker-to-release-storage-space-after-manual-delete-of-file-in-volu

Docker-compose: must disable firewall and restart docker service when docker-compuse up: https://stackoverflow.com/questions/60554679/docker-compose-must-disable-firewall-and-restart-docker-service-when-docker-com


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

### Iptables

How to verify if iptables is running or the firewall is activated: https://superuser.com/questions/1124317/how-to-verify-if-iptables-is-running-or-the-firewall-is-activated

### ReactOS

* [ReactOS on QEMU](https://reactos.org/wiki/QEMU)

### Which Windows?

* [Windows 10 or Windows Server for Game Servers](https://www.reddit.com/r/servers/comments/kl0ze4/windows_10_or_windows_server_for_game_servers/)
* [What is the lightest version of Windows 10? ](https://techtrix.co/what-is-the-lightest-version-of-windows-10/)
* [Windows LTSC | The Best Version of Windows 10](https://www.youtube.com/watch?v=8dyhoh7u8JA)
* [Introducing Tiny 10!](https://www.reddit.com/r/windows/comments/ml538t/introducing_tiny_10/)
* [Windows10Debloater](https://github.com/Sycnex/Windows10Debloater): Script/Utility/Application to debloat Windows 10, to remove Windows pre-installed unnecessary applications, stop some telemetry functions, stop Cortana from being used as your Search Index, disable unnecessary scheduled tasks, and more...
- [ReviOS](https://www.revi.cc/)
- [Windows Disc Image Repository](https://isofiles.bd581e55.workers.dev/)
- [Windows10 LTSC 2021 Download link. (Updated)](https://www.reddit.com/r/Windows10LTSC/comments/qw06gr/windows10_ltsc_2021_download_link_updated/)
