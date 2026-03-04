# Shell Scripting Cheatsheet

## Script Basics

```bash
#!/bin/bash                    # Shebang
chmod +x script.sh             # Make executable
./script.sh                    # Run script
bash script.sh                 # Run with bash
```

## Variables

```bash
name="value"                   # Declare
echo $name                     # Use variable
echo ${name}                   # Use variable (safer)
readonly VAR="value"           # Read-only variable
unset VAR                      # Delete variable
```

## Special Variables

```bash
$0          # Script name
$1 $2 $3    # Arguments
$#          # Number of arguments
$@          # All arguments (separate)
$*          # All arguments (single)
$?          # Exit status
$$          # Process ID
$!          # Last background PID
```

## Command Substitution

```bash
result=$(command)              # Modern
result=`command`               # Old style
```

## String Operations

```bash
${#str}                        # Length
${str:0:5}                     # Substring
${str/old/new}                 # Replace first
${str//old/new}                # Replace all
${str#prefix}                  # Remove prefix
${str%suffix}                  # Remove suffix
${str^^}                       # Uppercase
${str,,}                       # Lowercase
```

## Arithmetic

```bash
$((a + b))                     # Addition
$((a - b))                     # Subtraction
$((a * b))                     # Multiplication
$((a / b))                     # Division
$((a % b))                     # Modulo
((a++))                        # Increment
((a--))                        # Decrement
```

## Conditionals

```bash
if [ condition ]; then
    commands
fi

if [ condition ]; then
    commands
else
    commands
fi

if [ condition ]; then
    commands
elif [ condition ]; then
    commands
else
    commands
fi
```

## Test Operators

### String Tests

```bash
[ "$a" = "$b" ]                # Equal
[ "$a" != "$b" ]               # Not equal
[ -z "$a" ]                    # Empty
[ -n "$a" ]                    # Not empty
```

### Numeric Tests

```bash
[ $a -eq $b ]                  # Equal
[ $a -ne $b ]                  # Not equal
[ $a -lt $b ]                  # Less than
[ $a -le $b ]                  # Less or equal
[ $a -gt $b ]                  # Greater than
[ $a -ge $b ]                  # Greater or equal
```

### File Tests

```bash
[ -e file ]                    # Exists
[ -f file ]                    # Regular file
[ -d file ]                    # Directory
[ -r file ]                    # Readable
[ -w file ]                    # Writable
[ -x file ]                    # Executable
[ -s file ]                    # Not empty
[ -L file ]                    # Symbolic link
```

### Logical Operators

```bash
[ cond1 ] && [ cond2 ]         # AND
[ cond1 ] || [ cond2 ]         # OR
[ ! cond ]                     # NOT
```

## Case Statement

```bash
case $var in
    pattern1)
        commands
        ;;
    pattern2)
        commands
        ;;
    *)
        default
        ;;
esac
```

## Loops

### For Loop

```bash
for i in 1 2 3; do
    echo $i
done

for i in {1..5}; do
    echo $i
done

for ((i=0; i<5; i++)); do
    echo $i
done

for file in *.txt; do
    echo $file
done
```

### While Loop

```bash
while [ condition ]; do
    commands
done

counter=0
while [ $counter -lt 5 ]; do
    echo $counter
    ((counter++))
done
```

### Until Loop

```bash
until [ condition ]; do
    commands
done
```

### Loop Control

```bash
break                          # Exit loop
continue                       # Next iteration
```

## Functions

```bash
function_name() {
    commands
    return 0
}

function_name arg1 arg2        # Call function
```

## Arrays

```bash
arr=(1 2 3 4 5)                # Declare
echo ${arr[0]}                 # First element
echo ${arr[@]}                 # All elements
echo ${#arr[@]}                # Length
arr+=(6)                       # Append
unset arr[2]                   # Delete element
```

## Input/Output

```bash
read var                       # Read input
read -p "Prompt: " var         # With prompt
read -sp "Password: " pass     # Silent
echo "text"                    # Print
printf "format" args           # Formatted print
```

## Redirection

```bash
cmd > file                     # Redirect stdout
cmd >> file                    # Append stdout
cmd 2> file                    # Redirect stderr
cmd &> file                    # Redirect both
cmd < file                     # Redirect stdin
cmd1 | cmd2                    # Pipe
cmd 2>&1                       # Stderr to stdout
```

## File Operations

```bash
# Read file line by line
while IFS= read -r line; do
    echo "$line"
done < file.txt

# Write to file
echo "text" > file.txt
echo "text" >> file.txt

# Here document
cat << EOF > file.txt
Line 1
Line 2
EOF
```

## Error Handling

```bash
set -e                         # Exit on error
set -u                         # Exit on undefined var
set -o pipefail                # Exit on pipe failure
set -x                         # Debug mode

trap 'cleanup' EXIT            # Run on exit
trap 'error_handler' ERR       # Run on error
```

## Command Line Arguments

```bash
while getopts "a:b:c" opt; do
    case $opt in
        a) arg_a=$OPTARG ;;
        b) arg_b=$OPTARG ;;
        c) flag_c=true ;;
    esac
done
```

## Common Patterns

### Check Command Exists

```bash
if command -v cmd &> /dev/null; then
    echo "exists"
fi
```

### Check Root

```bash
if [ "$EUID" -ne 0 ]; then
    echo "Run as root"
    exit 1
fi
```

### Temporary File

```bash
tmpfile=$(mktemp)
trap "rm -f $tmpfile" EXIT
```

### Logging

```bash
log() {
    echo "[$(date +'%Y-%m-%d %H:%M:%S')] $*"
}
```

### Yes/No Prompt

```bash
read -p "Continue? (y/n) " -n 1 -r
echo
if [[ $REPLY =~ ^[Yy]$ ]]; then
    echo "Yes"
fi
```

### Retry Logic

```bash
retry=0
max_retry=3
until command; do
    ((retry++))
    [ $retry -ge $max_retry ] && exit 1
    sleep 1
done
```

### Parallel Execution

```bash
command1 &
command2 &
wait
```

### Menu

```bash
select opt in "Opt1" "Opt2" "Quit"; do
    case $opt in
        "Opt1") echo "1" ;;
        "Opt2") echo "2" ;;
        "Quit") break ;;
    esac
done
```

## Text Processing

### grep

```bash
grep "pattern" file            # Search
grep -i "pattern" file         # Case insensitive
grep -r "pattern" dir          # Recursive
grep -v "pattern" file         # Invert match
grep -n "pattern" file         # Line numbers
```

### sed

```bash
sed 's/old/new/' file          # Replace first
sed 's/old/new/g' file         # Replace all
sed -i 's/old/new/g' file      # In-place edit
sed -n '5,10p' file            # Print lines 5-10
sed '/pattern/d' file          # Delete lines
```

### awk

```bash
awk '{print $1}' file          # Print column 1
awk -F: '{print $1}' file      # Custom delimiter
awk 'NR==5' file               # Print line 5
awk '/pattern/' file           # Print matching
awk '{sum+=$1} END {print sum}' file  # Sum column
```

### cut

```bash
cut -d: -f1 file               # Cut by delimiter
cut -c1-5 file                 # Cut characters
```

### sort & uniq

```bash
sort file                      # Sort lines
sort -r file                   # Reverse sort
sort -n file                   # Numeric sort
uniq file                      # Remove duplicates
sort file | uniq -c            # Count occurrences
```

## Debugging

```bash
bash -x script.sh              # Debug mode
bash -n script.sh              # Syntax check
set -x                         # Enable debug
set +x                         # Disable debug
```

## Best Practices

```bash
#!/bin/bash
set -euo pipefail              # Strict mode

# Quote variables
echo "$var"

# Use [[ ]] instead of [ ]
if [[ condition ]]; then
    commands
fi

# Check command success
command || exit 1

# Use functions
function_name() {
    local var="value"
    commands
}

# Add comments
# This does something important
```

## Common Commands

```bash
# File operations
cp, mv, rm, mkdir, touch, chmod, chown, ln

# Text processing
cat, grep, sed, awk, cut, sort, uniq, tr, wc, head, tail

# System info
ps, top, df, du, free, uptime, uname, hostname

# Network
ping, curl, wget, netstat, ss, nc

# Utilities
date, sleep, basename, dirname, find, xargs
```

## Exit Codes

```bash
exit 0                         # Success
exit 1                         # General error
exit 2                         # Misuse
exit 126                       # Cannot execute
exit 127                       # Command not found
exit 130                       # Ctrl+C
```

## Date/Time

```bash
date                           # Current date/time
date +%Y-%m-%d                 # 2024-01-15
date +%H:%M:%S                 # 14:30:45
date +%s                       # Unix timestamp
date -d "yesterday"            # Yesterday
```

## Math Operations

```bash
echo $((5 + 3))                # 8
echo $((10 / 3))               # 3 (integer)
echo "scale=2; 10/3" | bc      # 3.33 (float)
```

## Random Numbers

```bash
$RANDOM                        # Random 0-32767
$((RANDOM % 100))              # Random 0-99
```
