# Snap-tools
Tools for managing snapper organized btrfs filesystem layouts

## Installation
The tools are written on and for ArchLinux only.

### Dependencies
* btrfs-progs
* dosfstools
* arch-install-scripts
* util-linux
* cryptsetup

## Tools

### snap-install

TODO

### snap-part
Partitions a disk with GPT, creates encrypted luks containers with ext4 + btrfs.

* The system is bootable on EFI and BIOS computers
* Fully encrypted /boot and root /
* Independent encryption for optional security mechanisms
* Ext4 /boot for better grub support

```
+----------------------------------------------+
|                     GPT                      |
+---------+----------+------------+------------+
|  sda1   |   sda2   |    sda3    |    sda4    |
+----------------------------------------------+
| 1M Bios | 512M Efi | 512M Linux | 100% Linux |
|  Grub   | Efi Grub |    Luks    |    Luks    |
+----------------------------------------------+
|         |  Fat32   |    Ext4    |   Btrfs    |
|         |/boot/efi |   /boot    |     /      |
+---------+----------+------------+------------+
```

### mkfs.snap
Creates a btrfs filesystems with snapper compatible layout.

The layout consists of snapper tracked subvolumes mounted to the root mountpoint. Each subvolume also has a hidden snapshot subvolume where the snapper snapshots are stored. If you ever recover a system the last system state is backed up to the backup subvolume. Other subvolumes to exclude the pacman cache etc are stored in the excludes subvolume. The btrfs root is also mounted secret at /.btrfs for maintenance.


```
Example layout with subvolumes: home, data, vm

btrfs
`-- / -> /.btrfs (root:root 700)
    |-- subvolumes
    |   |-- root -> /
    |   |-- home -> /home
    |   |-- data -> /data
    |   `-- vm -> /vm
    |-- snapshots
    |   |-- root -> /.snapshots
    |   |-- home -> /home/.snapshots
    |   |-- data -> /data/.snapshots
    |   `-- vm -> /vm/.snapshots
    |-- backups
    |   |-- root-date
    |   |-- home-date
    |   |-- data-date
    |   `-- vm-date
    `-- excludes
        |-- pkg -> /var/cache/pacman/pkg
        |-- abs -> /var/abs
        |-- tmp -> /var/tmp
        |-- log -> /var/log
        `-- srv -> /srv

Non btrfs partitions (taken from the snap-part layout):

ext4
`-- / -> /boot

fat32
`-- / -> /boot/efi
```

### mount.snap
Mounts a snapper compatible btrfs layout created by mkfs.snap

### snap-restore

TODO

## TODO
* shellcheck
* AUR package

## Links
* [snapper](https://github.com/openSUSE/snapper)
* [snap-sync](https://github.com/wesbarnett/snap-sync)
* [snap-pac](https://github.com/wesbarnett/snap-pac)

## Version History
```
Untagged Release (25.12.2016)
* Initial release of the software
```
