# Linux Troubleshooting

## System Performance Issues

### CPU Troubleshooting
- `top` - Monitor CPU usage
- `htop` - Interactive CPU monitor
- `mpstat` - CPU statistics
- `uptime` - System load average
- `ps aux --sort=-%cpu | head` - Top CPU processes

### Memory Troubleshooting
- `free -h` - Memory usage
- `vmstat` - Virtual memory statistics
- `ps aux --sort=-%mem | head` - Top memory processes
- `cat /proc/meminfo` - Detailed memory info
- `swapon -s` - Swap usage

### Disk Troubleshooting
- `df -h` - Disk space usage
- `du -sh *` - Directory sizes
- `du -h --max-depth=1` - Subdirectory sizes
- `iostat` - I/O statistics
- `iotop` - I/O usage by process
- `lsblk` - List block devices
- `fdisk -l` - Disk partitions

## Network Troubleshooting

### Connectivity
- `ping <host>` - Test connectivity
- `traceroute <host>` - Trace route to host
- `mtr <host>` - Network diagnostic tool
- `nslookup <domain>` - DNS lookup
- `dig <domain>` - DNS query tool

### Network Configuration
- `ip addr` - Show IP addresses
- `ip route` - Show routing table
- `ifconfig` - Network interface config
- `netstat -tuln` - Listening ports
- `ss -tuln` - Socket statistics
- `lsof -i` - Network connections

### Firewall
- `iptables -L` - List firewall rules
- `ufw status` - UFW firewall status
- `firewall-cmd --list-all` - Firewalld status

## Log Analysis

### System Logs
- `journalctl -xe` - Recent logs with details
- `journalctl -f` - Follow logs
- `journalctl -p err` - Error logs only
- `dmesg` - Kernel messages
- `tail -f /var/log/syslog` - System log
- `tail -f /var/log/messages` - General messages

### Application Logs
- `/var/log/` - Log directory
- `grep -r "error" /var/log/` - Search for errors
- `journalctl -u <service>` - Service logs

## Process Issues

### Hung Processes
- `ps aux | grep <process>` - Find process
- `kill -15 <PID>` - Graceful termination
- `kill -9 <PID>` - Force kill
- `killall <name>` - Kill by name

### Zombie Processes
- `ps aux | grep Z` - Find zombies
- Kill parent process to clean up zombies

## File System Issues

### Disk Full
- `df -h` - Check disk space
- `du -sh /* | sort -h` - Find large directories
- `find / -type f -size +100M` - Find large files
- `lsof | grep deleted` - Find deleted but open files

### File Permissions
- `ls -l` - Check permissions
- `chmod` - Change permissions
- `chown` - Change ownership
- `getfacl` - Get ACL
- `setfacl` - Set ACL

### Inode Issues
- `df -i` - Check inode usage
- `find / -xdev -printf '%h\n' | sort | uniq -c | sort -k 1 -n` - Count files per directory

## Boot Issues

### Boot Logs
- `journalctl -b` - Current boot logs
- `journalctl -b -1` - Previous boot logs
- `dmesg | less` - Kernel boot messages

### GRUB Issues
- Edit GRUB: `/etc/default/grub`
- Update GRUB: `update-grub` or `grub2-mkconfig`

## Service Issues

### Service Debugging
- `systemctl status <service>` - Service status
- `systemctl list-dependencies <service>` - Dependencies
- `journalctl -u <service> -n 50` - Last 50 log entries
- `systemctl cat <service>` - View service file

## Hardware Issues

### Hardware Information
- `lshw` - List hardware
- `lspci` - PCI devices
- `lsusb` - USB devices
- `dmidecode` - DMI/SMBIOS info
- `sensors` - Temperature sensors

### Disk Health
- `smartctl -a /dev/sda` - SMART disk info
- `badblocks -v /dev/sda` - Check for bad blocks

## User and Authentication Issues

### User Troubleshooting
- `who` - Logged in users
- `last` - Login history
- `lastlog` - Last login per user
- `faillog` - Failed login attempts
- `passwd <user>` - Reset password

### Permission Issues
- `sudo -l` - Check sudo permissions
- `/etc/sudoers` - Sudo configuration
- `id <user>` - User ID and groups

## Common Commands

### System Information
- `uname -a` - System info
- `hostnamectl` - Hostname info
- `timedatectl` - Time/date info
- `cat /etc/os-release` - OS version

### Quick Diagnostics
- `uptime` - System uptime and load
- `w` - Who is logged in and what they're doing
- `dmesg -T` - Kernel messages with timestamps
