### Перименовываем LVM с root-разделом на Debian based системах:
```
lvs
```
  LV   VG        Attr       LSize   Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert  
  root almalinux -wi-ao----   4.89g  
  swap almalinux -wi-ao---- 616.00m  
```
fdisk -l
```
Disk /dev/sda: 6 GiB, 6442450944 bytes, 12582912 sectors  
Disk model: QEMU HARDDISK  
Units: sectors of 1 * 512 = 512 bytes  
Sector size (logical/physical): 512 bytes / 512 bytes  
I/O size (minimum/optimal): 512 bytes / 512 bytes  
Disklabel type: dos  
Disk identifier: 0x071bf10b  

Device     Boot   Start      End  Sectors  Size Id Type  
/dev/sda1  *       2048  1050623  1048576  512M 83 Linux  
/dev/sda2       1050624 12582911 11532288  5.5G 8e Linux LVM  

Disk /dev/mapper/almalinux-root: 4.89 GiB, 5255462912 bytes, 10264576 sectors  
Units: sectors of 1 * 512 = 512 bytes  
Sector size (logical/physical): 512 bytes / 512 bytes  
I/O size (minimum/optimal): 512 bytes / 512 bytes  

Disk /dev/mapper/almalinux-swap: 616 MiB, 645922816 bytes, 1261568 sectors  
Units: sectors of 1 * 512 = 512 bytes  
Sector size (logical/physical): 512 bytes / 512 bytes  
I/O size (minimum/optimal): 512 bytes / 512 bytes  
```
cat /etc/fstab | grep almalinux
```
/dev/mapper/almalinux-root /                       ext4    defaults        1 1  
/dev/mapper/almalinux-swap none                    swap    defaults        0 0  
```
cat /etc/default/grub | grep almalinux
```
GRUB_CMDLINE_LINUX="resume=/dev/mapper/almalinux-swap rd.lvm.lv=almalinux/root rd.lvm.lv=almalinux/swap"
```
vgrename almalinux otus
```
  Volume group "almalinux" successfully renamed to "otus"
```
vgchange -ay
```
2 logical volume(s) in volume group "otus" now active
```
sed -i 's/almalinux/otus/g' /etc/fstab && cat /etc/fstab | grep otus
```
/dev/mapper/otus-root /                       ext4    defaults        1 1  
/dev/mapper/otus-swap none                    swap    defaults        0 0  
```
sed -i 's/almalinux/otus/g' /etc/default/grub && cat /etc/default/grub | grep otus
```
GRUB_CMDLINE_LINUX="resume=/dev/mapper/otus-swap rd.lvm.lv=otus/root rd.lvm.lv=otus/swap"
```
systemctl daemon-reload
mount /dev/mapper/otus-root /mnt
mount --bind /dev /mnt/dev
mount --bind /proc /mnt/proc
mount --bind /sys/ /mnt/sys
mount --bind /run/ /mnt/run
mount --bind /boot/ /mnt/boot
chroot /mnt
```
```
grub2-mkconfig -o /boot/grub2/grub.cfg --update-bls-cmdline
```
```
reboot
```
