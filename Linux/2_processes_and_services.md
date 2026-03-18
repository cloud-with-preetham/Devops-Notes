# Processes and Services

## What is a Process?
A process is an instance of a running program with its own memory space and system resources.

## Process Types
- **Foreground Process**: Runs in terminal, blocks user input
- **Background Process**: Runs independently, doesn't block terminal
- **Daemon**: Background process that starts at boot

## Process States
- **Running (R)**: Currently executing
- **Sleeping (S)**: Waiting for event
- **Stopped (T)**: Suspended
- **Zombie (Z)**: Completed but not cleaned up

## Process Management Commands

### Viewing Processes
- `ps` - Show current processes
- `ps aux` - Show all processes with details
- `ps -ef` - Full format listing
- `top` - Real-time process monitor
- `htop` - Interactive process viewer
- `pgrep` - Find process by name

### Process Control
- `kill <PID>` - Terminate process
- `kill -9 <PID>` - Force kill process
- `killall <name>` - Kill by process name
- `pkill <pattern>` - Kill by pattern match

### Background/Foreground
- `command &` - Run in background
- `Ctrl+Z` - Suspend current process
- `bg` - Resume in background
- `fg` - Bring to foreground
- `jobs` - List background jobs

### Process Priority
- `nice -n <value> command` - Start with priority
- `renice <value> -p <PID>` - Change priority
- Priority range: -20 (highest) to 19 (lowest)

## Services (systemd)

### Service Management
- `systemctl start <service>` - Start service
- `systemctl stop <service>` - Stop service
- `systemctl restart <service>` - Restart service
- `systemctl reload <service>` - Reload configuration
- `systemctl status <service>` - Check service status
- `systemctl enable <service>` - Enable at boot
- `systemctl disable <service>` - Disable at boot

### Service Information
- `systemctl list-units --type=service` - List all services
- `systemctl list-unit-files` - List service files
- `systemctl is-active <service>` - Check if active
- `systemctl is-enabled <service>` - Check if enabled

### Viewing Logs
- `journalctl` - View system logs
- `journalctl -u <service>` - View service logs
- `journalctl -f` - Follow logs in real-time
- `journalctl -b` - Logs since last boot

## Legacy Init Systems

### SysVinit (older systems)
- `service <name> start/stop/restart`
- `/etc/init.d/<service> start/stop/restart`

## Process Information

### Process Details
- `/proc/<PID>/` - Process information directory
- `lsof` - List open files by process
- `strace` - Trace system calls
- `pstree` - Display process tree

### Resource Usage
- `top` - CPU and memory usage
- `pidstat` - Process statistics
- `iotop` - I/O usage by process
