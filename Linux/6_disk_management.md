# Disk Management

## Disk Information

### View Disks and Partitions
- `lsblk` - List block devices
- `lsblk -f` - Show filesystem info
- `fdisk -l` - List all disks and partitions
- `df -h` - Disk space usage
- `df -i` - Inode usage
- `du -sh /path` - Directory size
- `blkid` - Block device attributes

### Disk Details
- `hdparm -I /dev/sda` - Disk information
- `smartctl -a /dev/sda` - SMART disk health
- `lshw -class disk` - Hardware disk info
- `parted -l` - Partition information

## Partitioning

### fdisk (MBR)
- `fdisk /dev/sda` - Open fdisk utility
- `fdisk -l /dev/sda` - List partitions
- Commands in fdisk:
  - `n` - New partition
  - `d` - Delete partition
  - `p` - Print partition table
  - `w` - Write changes
  - `q` - Quit without saving

### parted (GPT)
- `parted /dev/sda` - Open parted
- `parted /dev/sda print` - Show partitions
- `parted /dev/sda mklabel gpt` - Create GPT table
- `parted /dev/sda mkpart primary 0% 100%` - Create partition
- `parted /dev/sda rm 1` - Remove partition 1

### gdisk (GPT)
- `gdisk /dev/sda` - GPT partitioning tool
- Similar commands to fdisk

## Filesystem Creation

### Creating Filesystems
- `mkfs.ext4 /dev/sda1` - Create ext4 filesystem
- `mkfs.xfs /dev/sda1` - Create XFS filesystem
- `mkfs.btrfs /dev/sda1` - Create Btrfs filesystem
- `mkfs.vfat /dev/sda1` - Create FAT32 filesystem
- `mkswap /dev/sda2` - Create swap space

### Filesystem Options
- `mkfs.ext4 -L mylabel /dev/sda1` - With label
- `mkfs.ext4 -m 1 /dev/sda1` - Reserved blocks (1%)
- `mkfs.ext4 -N 1000000 /dev/sda1` - Specify inodes

## Mounting

### Mount Operations
- `mount /dev/sda1 /mnt` - Mount filesystem
- `mount -t ext4 /dev/sda1 /mnt` - Specify type
- `mount -o ro /dev/sda1 /mnt` - Read-only
- `mount -o remount,rw /mnt` - Remount read-write
- `umount /mnt` - Unmount
- `umount -l /mnt` - Lazy unmount

### Mount Information
- `mount` - Show all mounts
- `findmnt` - Tree view of mounts
- `cat /proc/mounts` - Current mounts
- `lsof /mnt` - Processes using mount

### Persistent Mounts
- `/etc/fstab` - Filesystem table
- Format: `device mountpoint fstype options dump pass`
- Example: `/dev/sda1 /data ext4 defaults 0 2`
- `mount -a` - Mount all in fstab
- `systemctl daemon-reload` - Reload systemd mounts

## Swap Management

### Swap Operations
- `swapon /dev/sda2` - Enable swap
- `swapoff /dev/sda2` - Disable swap
- `swapon -s` - Show swap usage
- `free -h` - Memory and swap info

### Swap File
- `fallocate -l 2G /swapfile` - Create swap file
- `dd if=/dev/zero of=/swapfile bs=1M count=2048` - Alternative
- `chmod 600 /swapfile` - Set permissions
- `mkswap /swapfile` - Format as swap
- `swapon /swapfile` - Enable swap file

## LVM (Logical Volume Manager)

### Physical Volumes
- `pvcreate /dev/sda1` - Create physical volume
- `pvdisplay` - Show physical volumes
- `pvs` - List physical volumes
- `pvremove /dev/sda1` - Remove physical volume

### Volume Groups
- `vgcreate vg_name /dev/sda1` - Create volume group
- `vgextend vg_name /dev/sdb1` - Add disk to VG
- `vgdisplay` - Show volume groups
- `vgs` - List volume groups
- `vgremove vg_name` - Remove volume group

### Logical Volumes
- `lvcreate -L 10G -n lv_name vg_name` - Create LV
- `lvcreate -l 100%FREE -n lv_name vg_name` - Use all space
- `lvdisplay` - Show logical volumes
- `lvs` - List logical volumes
- `lvremove /dev/vg_name/lv_name` - Remove LV

### Resizing LVM
- `lvextend -L +5G /dev/vg_name/lv_name` - Extend LV
- `lvextend -l +100%FREE /dev/vg_name/lv_name` - Use all free
- `lvreduce -L -5G /dev/vg_name/lv_name` - Reduce LV
- `resize2fs /dev/vg_name/lv_name` - Resize ext4 filesystem
- `xfs_growfs /mount/point` - Grow XFS filesystem

## Filesystem Maintenance

### Checking Filesystems
- `fsck /dev/sda1` - Check filesystem
- `fsck -y /dev/sda1` - Auto-repair
- `e2fsck /dev/sda1` - Check ext filesystem
- `xfs_repair /dev/sda1` - Repair XFS

### Filesystem Tuning
- `tune2fs -l /dev/sda1` - Show filesystem info
- `tune2fs -L newlabel /dev/sda1` - Change label
- `tune2fs -m 1 /dev/sda1` - Set reserved blocks
- `tune2fs -c 30 /dev/sda1` - Max mount count before check

### Filesystem Labels
- `e2label /dev/sda1` - Show ext label
- `e2label /dev/sda1 newlabel` - Set ext label
- `xfs_admin -L newlabel /dev/sda1` - Set XFS label
- `blkid` - Show all labels and UUIDs

## RAID Management

### mdadm (Software RAID)
- `mdadm --create /dev/md0 --level=1 --raid-devices=2 /dev/sda1 /dev/sdb1` - Create RAID1
- `mdadm --detail /dev/md0` - RAID details
- `cat /proc/mdstat` - RAID status
- `mdadm --add /dev/md0 /dev/sdc1` - Add disk
- `mdadm --fail /dev/md0 /dev/sda1` - Mark disk failed
- `mdadm --remove /dev/md0 /dev/sda1` - Remove disk
- `mdadm --stop /dev/md0` - Stop RAID

## Disk Performance

### Performance Testing
- `hdparm -tT /dev/sda` - Disk speed test
- `dd if=/dev/zero of=/tmp/test bs=1M count=1024` - Write test
- `iostat -x 1` - I/O statistics
- `iotop` - I/O usage by process

### Disk Optimization
- `hdparm -W1 /dev/sda` - Enable write cache
- `blockdev --setra 256 /dev/sda` - Set read-ahead
- `fstrim -v /` - TRIM SSD (manual)
- `systemctl enable fstrim.timer` - Enable periodic TRIM

## Disk Quotas

### Quota Management
- `quotacheck -cug /mount` - Create quota files
- `quotaon /mount` - Enable quotas
- `quotaoff /mount` - Disable quotas
- `edquota -u username` - Edit user quota
- `quota -u username` - Show user quota
- `repquota -a` - Report all quotas

## Disk Cloning

### Cloning Operations
- `dd if=/dev/sda of=/dev/sdb bs=4M status=progress` - Clone disk
- `dd if=/dev/sda of=/backup/disk.img bs=4M` - Disk image
- `rsync -avxHAX / /mnt/backup` - Sync filesystem
- `clonezilla` - Disk cloning tool

## Network Storage

### NFS
- `mount -t nfs server:/share /mnt` - Mount NFS
- `/etc/exports` - NFS exports configuration
- `exportfs -a` - Export all shares
- `showmount -e server` - Show NFS exports

### CIFS/SMB
- `mount -t cifs //server/share /mnt -o username=user` - Mount SMB
- `smbclient -L server` - List SMB shares
