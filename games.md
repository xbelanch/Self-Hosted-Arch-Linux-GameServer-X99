# Games supported

## Rust Legacy

https://www.cyberciti.biz/faq/linux-kvm-vnc-for-guest-machine/

https://wiki.gentoo.org/wiki/QEMU/Options#Display_options

qemu-system-x86_64 -cpu host -enable-kvm -m 2048 -hda reactos.raw -boot c -k en-us -name RustLegacyServer -vga qxl -display vnc=0.0.0.0:1


With brigdge:

és necessary? No, no és necessari
sudo pacman -S libvirt

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


### Issues

* Hi ha un moment que el cursor del vnc es queda congelat al centre de la pantalla.
* How to encrypt qemu vnc server? (unencrypted connection)