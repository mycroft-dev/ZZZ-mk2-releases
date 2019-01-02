This subdirectory contains Mark2 releases and possibly 
some rootfs snapshots.

A BSP (board support package) will be a tarball with a subdirectory named like mk2-bsp-*
and it will have .deb packages and some files like BOOT.BIN and image.ub.

An Aaware release will be a tarball with a subdirectory named like mk2-aaw-* and
will have debian packages that can be installed in a working rootfs.

To create a bootable SD card from a full rootfs snapshot:
1) Download a rootfs tarball from the Mark2 rootfs download site. 
2) Create SDcard partitioned as in A below and plug it into computer
3) Upack file to the ROOTFS partition wherever it is mounted, for example:
sudo tar -C /media/ROOTFS --strip-components=2 -xvpf 20190102-mk2rootfs-ub1804.tgz
4) Copy the release BOOT.BIN and image.ub to the boot partition (where mounted), i.e:
$  cp mk2-bsp-20181230/BOOT.BIN /media/BOOT/
$  cp mk2-bsp-20181230/image.ub /media/BOOT/
5) unmount both partitions before removing
6) plug SDcard into Mark2 and turn on power - it should boot to the new image


A. Creating SDcard partitions from Linux:
# ---- using sd_card inserted in USB dongle (i.e. /dev/sdb)

# ------------- Partitions ---------------------------------------------------
1. Use sudo fdisk /dev/sdb (or whatever dev the sdcard installs) to partition as follows:
a) delete all existing partitions, i.e commands d 1, 
b) create new boot partition 1:  n, p 1, +256M, t C (fat32)
c) create new rootfs partition: n, p 2, enter, enter
d) p should confirm the partitions as follows:
   Device Boot      Start         End      Blocks   Id  System
/dev/sdb1               1         120      265190    c  W95 FAT32 (LBA)
/dev/sdb2             121        3521     7516210   83  Linux


# -------------- Create Filesystems -------------------------------------------
$ sudo mkfs.vfat -n BOOT /dev/sdb1
mkfs.vfat 3.0.9 (31 Jan 2010)
$ sudo mke2fs -t ext4 -L ROOTFS /dev/sdb2



