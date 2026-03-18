# File Management

## Basic File Operations

### Creating Files and Directories
- `touch file.txt` - Create empty file
- `mkdir directory` - Create directory
- `mkdir -p path/to/dir` - Create nested directories
- `mktemp` - Create temporary file

### Copying Files
- `cp source dest` - Copy file
- `cp -r source dest` - Copy directory recursively
- `cp -p source dest` - Preserve attributes
- `cp -u source dest` - Copy only if newer
- `cp -i source dest` - Interactive (prompt before overwrite)

### Moving and Renaming
- `mv source dest` - Move or rename
- `mv -i source dest` - Interactive move
- `mv -u source dest` - Move only if newer
- `rename` - Batch rename files

### Deleting Files
- `rm file` - Remove file
- `rm -r directory` - Remove directory recursively
- `rm -f file` - Force remove without prompt
- `rm -i file` - Interactive removal
- `rmdir directory` - Remove empty directory

## Viewing Files

### Display Content
- `cat file` - Display entire file
- `tac file` - Display in reverse
- `less file` - Page through file
- `more file` - Page through file (basic)
- `head file` - First 10 lines
- `head -n 20 file` - First 20 lines
- `tail file` - Last 10 lines
- `tail -n 20 file` - Last 20 lines
- `tail -f file` - Follow file updates

### File Information
- `ls` - List files
- `ls -l` - Long format with details
- `ls -a` - Show hidden files
- `ls -lh` - Human-readable sizes
- `ls -lt` - Sort by modification time
- `ls -lS` - Sort by size
- `stat file` - Detailed file information
- `file file` - Determine file type

## Searching Files

### Find Files
- `find /path -name "*.txt"` - Find by name
- `find /path -type f` - Find files only
- `find /path -type d` - Find directories only
- `find /path -size +100M` - Find files larger than 100MB
- `find /path -mtime -7` - Modified in last 7 days
- `find /path -user username` - Find by owner
- `locate filename` - Quick file search (uses database)
- `updatedb` - Update locate database

### Search Content
- `grep "pattern" file` - Search in file
- `grep -r "pattern" /path` - Recursive search
- `grep -i "pattern" file` - Case-insensitive
- `grep -v "pattern" file` - Invert match
- `grep -n "pattern" file` - Show line numbers
- `grep -c "pattern" file` - Count matches
- `ack "pattern"` - Better grep alternative
- `ag "pattern"` - Silver searcher (fast)

## File Permissions

### Permission Format
- `r` (4) - Read
- `w` (2) - Write
- `x` (1) - Execute

### Changing Permissions
- `chmod 755 file` - rwxr-xr-x
- `chmod 644 file` - rw-r--r--
- `chmod u+x file` - Add execute for user
- `chmod g-w file` - Remove write for group
- `chmod o=r file` - Set read-only for others
- `chmod -R 755 directory` - Recursive

### Changing Ownership
- `chown user file` - Change owner
- `chown user:group file` - Change owner and group
- `chown -R user directory` - Recursive
- `chgrp group file` - Change group only

## File Compression

### tar Archives
- `tar -cvf archive.tar files` - Create tar
- `tar -xvf archive.tar` - Extract tar
- `tar -tvf archive.tar` - List contents
- `tar -czvf archive.tar.gz files` - Create gzip tar
- `tar -xzvf archive.tar.gz` - Extract gzip tar
- `tar -cjvf archive.tar.bz2 files` - Create bzip2 tar
- `tar -xjvf archive.tar.bz2` - Extract bzip2 tar

### Compression Tools
- `gzip file` - Compress with gzip
- `gunzip file.gz` - Decompress gzip
- `bzip2 file` - Compress with bzip2
- `bunzip2 file.bz2` - Decompress bzip2
- `zip archive.zip files` - Create zip
- `unzip archive.zip` - Extract zip

## File Comparison

### Compare Files
- `diff file1 file2` - Show differences
- `diff -u file1 file2` - Unified format
- `diff -y file1 file2` - Side-by-side
- `cmp file1 file2` - Byte-by-byte comparison
- `comm file1 file2` - Compare sorted files
- `vimdiff file1 file2` - Visual diff in vim

## Links

### Hard and Soft Links
- `ln source link` - Create hard link
- `ln -s source link` - Create symbolic link
- `readlink link` - Show link target
- `unlink link` - Remove link

## File Attributes

### Special Attributes
- `lsattr file` - List attributes
- `chattr +i file` - Make immutable
- `chattr -i file` - Remove immutable
- `chattr +a file` - Append-only mode

## Disk Usage

### Space Analysis
- `du -sh directory` - Directory size
- `du -h --max-depth=1` - Subdirectory sizes
- `df -h` - Disk space usage
- `ncdu` - Interactive disk usage analyzer

## Text Processing

### Editing and Manipulation
- `sed 's/old/new/g' file` - Replace text
- `awk '{print $1}' file` - Print first column
- `cut -d',' -f1 file` - Cut by delimiter
- `sort file` - Sort lines
- `uniq file` - Remove duplicates
- `wc -l file` - Count lines
- `tr 'a-z' 'A-Z' < file` - Translate characters
