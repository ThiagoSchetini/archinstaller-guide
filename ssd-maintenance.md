# SSD Maintenance

## Add SSD as auto mounted unit using a common GPT Ext4 on a UEFI system

Get information on:

    lsblk -f

Start process, 'nvme1m1' is an example:
  
    sudo umount /dev/nvme1n1*
    sudo wipefs -a /dev/nvme1n1

Prepare partitions:

    sudo fdisk /dev/nvme1n1
    - use g (gpt partiton)
    - them n (new partition, use defaults for full size, just press enter 3 or more times)
    - finally w to write

Format using label (-L myalias):

    sudo mkfs.ext4 -L STORAGE /dev/nvme1n1p1

Create permanent mount point:

    sudo mkdir -p /mnt/storage

Get the UUID:

    sudo blkid /dev/nvme1n1p1

Add It to /etc/fstab:

    sudo vim /etc/fstab
    UUID=5b7e7...your-uuid...417a  /mnt/storage  ext4  defaults,noatime,user,exec  0 2
    
Test:
    
    sudo mount -a

Single user workstation permissiosn setup:

    sudo chown -R thiago:thiago /mnt/storage
    sudo chmod -R 755 /mnt/storage
  
## Label existing system or boot partitions

Label a normal Ext4 partition:
  
    sudo e2label /dev/nvme0n1p2 MAIN

Label a boot fat32 existing partition (always UPPERCASE! on fat32):

    sudo fatlabel /dev/nvme0n1p1 BOOT
