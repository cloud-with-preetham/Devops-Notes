# Linux Cheatsheet

## File Operations
- `ls -lah` - List all files with details
- `cd /path` - Change directory
- `pwd` - Print working directory
- `cp -r source dest` - Copy recursively
- `mv source dest` - Move/rename
- `rm -rf dir` - Remove directory forcefully
- `mkdir -p path/to/dir` - Create nested directories
- `touch file` - Create empty file
- `cat file` - Display file content
- `less file` - View file with pagination
- `head -n 20 file` - First 20 lines
- `tail -f file` - Follow file updates

## Search and Find
- `find /path -name "*.txt"` - Find files by name
- `find /path -type f -size +100M` - Find large files
- `grep -r "pattern" /path` - Recursive search
- `grep -i "pattern" file` - Case-insensitive search
- `locate filename` - Quick file search
- `which command` - Find command location
- `whereis command` - Locate binary/source/man

## Permissions
- `chmod 755 file` - rwxr-xr-x
- `chmod 644 file` - rw-r--r--
- `chmod +x file` - Add execute permission
- `chown user:group file` - Change ownership
- `chown -R user:group dir` - Recursive ownership

## Compression
- `tar -czvf archive.tar.gz files` - Create gzip archive
- `tar -xzvf archive.tar.gz` - Extract gzip archive
- `tar -cjvf archive.tar.bz2 files` - Create bzip2 archive
- `tar -xjvf archive.tar.bz2` - Extract bzip2 archive
- `zip -r archive.zip files` - Create zip
- `unzip archive.zip` - Extract zip
- `gzip file` - Compress file
- `gunzip file.gz` - Decompress file

## Process Management
- `ps aux` - List all processes
- `ps aux | grep process` - Find specific process
- `top` - Real-time process monitor
- `htop` - Interactive process viewer
- `kill PID` - Terminate process
- `kill -9 PID` - Force kill
- `killall name` - Kill by name
- `pkill pattern` - Kill by pattern
- `bg` - Background process
- `fg` - Foreground process
- `jobs` - List background jobs
- `nohup command &` - Run immune to hangups

## System Information
- `uname -a` - System information
- `hostname` - Show hostname
- `uptime` - System uptime
- `whoami` - Current user
- `id` - User ID and groups
- `date` - Current date/time
- `cal` - Calendar
- `w` - Who is logged in
- `last` - Login history

## Resource Monitoring
- `df -h` - Disk space usage
- `du -sh *` - Directory sizes
- `free -h` - Memory usage
- `top` - CPU/memory monitor
- `iostat` - I/O statistics
- `vmstat` - Virtual memory stats
- `netstat -tuln` - Network connections
- `ss -tuln` - Socket statistics

## User Management
- `useradd -m username` - Create user
- `passwd username` - Set password
- `usermod -aG group user` - Add to group
- `userdel -r username` - Delete user
- `groupadd groupname` - Create group
- `su - username` - Switch user
- `sudo command` - Run as root
- `visudo` - Edit sudoers

## Network
- `ip addr` - Show IP addresses
- `ip route` - Show routing table
- `ping host` - Test connectivity
- `traceroute host` - Trace route
- `nslookup domain` - DNS lookup
- `dig domain` - DNS query
- `curl url` - Transfer data from URL
- `wget url` - Download file
- `ssh user@host` - SSH connection
- `scp file user@host:/path` - Secure copy
- `netstat -tuln` - Listening ports
- `ss -tuln` - Socket stats

## Service Management (systemd)
- `systemctl start service` - Start service
- `systemctl stop service` - Stop service
- `systemctl restart service` - Restart service
- `systemctl status service` - Service status
- `systemctl enable service` - Enable at boot
- `systemctl disable service` - Disable at boot
- `systemctl list-units --type=service` - List services
- `journalctl -u service` - Service logs
- `journalctl -f` - Follow logs

## Package Management

### Debian/Ubuntu (apt)
- `apt update` - Update package list
- `apt upgrade` - Upgrade packages
- `apt install package` - Install package
- `apt remove package` - Remove package
- `apt search package` - Search package
- `apt list --installed` - List installed

### Red Hat/CentOS (yum/dnf)
- `yum update` - Update packages
- `yum install package` - Install package
- `yum remove package` - Remove package
- `yum search package` - Search package
- `yum list installed` - List installed

## Text Processing
- `cat file` - Display file
- `head -n 10 file` - First 10 lines
- `tail -n 10 file` - Last 10 lines
- `grep "pattern" file` - Search pattern
- `sed 's/old/new/g' file` - Replace text
- `awk '{print $1}' file` - Print column
- `cut -d',' -f1 file` - Cut by delimiter
- `sort file` - Sort lines
- `uniq file` - Remove duplicates
- `wc -l file` - Count lines
- `diff file1 file2` - Compare files

## Disk Management
- `lsblk` - List block devices
- `fdisk -l` - List partitions
- `mount /dev/sda1 /mnt` - Mount filesystem
- `umount /mnt` - Unmount
- `df -h` - Disk usage
- `du -sh dir` - Directory size
- `fsck /dev/sda1` - Check filesystem

## Redirection and Pipes
- `command > file` - Redirect output (overwrite)
- `command >> file` - Redirect output (append)
- `command < file` - Redirect input
- `command1 | command2` - Pipe output
- `command 2> file` - Redirect errors
- `command &> file` - Redirect all output
- `command | tee file` - Output to file and stdout

## Shortcuts
- `Ctrl+C` - Kill current process
- `Ctrl+Z` - Suspend process
- `Ctrl+D` - Exit/logout
- `Ctrl+L` - Clear screen
- `Ctrl+A` - Beginning of line
- `Ctrl+E` - End of line
- `Ctrl+U` - Clear line before cursor
- `Ctrl+K` - Clear line after cursor
- `Ctrl+R` - Search command history
- `!!` - Repeat last command
- `!$` - Last argument of previous command

## File Viewing/Editing
- `cat file` - Display file
- `less file` - Page through file
- `more file` - Page through file
- `nano file` - Simple text editor
- `vi file` - Vi editor
- `vim file` - Vim editor

## Archives and Backup
- `tar -czvf backup.tar.gz /path` - Create backup
- `tar -xzvf backup.tar.gz` - Extract backup
- `rsync -avz source dest` - Sync directories
- `rsync -avz source user@host:dest` - Remote sync
- `dd if=/dev/sda of=/backup/disk.img` - Disk image

## System Control
- `shutdown -h now` - Shutdown now
- `shutdown -r now` - Reboot now
- `reboot` - Reboot system
- `poweroff` - Power off system
- `init 0` - Shutdown
- `init 6` - Reboot

## Environment Variables
- `echo $VAR` - Display variable
- `export VAR=value` - Set variable
- `env` - Show all variables
- `printenv` - Print environment
- `source file` - Execute file in current shell
- `. file` - Execute file (shorthand)

## Aliases
- `alias ll='ls -lah'` - Create alias
- `unalias ll` - Remove alias
- `alias` - List all aliases

## History
- `history` - Show command history
- `history | grep command` - Search history
- `!n` - Execute command number n
- `!!` - Execute last command
- `!string` - Execute last command starting with string
- `Ctrl+R` - Reverse search history
