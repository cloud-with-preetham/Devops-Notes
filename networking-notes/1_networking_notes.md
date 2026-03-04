# Networking

## OSI Model

### 7 Layers

1. **Physical** - Cables, signals, bits
2. **Data Link** - MAC addresses, switches, frames
3. **Network** - IP addresses, routers, packets
4. **Transport** - TCP/UDP, ports, segments
5. **Session** - Connection management
6. **Presentation** - Data formatting, encryption
7. **Application** - HTTP, FTP, DNS, SMTP

## TCP/IP Model

### 4 Layers

1. **Network Access** - Physical + Data Link
2. **Internet** - IP, ICMP, routing
3. **Transport** - TCP, UDP
4. **Application** - HTTP, DNS, FTP, SSH

## IP Addressing

### IPv4

- Format: `192.168.1.1`
- 32-bit address (4 octets)
- Classes: A, B, C, D, E
- Private ranges:
  - Class A: `10.0.0.0/8`
  - Class B: `172.16.0.0/12`
  - Class C: `192.168.0.0/16`

### IPv6

- Format: `2001:0db8:85a3:0000:0000:8a2e:0370:7334`
- 128-bit address (8 groups)
- Simplified: `2001:db8:85a3::8a2e:370:7334`

### Subnetting

- CIDR notation: `192.168.1.0/24`
- Subnet mask: `255.255.255.0`
- `/24` = 256 addresses (254 usable)
- `/25` = 128 addresses (126 usable)
- `/26` = 64 addresses (62 usable)
- `/27` = 32 addresses (30 usable)
- `/28` = 16 addresses (14 usable)
- `/29` = 8 addresses (6 usable)
- `/30` = 4 addresses (2 usable)

## Network Commands

### Connectivity Testing

- `ping host` - Test connectivity
- `ping -c 4 host` - Send 4 packets
- `ping6 host` - IPv6 ping
- `traceroute host` - Trace route
- `tracepath host` - Trace path
- `mtr host` - Network diagnostic tool

### Network Configuration

- `ip addr` - Show IP addresses
- `ip addr show eth0` - Show specific interface
- `ip addr add 192.168.1.10/24 dev eth0` - Add IP
- `ip addr del 192.168.1.10/24 dev eth0` - Remove IP
- `ip link` - Show network interfaces
- `ip link set eth0 up` - Enable interface
- `ip link set eth0 down` - Disable interface
- `ifconfig` - Legacy interface config
- `ifconfig eth0 192.168.1.10 netmask 255.255.255.0` - Set IP

### Routing

- `ip route` - Show routing table
- `ip route show` - Display routes
- `ip route add 192.168.2.0/24 via 192.168.1.1` - Add route
- `ip route del 192.168.2.0/24` - Delete route
- `ip route add default via 192.168.1.1` - Add default gateway
- `route -n` - Show routing table (legacy)

### DNS

- `nslookup domain` - DNS lookup
- `nslookup domain 8.8.8.8` - Query specific DNS
- `dig domain` - DNS query tool
- `dig domain +short` - Short output
- `dig @8.8.8.8 domain` - Query specific DNS
- `host domain` - DNS lookup
- `cat /etc/resolv.conf` - DNS configuration
- `cat /etc/hosts` - Local DNS entries

### Network Statistics

- `netstat -tuln` - Listening ports
- `netstat -tunap` - All connections with PIDs
- `netstat -r` - Routing table
- `netstat -i` - Interface statistics
- `ss -tuln` - Socket statistics
- `ss -tunap` - All sockets with PIDs
- `ss -s` - Summary statistics

### Port Scanning

- `nc -zv host 80` - Check port 80
- `nc -zv host 1-1000` - Scan ports 1-1000
- `nmap host` - Network scan
- `nmap -p 80,443 host` - Scan specific ports
- `nmap -sV host` - Service version detection
- `telnet host port` - Test port connectivity

### Network Monitoring

- `tcpdump` - Packet capture
- `tcpdump -i eth0` - Capture on interface
- `tcpdump -i eth0 port 80` - Capture port 80
- `tcpdump -i eth0 -w capture.pcap` - Save to file
- `tcpdump -r capture.pcap` - Read from file
- `iftop` - Bandwidth monitoring
- `nethogs` - Per-process bandwidth
- `vnstat` - Network traffic monitor

### ARP

- `arp -a` - Show ARP cache
- `arp -n` - Show ARP (numeric)
- `ip neigh` - Show neighbor table
- `ip neigh flush all` - Clear ARP cache

### Network Files

- `/etc/network/interfaces` - Network config (Debian)
- `/etc/sysconfig/network-scripts/` - Network config (RHEL)
- `/etc/resolv.conf` - DNS servers
- `/etc/hosts` - Static hostname mapping
- `/etc/hostname` - System hostname
- `/etc/nsswitch.conf` - Name service switch

## Protocols

### TCP (Transmission Control Protocol)

- Connection-oriented
- Reliable delivery
- Flow control
- Error checking
- Used by: HTTP, HTTPS, FTP, SSH, SMTP

### UDP (User Datagram Protocol)

- Connectionless
- Fast, no overhead
- No guaranteed delivery
- Used by: DNS, DHCP, VoIP, streaming

### ICMP (Internet Control Message Protocol)

- Network diagnostics
- Ping, traceroute
- Error reporting

## Common Ports

### Well-Known Ports (0-1023)

- `20/21` - FTP
- `22` - SSH
- `23` - Telnet
- `25` - SMTP
- `53` - DNS
- `67/68` - DHCP
- `80` - HTTP
- `110` - POP3
- `143` - IMAP
- `443` - HTTPS
- `465` - SMTPS
- `587` - SMTP (submission)
- `993` - IMAPS
- `995` - POP3S
- `3306` - MySQL
- `5432` - PostgreSQL
- `6379` - Redis
- `8080` - HTTP alternate
- `27017` - MongoDB

## Network Tools

### File Transfer

- `scp file user@host:/path` - Secure copy
- `scp -r dir user@host:/path` - Copy directory
- `rsync -avz source dest` - Sync files
- `rsync -avz source user@host:dest` - Remote sync
- `wget url` - Download file
- `curl url` - Transfer data
- `curl -O url` - Download file
- `ftp host` - FTP client
- `sftp user@host` - Secure FTP

### Remote Access

- `ssh user@host` - SSH connection
- `ssh -p 2222 user@host` - Custom port
- `ssh -i key.pem user@host` - Use key file
- `ssh -L 8080:localhost:80 user@host` - Local port forward
- `ssh -R 8080:localhost:80 user@host` - Remote port forward
- `ssh -D 1080 user@host` - SOCKS proxy

## Network Configuration

### Static IP (Debian/Ubuntu)

```
/etc/network/interfaces:
auto eth0
iface eth0 inet static
    address 192.168.1.10
    netmask 255.255.255.0
    gateway 192.168.1.1
    dns-nameservers 8.8.8.8 8.8.4.4
```

### Static IP (RHEL/CentOS)

```
/etc/sysconfig/network-scripts/ifcfg-eth0:
DEVICE=eth0
BOOTPROTO=static
IPADDR=192.168.1.10
NETMASK=255.255.255.0
GATEWAY=192.168.1.1
DNS1=8.8.8.8
ONBOOT=yes
```

### NetworkManager

- `nmcli device status` - Device status
- `nmcli connection show` - Show connections
- `nmcli connection up eth0` - Activate connection
- `nmcli connection down eth0` - Deactivate connection
- `nmcli device wifi list` - List WiFi networks
- `nmcli device wifi connect SSID password PASSWORD` - Connect WiFi

## Firewall

### iptables

- `iptables -L` - List rules
- `iptables -A INPUT -p tcp --dport 80 -j ACCEPT` - Allow port 80
- `iptables -A INPUT -s 192.168.1.0/24 -j ACCEPT` - Allow subnet
- `iptables -A INPUT -j DROP` - Drop all other
- `iptables -D INPUT 1` - Delete rule 1
- `iptables -F` - Flush all rules
- `iptables-save > /etc/iptables.rules` - Save rules
- `iptables-restore < /etc/iptables.rules` - Restore rules

### UFW (Uncomplicated Firewall)

- `ufw status` - Show status
- `ufw enable` - Enable firewall
- `ufw disable` - Disable firewall
- `ufw allow 80` - Allow port 80
- `ufw allow 22/tcp` - Allow TCP port 22
- `ufw deny 23` - Deny port 23
- `ufw delete allow 80` - Remove rule
- `ufw allow from 192.168.1.0/24` - Allow subnet

### firewalld

- `firewall-cmd --state` - Check status
- `firewall-cmd --list-all` - List all settings
- `firewall-cmd --add-port=80/tcp` - Add port
- `firewall-cmd --add-port=80/tcp --permanent` - Add permanently
- `firewall-cmd --remove-port=80/tcp` - Remove port
- `firewall-cmd --reload` - Reload configuration

## Network Troubleshooting

### Common Issues

1. **No connectivity**: Check cable, IP, gateway
2. **Slow network**: Check bandwidth, latency
3. **DNS issues**: Check resolv.conf, test DNS
4. **Routing issues**: Check routing table
5. **Firewall blocking**: Check firewall rules

### Diagnostic Steps

1. `ping 127.0.0.1` - Test loopback
2. `ping gateway` - Test local network
3. `ping 8.8.8.8` - Test internet (IP)
4. `ping google.com` - Test DNS
5. `traceroute destination` - Find where packets fail
6. `netstat -tuln` - Check listening services
7. `tcpdump` - Capture and analyze packets

## Network Performance

### Bandwidth Testing

- `iperf3 -s` - Start server
- `iperf3 -c server` - Test to server
- `speedtest-cli` - Internet speed test

### Latency Testing

- `ping -c 100 host` - Average latency
- `mtr host` - Continuous monitoring

## DHCP

### DHCP Client

- `dhclient eth0` - Request IP
- `dhclient -r eth0` - Release IP
- `cat /var/lib/dhcp/dhclient.leases` - View leases

## VLANs

### VLAN Configuration

- `ip link add link eth0 name eth0.10 type vlan id 10` - Create VLAN
- `ip addr add 192.168.10.1/24 dev eth0.10` - Add IP to VLAN
- `ip link set eth0.10 up` - Enable VLAN interface

## Bonding/Teaming

### Network Bonding

- Combine multiple interfaces
- Modes: balance-rr, active-backup, balance-xor, broadcast, 802.3ad
- Provides redundancy and increased bandwidth
