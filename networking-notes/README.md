# Networking Notes

Comprehensive networking concepts, protocols, and command reference guide.

## Contents

1. **[Networking](1_networking.md)**
   - OSI and TCP/IP models
   - IP addressing and subnetting
   - Network protocols (TCP, UDP, ICMP)
   - Network commands and configuration
   - Routing and DNS
   - Firewall management
   - Network troubleshooting
   - Performance testing

2. **[Cheatsheet](2_networking_cheatsheet.md)**
   - Quick reference commands
   - Common ports
   - Subnetting reference
   - Troubleshooting steps
   - Network files locations

## Quick Start

For beginners:

- Start with OSI Model and TCP/IP Model in networking.md
- Learn IP addressing and subnetting basics
- Practice with commands in cheatsheet.md

For network administrators:

- Review network configuration and routing
- Study firewall management
- Master troubleshooting techniques
- Use cheatsheet.md for quick command reference

## Common Tasks

### Test Connectivity

```bash
ping host
traceroute host
mtr host
```

### Check Network Configuration

```bash
ip addr
ip route
cat /etc/resolv.conf
```

### Monitor Network

```bash
netstat -tuln
ss -tunap
tcpdump -i eth0
```

### Troubleshoot Issues

```bash
ping 127.0.0.1
ping gateway
ping 8.8.8.8
ping google.com
```

## Usage

Use `Ctrl+F` to search for specific commands, ports, or topics within each file.

## Contributing

Feel free to add more commands, examples, or corrections to improve these networking notes.
