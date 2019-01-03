This subdirectory contains Mark2 releases which consist of two components:

1. A BSP (board support package) will be a tarball with a subdirectory named
like mk2-bsp-* which contains:
a. BOOT.BIN and image.ub (which go into boot partition)
b. .deb packages which install into the rootfs, which includes things like
  - armsoc graphics driver/library
  - setup package for gpio, alsa, x11, etc.
  - mali GPU library


To update mark2 with a new BSP release, the following should be done
on a running system:
  1. Download the mk2-bsp* release and unpack it.
  2. As root, mount the boot partition and copy BOOT.BIN and image.ub to it:
  $  cp mk2-bsp-*/BOOT.BIN /mnt/BOOT/
  $  cp mk2-bsp-*/image.ub /mnt/BOOT/
  2. cp mk2-bsp-*/libhwaccel64.so /usr/local/lib/
  3. cp mk2-bsp-*/mali.ko /usr/modules/`uname -r`/
  4. dpkg -i mk2-bsp-*/*.deb



2. An Aaware release will be a tarball with a subdirectory named like mk2-aaw-*
which will have debian packages that can be installed in the rootfs:
  - 3 driver packages (*aawdriver*)
  - aawutil package
  - aawscd package
  - aawscdconf package (there may be a choice of aawsdconf packages for
    different mark2 configurations)

To update mark2 with a new Aaware release, the following should be done
on a running system:
 - Download the mk2-aaw* release and unpack it.
 - dpkg -i mk2-aaw-*/*driver*.deb
 - dpkg -i mk2-aaw-*/*aawutil*.deb
 - dpkg -i mk2-aaw-*/*aawscd*.deb
 - dpkg -i mk2-aaw-*/*conf*m8*.deb  (install 8-mic config)


To create a bootable SD card with a "full" rootfs snapshot that already has the
BSP and Aaware packages installed on it:
1. Download a rootfs tarball from the Mark2 rootfs download site.
2. Create SDcard partitioned as in A below and plug it into computer
3. Upack file to the ROOTFS partition wherever it is mounted, for example:
$ sudo tar -C /media/ROOTFS --strip-components=2 -xvpf mk2-fullrfs-aawmali-20190102.tgz
4. Copy the release BOOT.BIN and image.ub to the boot partition (where mounted), i.e:
$  cp mk2-bsp-*/BOOT.BIN /media/BOOT/
$  cp mk2-bsp-*/image.ub /media/BOOT/
5. unmount both partitions before removing
6. plug SDcard into Mark2 and turn on power - it should boot to the new image


A. Creating SDcard partitions from Linux i.e. to sd_card inserted in USB:

Partition SD Card
1. Use sudo fdisk /dev/sdb (or whatever dev the sdcard is assigned)
2. delete all existing partitions, i.e commands d 1, etc.
3. create new boot partition 1:  n, p 1, +256M, t C (fat32)
4. create new rootfs partition: n, p 2, enter, enter (fill remaining w/ext4)
5. p should confirm the partitions are similar to:
   Device Boot      Start         End      Blocks   Id  System
/dev/sdb1               1         120      265190    c  W95 FAT32 (LBA)
/dev/sdb2             121        3521     7516210   83  Linux

Create Filesystems
1. $ sudo mkfs.vfat -n BOOT /dev/sdb1
2. $ sudo mke2fs -t ext4 -L ROOTFS /dev/sdb2
3. mount the partitions for installing boot files and rootfs (remove card and
reinsert for auto boot, i.e. /media/BOOT and /media/ROOTFS)
