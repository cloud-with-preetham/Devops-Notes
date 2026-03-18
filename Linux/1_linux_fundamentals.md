# Linux Fundamentals

## What is Linux?
Linux is an open-source, Unix-like operating system kernel created by Linus Torvalds in 1991.

## Basic Commands

### Navigation
- `pwd` - Print working directory
- `ls` - List directory contents
- `cd` - Change directory
- `cd ..` - Go up one directory
- `cd ~` - Go to home directory

### File Operations
- `touch` - Create empty file
- `mkdir` - Create directory
- `cp` - Copy files/directories
- `mv` - Move or rename files
- `rm` - Remove files
- `rm -r` - Remove directories recursively

### File Viewing
- `cat` - Display file contents
- `less` - View file with pagination
- `head` - Show first lines of file
- `tail` - Show last lines of file

### File Permissions
- `chmod` - Change file permissions
- `chown` - Change file owner
- `ls -l` - List with permissions

### System Information
- `uname -a` - System information
- `whoami` - Current user
- `df -h` - Disk space usage
- `free -h` - Memory usage
- `top` - Process monitor

### Text Processing
- `grep` - Search text patterns
- `find` - Search for files
- `wc` - Word count
- `sort` - Sort lines
- `uniq` - Remove duplicates

## File System Hierarchy
- `/` - Root directory
- `/home` - User home directories
- `/etc` - Configuration files
- `/var` - Variable data
- `/tmp` - Temporary files
- `/usr` - User programs
- `/bin` - Essential binaries

## Package Management
- **Debian/Ubuntu**: `apt`, `apt-get`, `dpkg`
- **Red Hat/CentOS**: `yum`, `dnf`, `rpm`

## Process Management
- `ps` - List processes
- `kill` - Terminate process
- `bg` - Background process
- `fg` - Foreground process
- `jobs` - List background jobs

## Networking
- `ping` - Test connectivity
- `ifconfig` / `ip` - Network configuration
- `netstat` - Network statistics
- `ssh` - Secure shell connection
- `scp` - Secure copy

## Redirection & Pipes
- `>` - Redirect output to file (overwrite)
- `>>` - Redirect output to file (append)
- `<` - Redirect input from file
- `|` - Pipe output to another command
