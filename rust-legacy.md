# Rust Legacy

https://github.com/d3adwolf/rustlegacy
Omg! https://www.legionrust.com/

https://rust.fandom.com/wiki/Console_(Legacy)

Setup hardrive with raw format and 8G of free space.

``` shell
qemu-img create -f raw rustserver.img 8G
```

Alternative you can create qcow2 format but you can't mount as iso later:

``` shell
qemu-img create -f qcow2 rustserver-reactos.qcow2 8G
```

Install ReactOS:

``` shell
qemu-system-i386 -cpu host -smp 2 -enable-kvm -m 4G -drive if=ide,index=0,media=disk,file=rustserver-reactos.qcow2 -drive if=ide,index=2,media=cdrom,file=ReactOS-0.4.14-release-14-g210d602.iso -boot order=d -serial file:ReactOS.log -name rustserver -display vnc=0.0.0.0:0 -nic bridge,br=br0 -usbdevice tablet &
```

Once ReactOS is installed, you need to install **Monot Net Development Framework** and **DirectX End User Runtimes** from the **ReactOS Package Manager** (located at Desktop).


https://www.cyberciti.biz/faq/linux-kvm-vnc-for-guest-machine/

https://wiki.gentoo.org/wiki/QEMU/Options#Display_options

és necessary?

``` shell
sudo pacman -S libvirt
```

Això ha funcionat, llegint la documentació següent:

* [Network bridge](https://wiki.archlinux.org/title/Network_bridge#No_networking_on_hosted_servers_after_bridge_configuration)
* [Network stop working after adding bridge interface](https://serverfault.com/questions/868189/network-stop-working-after-adding-bridge-interface)

Instalem el paquet `bridge-utils`:

``` shell
sudo pacman -S bridge-utils
```

A continuació creem el bridge port `br0` i l'enllaçem amb el port físic `enp6s0`:

``` shell
sudo brctl addbr br0
sudo brctl addif br0 enp6s0
```

Mostrem que tot és correcte:

``` shell
sudo brctl show
bridge name     bridge id               STP enabled     interfaces
br0             8000.621e2e36e88b       no              enp6s0
```

Fem un delete i flush de la xarxa física (si té assignada una ip prèviament):

``` shell
sudo ip addr del <192.168.x.x> dev enp6s0
sudo ip addr flush dev enp6s0
```

Afegim la ip al bridge `br0` i afegim la route:

``` shell
sudo ip addr add 192.168.1.3/24 dev br0
sudo ip route add default via 192.168.1.1
```

Ara és el moment de crear la màquina virtual de ReactOS que portarà el server dins:

``` shell
qemu-system-x86_64 -cpu host -smp 4 -enable-kvm -m 4G -drive file=rustserver.img,format=raw -boot c -name rustserver -display vnc=0.0.0.0:0 -nic bridge,br=br0,model=virtio-net-pci &
```

Una vegada configurada la xarxa i engegat la màquina virtual, podem veure com queda (`ip address`):

``` shell
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: enp6s0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel master br0 state UP group default qlen 1000
    link/ether 00:e0:4c:b3:04:19 brd ff:ff:ff:ff:ff:ff
3: br0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default qlen 1000
    link/ether 62:1e:2e:36:e8:8b brd ff:ff:ff:ff:ff:ff
    inet 192.168.1.3/24 scope global br0
       valid_lft forever preferred_lft forever
4: tap0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel master br0 state UNKNOWN group default qlen 1000
    link/ether fe:c7:26:49:3e:d1 brd ff:ff:ff:ff:ff:ff
    inet6 fe80::fcc7:26ff:fe49:3ed1/64 scope link
       valid_lft forever preferred_lft forever
```

Si tornem a fer el `brctl show`, veurem el `tap0` com a interface:

``` shell
bridge name     bridge id               STP enabled     interfaces
br0             8000.621e2e36e88b       no              enp6s0
                                                        tap0
```

Si volem muntar la iso en local:

``` shell
sudo mount -o loop,offset=$((512*2048)),rw,uid=`id -u`,gid=`id -g` rustserver.img mnt/
```

Recorda que el valor de l'`offset` el podem trobar mitjançant un `sudo fdisk -l rustserver.img`, multiplicant la mida del sector pel valor d'inici del device:

``` shell
Disk rustserver.img: 6 GiB, 6442450944 bytes, 12582912 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x2a0f41f9

Device          Boot Start      End  Sectors Size Id Type
rustserver.img1 *     2048 12582863 12580816   6G  b W95 FAT32
```

### Issues using ReactOS

* It last 10 minutes until rust server starts!
* Hi ha un moment que el cursor del vnc es queda congelat al centre de la pantalla quan el servidor de
* How to encrypt qemu vnc server? (unencrypted connection)

### Windows 7/10 as server

* [Introducing Tiny 10!](https://www.reddit.com/r/windows/comments/ml538t/introducing_tiny_10/)
* [Tiny 7 Windows](https://archive.org/details/Tiny7)
* [Windows10Debloater](https://github.com/Sycnex/Windows10Debloater)

``` shell
qemu-img create -f qcow2 rustserver-tiny10.qcow2 8G
```

``` shell
qemu-system-x86_64 -cpu host -smp 2 -enable-kvm -m 4G -drive if=ide,index=0,media=disk,file=rustserver-tiny10.qcow2 -drive if=ide,index=2,media=cdrom,file=Tiny10.iso -boot order=d -serial file:Tiny10.log -name rustserver -display vnc=0.0.0.0:1 -nic bridge,br=br0 -usbdevice tablet &
```

Per muntar i desmuntar: [How to mount a qcow2 disk image](https://gist.github.com/shamil/62935d9b456a6f9877b5)

[How To Mount A Windows NTFS Disk In Linux](https://www.rootusers.com/how-to-mount-a-windows-ntfs-disk-in-linux/)
[Can't Mount NTFS drive "The disk contains an unclean file system"](https://askubuntu.com/questions/462381/cant-mount-ntfs-drive-the-disk-contains-an-unclean-file-system)
[https://isofiles.bd581e55.workers.dev/](https://isofiles.bd581e55.workers.dev/): to obtain LTSC Windows 10

``` shell
sudo modprobe nbd max_part=8
sudo qemu-nbd --connect=/dev/nbd0 rustserver-tiny10.qcow2
sudo pacman -Ss ntfs-3g
sudo mount /dev/nbd0p2 mnt/
sudo umount mnt/
sudo qemu-nbd --disconnect /dev/nbd0
```

``` shell
qemu-system-x86_64 -cpu host -smp 4 -enable-kvm -m 6G -drive file=rustserver-win7.img,format=raw -boot d -name rustserver -display vnc=0.0.0.0:1 -nic bridge,br=br0 -usbdevice tablet -cdrom win7.iso
```

Ara mateix, el que ha donat millors resultats (Tiny7):

``` shell
qemu-img create -f qcow2 rustserver-tiny7.qcow2 8G
```
Instal·lem:

``` shell
qemu-system-x86_64 -cpu host -smp 2 -enable-kvm -m 2G -drive if=ide,index=0,media=disk,file=rustserver-tiny7.qcow2 -drive if=ide,index=2,media=cdrom,file=files/Tiny7.iso -boot order=d -name rustserver -display vnc=0.0.0.0:0 -usbdevice tablet -device VGA,vgamem_mb=256 -nic bridge,br=br0 &
```

Un cop instal·lat, ja podem executar:

``` shell
qemu-system-x86_64 -cpu host -smp 2 -enable-kvm -m 2G -drive if=ide,index=0,media=disk,file=rustserver-tiny7.qcow2 -boot order=c -name rustserver -display vnc=0.0.0.0:0 -usbdevice tablet -device VGA,vgamem_mb=256 -nic bridge,br=br0 &
```

Per executar Rust només et caldrà el runtime de Mono:

``` shell
wget -c https://download.mono-project.com/archive/6.12.0/windows-installer/mono-6.12.0.107-gtksharp-2.12.45-win32-0.msi
```

Per muntar i desmuntar el disc (i fer canvis):

``` shell
sudo qemu-nbd --connect=/dev/nbd0 rustserver-tiny7.qcow2
sudo fdisk -l /dev/nbd0
sudo mount /dev/nbd0p2 mnt/
```

i fiquem a dins el que necessitem...

per desmuntar:

``` shell
sudo umount mnt
sudo qemu-nbd --disconnect /dev/nbd0
```

## Need to test if this works...

[How To Connect QEMU Host-Guest Networks Made Easy ](https://www.ubuntubuzz.com/2021/12/how-to-connect-qemu-host-guest-networks-made-easy.html)

Dit d'una altra manera, és possible establir un redireccionament udp d'un port a un altre sense fer servir el bridge?

O un altre opció: [quickhowto: qemu networking using vde, tun/tap, and bridge](https://selamatpagicikgu.wordpress.com/2011/06/08/quickhowto-qemu-networking-using-vde-tuntap-and-bridge/)

## Console commands

https://rust.fandom.com/wiki/Console_(Legacy)

## Rust Legacy Buster

- [RustBuster files (client/server)](https://fougerite.com/resources/categories/rustbuster.8/)
- [Fougerite Official](https://fougerite.com/resources/categories/fougerite.3/)
- [Full Server Fougerite1.76B + RustBuster2.09 (531.9 Mb)](https://fougerite.com/resources/categories/rust-files.7/)

## References

- [QEMU Documentation/Networking](https://wiki.qemu.org/Documentation/Networking#How_to_create_a_virtual_network_device.3F)
- [Network bridge](https://wiki.archlinux.org/title/Network_bridge#No_networking_on_hosted_servers_after_bridge_configuration)
- [Network stop working after adding bridge interface](https://serverfault.com/questions/868189/network-stop-working-after-adding-bridge-interface)