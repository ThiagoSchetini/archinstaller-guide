# SSD Maintenance

## Add SSD as auto mounted unit

Get information on:

    lsblk -f

Start process, 'nvme1m1' is an example:
  
    sudo umount /dev/nvme1n1*
    sudo wipefs -a /dev/nvme1n1

Prepare partitions:

    sudo parted /dev/nvme1n1 -- mklabel gpt
    sudo parted /dev/nvme1n1 -- mkpart primary ext4 1MiB 100%

Format using label (-L myalias):

    sudo mkfs.ext4 -L STORAGE /dev/nvme1n1p1

Create permanent mount point:

    sudo mkdir -p /mnt/storage

Get the UUID:

    sudo blkid /dev/nvme1n1p1

Add it to /etc/fstab:

    sudo vim /etc/fstab
    UUID=5b7e7...your-uuid...417a  /mnt/storage  ext4  defaults,noatime,user,exec  0 2
    
Test:
    
    sudo mount -a

Single user workstation permissions setup (needed for normal operation):

    sudo chown -R thiago:thiago /mnt/storage
    sudo chmod -R 755 /mnt/storage

Symlink creation, them you open nautilus and drag it to sidebar (to appear as Documents)

    ln -s /mnt/storage ~/Storage
  
## Label existing partitions

Label a normal Ext4 partition:
  
    sudo e2label /dev/nvme0n1p2 MAIN

Label a boot fat32 existing partition (always UPPERCASE! on fat32):

    sudo fatlabel /dev/nvme0n1p1 BOOT

## Beckup using rsync

Before rsync first time on a USB or other external ssd/drive, permissions on the target should be identical to the source, example:

    sudo chown -R thiago:thiago /run/media/thiago/BECKUPHD
    sudo chmod -R 755 /run/media/thiago/BECKUPHD

To use rsync on a usb or direct (fstab) mounted unit, it will only update differences after first run:

    rsync -aAXHv --delete --progress /mnt/storage/ /run/media/thiago/BECKUPHD

The flags are important:

    -a archive mode (recursive, preserve symlinks, permissions, timestamps, group, owner, devices)
    -A preserve ACLs Access Control Lists (more granular than standard Unix permissions).
    -X preserve extended attributes SELinux labels, system capabilities, or special flags
       *Essential if you have encrypted filesystems (like gocryptfs) or special Linux file attributes.
    -H preserve hard links. If you have hard-linked files in /mnt/storage...
    -v verbose
    -h human-readable
    --delete remove files from destination that are missing in source
    --progress show per-file progress

