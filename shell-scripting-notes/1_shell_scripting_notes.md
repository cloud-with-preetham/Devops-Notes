# Shell Scripting

## Basics

### Shebang

```bash
#!/bin/bash
#!/bin/sh
#!/usr/bin/env bash
```

### Hello World

```bash
#!/bin/bash
echo "Hello, World!"
```

### Make Script Executable

```bash
chmod +x script.sh
./script.sh
```

## Variables

### Declaring Variables

```bash
name="John"
age=25
readonly PI=3.14
```

### Using Variables

```bash
echo $name
echo ${name}
echo "My name is $name"
echo "My name is ${name}"
```

### Command Substitution

```bash
current_date=$(date)
files=$(ls -l)
user=`whoami`  # Old style
```

### Special Variables

```bash
$0  # Script name
$1, $2, $3  # Positional parameters
$#  # Number of arguments
$@  # All arguments as separate words
$*  # All arguments as single word
$?  # Exit status of last command
$$  # Process ID of script
$!  # Process ID of last background command
```

## Input/Output

### Reading Input

```bash
read name
read -p "Enter your name: " name
read -sp "Enter password: " password  # Silent
read -t 5 name  # Timeout after 5 seconds
```

### Output

```bash
echo "Hello"
echo -n "No newline"
echo -e "Line1\nLine2"  # Enable escape sequences
printf "Name: %s, Age: %d\n" "$name" "$age"
```

## Conditionals

### If Statement

```bash
if [ condition ]; then
    # commands
fi

if [ condition ]; then
    # commands
else
    # commands
fi

if [ condition1 ]; then
    # commands
elif [ condition2 ]; then
    # commands
else
    # commands
fi
```

### Test Operators

#### String Comparison

```bash
[ "$a" = "$b" ]   # Equal
[ "$a" != "$b" ]  # Not equal
[ -z "$a" ]       # Empty string
[ -n "$a" ]       # Not empty string
```

#### Numeric Comparison

```bash
[ "$a" -eq "$b" ]  # Equal
[ "$a" -ne "$b" ]  # Not equal
[ "$a" -lt "$b" ]  # Less than
[ "$a" -le "$b" ]  # Less than or equal
[ "$a" -gt "$b" ]  # Greater than
[ "$a" -ge "$b" ]  # Greater than or equal
```

#### File Tests

```bash
[ -e file ]  # Exists
[ -f file ]  # Regular file
[ -d file ]  # Directory
[ -r file ]  # Readable
[ -w file ]  # Writable
[ -x file ]  # Executable
[ -s file ]  # Not empty
[ -L file ]  # Symbolic link
```

#### Logical Operators

```bash
[ condition1 ] && [ condition2 ]  # AND
[ condition1 ] || [ condition2 ]  # OR
[ ! condition ]                    # NOT
[ condition1 -a condition2 ]       # AND (inside test)
[ condition1 -o condition2 ]       # OR (inside test)
```

### Case Statement

```bash
case $variable in
    pattern1)
        # commands
        ;;
    pattern2)
        # commands
        ;;
    *)
        # default
        ;;
esac
```

## Loops

### For Loop

```bash
for i in 1 2 3 4 5; do
    echo $i
done

for i in {1..5}; do
    echo $i
done

for i in {0..10..2}; do  # Step by 2
    echo $i
done

for file in *.txt; do
    echo $file
done

for ((i=0; i<5; i++)); do
    echo $i
done
```

### While Loop

```bash
while [ condition ]; do
    # commands
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
    # commands
done

counter=0
until [ $counter -ge 5 ]; do
    echo $counter
    ((counter++))
done
```

### Loop Control

```bash
break     # Exit loop
continue  # Skip to next iteration
```

## Functions

### Defining Functions

```bash
function_name() {
    # commands
}

function function_name {
    # commands
}
```

### Function with Parameters

```bash
greet() {
    echo "Hello, $1!"
}
greet "John"

add() {
    local result=$(($1 + $2))
    echo $result
}
sum=$(add 5 3)
```

### Return Values

```bash
is_even() {
    if [ $(($1 % 2)) -eq 0 ]; then
        return 0  # True
    else
        return 1  # False
    fi
}

if is_even 4; then
    echo "Even"
fi
```

## Arrays

### Declaring Arrays

```bash
arr=(1 2 3 4 5)
arr[0]=1
arr[1]=2
```

### Accessing Arrays

```bash
echo ${arr[0]}      # First element
echo ${arr[@]}      # All elements
echo ${arr[*]}      # All elements
echo ${#arr[@]}     # Array length
echo ${!arr[@]}     # Array indices
```

### Array Operations

```bash
arr+=(6)            # Append
unset arr[2]        # Delete element
arr=(${arr[@]} 7)   # Append

# Loop through array
for item in "${arr[@]}"; do
    echo $item
done
```

## String Operations

### String Length

```bash
str="Hello"
echo ${#str}  # 5
```

### Substring

```bash
str="Hello World"
echo ${str:0:5}   # Hello
echo ${str:6}     # World
```

### String Replacement

```bash
str="Hello World"
echo ${str/World/Universe}  # Replace first
echo ${str//o/O}            # Replace all
```

### String Removal

```bash
str="Hello World"
echo ${str#Hello }   # Remove from start
echo ${str%World}    # Remove from end
```

### Case Conversion

```bash
str="Hello World"
echo ${str^^}  # HELLO WORLD (uppercase)
echo ${str,,}  # hello world (lowercase)
```

## Arithmetic

### Arithmetic Operations

```bash
a=5
b=3

# Using $(( ))
result=$((a + b))
result=$((a - b))
result=$((a * b))
result=$((a / b))
result=$((a % b))

# Using let
let result=a+b
let result++
let result--

# Using expr
result=$(expr $a + $b)
```

## File Operations

### Reading Files

```bash
# Line by line
while IFS= read -r line; do
    echo "$line"
done < file.txt

# All at once
content=$(cat file.txt)
```

### Writing Files

```bash
echo "text" > file.txt   # Overwrite
echo "text" >> file.txt  # Append
cat > file.txt << EOF
Line 1
Line 2
EOF
```

### File Checks

```bash
if [ -f "file.txt" ]; then
    echo "File exists"
fi

if [ ! -f "file.txt" ]; then
    echo "File does not exist"
fi
```

## Redirection

### Standard Streams

```bash
command > file       # Redirect stdout
command 2> file      # Redirect stderr
command &> file      # Redirect both
command >> file      # Append stdout
command 2>&1         # Redirect stderr to stdout
command < file       # Redirect stdin
command | command2   # Pipe
```

### Here Documents

```bash
cat << EOF
Line 1
Line 2
EOF

cat << 'EOF'  # No variable expansion
$variable
EOF
```

## Error Handling

### Exit Status

```bash
command
if [ $? -eq 0 ]; then
    echo "Success"
else
    echo "Failed"
fi
```

### Exit on Error

```bash
set -e  # Exit on error
set -u  # Exit on undefined variable
set -o pipefail  # Exit on pipe failure
set -x  # Debug mode (print commands)
```

### Trap

```bash
trap 'echo "Error occurred"' ERR
trap 'cleanup' EXIT
trap 'echo "Interrupted"' INT TERM
```

## Command Line Arguments

### Parsing Arguments

```bash
while getopts "a:b:c" opt; do
    case $opt in
        a) arg_a=$OPTARG ;;
        b) arg_b=$OPTARG ;;
        c) flag_c=true ;;
        \?) echo "Invalid option" ;;
    esac
done
```

### Shift Arguments

```bash
while [ $# -gt 0 ]; do
    case $1 in
        --option)
            option=$2
            shift 2
            ;;
        *)
            shift
            ;;
    esac
done
```

## Useful Patterns

### Check if Command Exists

```bash
if command -v git &> /dev/null; then
    echo "Git is installed"
fi
```

### Run as Root

```bash
if [ "$EUID" -ne 0 ]; then
    echo "Please run as root"
    exit 1
fi
```

### Create Temporary File

```bash
tmpfile=$(mktemp)
trap "rm -f $tmpfile" EXIT
```

### Logging

```bash
log() {
    echo "[$(date +'%Y-%m-%d %H:%M:%S')] $*"
}
log "Script started"
```

### Progress Bar

```bash
for i in {1..100}; do
    echo -ne "\rProgress: $i%"
    sleep 0.1
done
echo
```

### Menu

```bash
select option in "Option 1" "Option 2" "Quit"; do
    case $option in
        "Option 1") echo "Selected 1" ;;
        "Option 2") echo "Selected 2" ;;
        "Quit") break ;;
    esac
done
```

## Best Practices

1. **Always quote variables**: `"$variable"`
2. **Use `[[` instead of `[`** for better features
3. **Check command success**: `command || exit 1`
4. **Use meaningful variable names**
5. **Add comments for complex logic**
6. **Use functions for reusable code**
7. **Handle errors gracefully**
8. **Use `shellcheck` for linting**
9. **Make scripts idempotent when possible**
10. **Use `set -euo pipefail` for safety**

## Debugging

```bash
bash -x script.sh        # Debug mode
bash -n script.sh        # Check syntax
set -x                   # Enable debug in script
set +x                   # Disable debug
echo "Debug: var=$var"   # Print debug info
```

## Common Commands in Scripts

```bash
# File operations
cp, mv, rm, mkdir, touch, chmod, chown

# Text processing
grep, sed, awk, cut, sort, uniq, tr, wc

# System
ps, kill, top, df, du, free, uptime

# Network
ping, curl, wget, netstat, ss

# Utilities
date, sleep, basename, dirname, realpath
```
