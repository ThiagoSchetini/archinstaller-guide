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

To use rsync on a direct/USB ssd/hd mounted unit using ext4, it will only update differences after first run:

    rsync -aAXHv --delete --progress /mnt/storage/ /run/media/thiago/BECKUPHD

To use rsync on a usb pen drive using FAT32 extfat use:

    rsync -rltDv --delete --progress /mnt/storage/ /run/media/thiago/BECKUPPEN

The flags are important:

    # HD/SSD (ext4)
    -a archive mode (recursive, preserve symlinks, permissions, timestamps, group, owner, devices)
    -A preserve ACLs Access Control Lists (more granular than standard Unix permissions).
    -X preserve extended attributes SELinux labels, system capabilities, or special flags
       *Essential if you have encrypted filesystems (like gocryptfs) or special Linux file attributes.
    -H preserve hard links. If you have hard-linked files in /mnt/storage...
    -v verbose
    -h human-readable
    --delete remove files from destination that are missing in source
    --progress show per-file progress

    # Pen drives (extfat)
    -r recursive
    -l preserve symlinks
    -t preserve modification times
    -D preserve devices/special files (ignored on FAT)
    -v verbose
    --progress show progress

## Encrypt/Decrypt some specific folder

Use gocryptfs (userspace)

    sudo pacman -S gocryptfs

Create container directory:

    mkdir -p /mnt/storage/Documents.gocryptfs
    mkdir -p /home/thiago/Documents   # mount point (empty)

You’ll be asked for a passphrase. Choose a strong one and store it safely

Initialize gocryptfs in the container (it will prompt for passphrase):

    gocryptfs -init /mnt/storage/Documents.gocryptfs

Open/Mount the vault (will ask for passphrase):

    gocryptfs /mnt/storage/Documents.gocryptfs /home/thiago/Documents

## Pen drive formating guide

Find the device

    lsblk -f

Wipe it

    sudo umount /dev/sdb1
    sudo wipefs -a /dev/sdb

Partition MBR (DOS) maximum cross-platform compatibility (Windows/macOS/Linux)
If your pen drive is < 2 TB, MBR is fine

    sudo parted /dev/sdb -- mklabel msdos
    sudo parted /dev/sdb -- mkpart primary fat32 1MiB 100% # single FAT32 partition

Label it:

    sudo fatlabel /dev/sdb1 BECKUPPEN
    
Format it using exfat (better for >4GB files):

    sudo mkfs.exfat -n BECKUPPEN /dev/sdb1

Unplug and Plug again to check it mounted, like "/run/media/thiago/BECKUPPEN"

    lsblk -f
