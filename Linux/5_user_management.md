# User Management

## User Accounts

### Creating Users
- `useradd username` - Create user
- `useradd -m username` - Create user with home directory
- `useradd -m -s /bin/bash username` - Create with specific shell
- `useradd -m -G group1,group2 username` - Create with groups
- `adduser username` - Interactive user creation (Debian/Ubuntu)

### Modifying Users
- `usermod -l newname oldname` - Rename user
- `usermod -d /new/home username` - Change home directory
- `usermod -s /bin/zsh username` - Change shell
- `usermod -aG groupname username` - Add user to group
- `usermod -L username` - Lock user account
- `usermod -U username` - Unlock user account

### Deleting Users
- `userdel username` - Delete user
- `userdel -r username` - Delete user and home directory

### User Information
- `id username` - User ID and groups
- `whoami` - Current user
- `who` - Logged in users
- `w` - Who is logged in and what they're doing
- `last` - Login history
- `lastlog` - Last login for all users
- `finger username` - User information

## Password Management

### Setting Passwords
- `passwd` - Change own password
- `passwd username` - Change user password (root)
- `passwd -l username` - Lock password
- `passwd -u username` - Unlock password
- `passwd -d username` - Delete password
- `passwd -e username` - Expire password (force change)

### Password Policies
- `chage -l username` - View password aging info
- `chage -M 90 username` - Max password age (90 days)
- `chage -m 7 username` - Min days between changes
- `chage -W 14 username` - Warning days before expiry
- `chage -E 2024-12-31 username` - Account expiry date

## Group Management

### Creating Groups
- `groupadd groupname` - Create group
- `groupadd -g 1500 groupname` - Create with specific GID

### Modifying Groups
- `groupmod -n newname oldname` - Rename group
- `groupmod -g 2000 groupname` - Change GID
- `gpasswd -a username groupname` - Add user to group
- `gpasswd -d username groupname` - Remove user from group
- `gpasswd -A username groupname` - Set group admin

### Deleting Groups
- `groupdel groupname` - Delete group

### Group Information
- `groups username` - Show user's groups
- `getent group groupname` - Group details
- `lid -g groupname` - List group members

## User Configuration Files

### Important Files
- `/etc/passwd` - User account information
- `/etc/shadow` - Encrypted passwords
- `/etc/group` - Group information
- `/etc/gshadow` - Secure group information
- `/etc/sudoers` - Sudo configuration
- `/etc/login.defs` - Default settings for user creation
- `/etc/skel/` - Default files for new users

### File Format Examples
```
/etc/passwd: username:x:UID:GID:comment:home:shell
/etc/group: groupname:x:GID:members
```

## Sudo Access

### Managing Sudo
- `visudo` - Edit sudoers file safely
- `sudo -l` - List sudo privileges
- `sudo -u username command` - Run as specific user
- `sudo -i` - Interactive root shell
- `sudo su -` - Switch to root

### Sudoers Configuration
```
username ALL=(ALL:ALL) ALL
%groupname ALL=(ALL:ALL) ALL
username ALL=(ALL) NOPASSWD: ALL
```

## User Sessions

### Session Management
- `su username` - Switch user
- `su - username` - Switch user with environment
- `exit` - Exit current session
- `logout` - Logout from shell
- `pkill -u username` - Kill user processes
- `loginctl list-sessions` - List sessions
- `loginctl terminate-session ID` - Terminate session

## User Limits

### Resource Limits
- `ulimit -a` - Show all limits
- `ulimit -n 4096` - Set max open files
- `ulimit -u 1000` - Set max processes
- `/etc/security/limits.conf` - Persistent limits

### Limits Configuration
```
username soft nofile 4096
username hard nofile 8192
@groupname soft nproc 1000
```

## User Monitoring

### Activity Monitoring
- `who` - Currently logged in
- `w` - Detailed user activity
- `users` - List logged in users
- `last -n 10` - Last 10 logins
- `lastb` - Failed login attempts
- `ac -p` - Login time per user

## Home Directory Management

### Home Directory Operations
- `mkhomedir_helper username` - Create home directory
- `cp -r /etc/skel /home/username` - Copy skeleton files
- `chown -R username:username /home/username` - Fix ownership

## User Environment

### Shell Configuration
- `~/.bashrc` - Bash configuration
- `~/.bash_profile` - Bash login script
- `~/.profile` - Shell profile
- `/etc/profile` - System-wide profile
- `/etc/bash.bashrc` - System-wide bashrc

### Environment Variables
- `echo $USER` - Current username
- `echo $HOME` - Home directory
- `echo $SHELL` - Current shell
- `env` - Show all environment variables
- `export VAR=value` - Set environment variable

## Batch Operations

### Bulk User Management
- `newusers file` - Create multiple users from file
- `chpasswd` - Change multiple passwords from stdin
- `for user in user1 user2; do useradd $user; done` - Loop creation
