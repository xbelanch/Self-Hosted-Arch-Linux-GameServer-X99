# Games supported

## Rust Legacy

https://www.cyberciti.biz/faq/linux-kvm-vnc-for-guest-machine/

https://wiki.gentoo.org/wiki/QEMU/Options#Display_options

qemu-system-x86_64 -cpu host -enable-kvm -m 2048 -hda reactos.raw -boot c -k en-us -name RustLegacyServer -vga qxl -display vnc=0.0.0.0:1

sudo mount -o loop,offset=$((512*2048)),rw,uid=`id -u`,gid=`id -g` reactos.raw mnt/