# Arch Linux Self Hosted Game Server Guide

## Server Specs

* Barcelona, Europe
* Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz - 2t/12c - 3.1GHz
* 32GB DDR4 2400 MHz
* 1x512gb NVME

This machine hosts:

* [Rust Legacy](https://d3adwolf.github.io/rustlegacy/index-english.html)
* [Project Zomboid]

## X99 Chinese Motherboards



## Installing Arch Linux

### Configure SSH


### Qemu

* [VGA and other display devices in qemu](https://www.kraxel.org/blog/2019/09/display-devices-in-qemu/)
* [Host mouse pointer not aligned with guest mouse pointer in Qemu VNC](https://unix.stackexchange.com/questions/555082/host-mouse-pointer-not-aligned-with-guest-mouse-pointer-in-qemu-vnc)

####  Docker breaks existing bridge I use for KVM/QEMU

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

### ReactOS

* [ReactOS on QEMU](https://reactos.org/wiki/QEMU)

### Which Windows?

* [Windows 10 or Windows Server for Game Servers](https://www.reddit.com/r/servers/comments/kl0ze4/windows_10_or_windows_server_for_game_servers/)
* [What is the lightest version of Windows 10? ](https://techtrix.co/what-is-the-lightest-version-of-windows-10/)
* [Windows LTSC | The Best Version of Windows 10](https://www.youtube.com/watch?v=8dyhoh7u8JA)
* [Introducing Tiny 10!](https://www.reddit.com/r/windows/comments/ml538t/introducing_tiny_10/)
* [Windows10Debloater](https://github.com/Sycnex/Windows10Debloater): Script/Utility/Application to debloat Windows 10, to remove Windows pre-installed unnecessary applications, stop some telemetry functions, stop Cortana from being used as your Search Index, disable unnecessary scheduled tasks, and more...
