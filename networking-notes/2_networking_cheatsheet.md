# Networking Cheatsheet

## Quick Diagnostics

- `ping host` - Test connectivity
- `ping -c 4 host` - Send 4 packets
- `traceroute host` - Trace route to host
- `mtr host` - Real-time network diagnostic
- `nslookup domain` - DNS lookup
- `dig domain` - Detailed DNS query
- `host domain` - Simple DNS lookup

## IP Configuration

- `ip addr` - Show all IP addresses
- `ip addr show eth0` - Show specific interface
- `ip link` - Show network interfaces
- `ip link set eth0 up/down` - Enable/disable interface
- `ifconfig` - Legacy interface info
- `hostname -I` - Show IP addresses

## Routing

- `ip route` - Show routing table
- `ip route add 192.168.2.0/24 via 192.168.1.1` - Add route
- `ip route del 192.168.2.0/24` - Delete route
- `ip route add default via 192.168.1.1` - Set default gateway
- `route -n` - Show routes (numeric)

## Ports and Connections

- `netstat -tuln` - Listening ports
- `netstat -tunap` - All connections with PIDs
- `ss -tuln` - Socket statistics (faster)
- `ss -tunap` - All sockets with PIDs
- `lsof -i :80` - What's using port 80
- `lsof -i` - All network connections

## Port Testing

- `nc -zv host 80` - Test single port
- `nc -zv host 1-1000` - Scan port range
- `telnet host port` - Test port connectivity
- `nmap host` - Network scan
- `nmap -p 80,443 host` - Scan specific ports

## DNS

- `nslookup domain` - Basic DNS lookup
- `nslookup domain 8.8.8.8` - Query specific DNS server
- `dig domain` - Detailed DNS info
- `dig domain +short` - Short answer only
- `dig @8.8.8.8 domain` - Query specific server
- `cat /etc/resolv.conf` - View DNS config
- `cat /etc/hosts` - View local DNS entries

## Packet Capture

- `tcpdump -i eth0` - Capture on interface
- `tcpdump -i eth0 port 80` - Capture specific port
- `tcpdump -i eth0 host 192.168.1.1` - Capture specific host
- `tcpdump -i eth0 -w file.pcap` - Save to file
- `tcpdump -r file.pcap` - Read from file
- `tcpdump -i eth0 -n` - Don't resolve names

## Bandwidth Monitoring

- `iftop` - Real-time bandwidth by connection
- `iftop -i eth0` - Monitor specific interface
- `nethogs` - Bandwidth by process
- `vnstat` - Network traffic statistics
- `speedtest-cli` - Internet speed test
- `iperf3 -s` - Start iperf server
- `iperf3 -c server` - Test to server

## ARP

- `arp -a` - Show ARP cache
- `arp -n` - Show ARP (no DNS)
- `ip neigh` - Show neighbor table
- `ip neigh flush all` - Clear ARP cache
- `arp -d host` - Delete ARP entry

## File Transfer

- `scp file user@host:/path` - Secure copy
- `scp -r dir user@host:/path` - Copy directory
- `rsync -avz source dest` - Sync files
- `rsync -avz -e ssh source user@host:dest` - Remote sync
- `wget url` - Download file
- `curl -O url` - Download file
- `curl url` - Display content

## SSH

- `ssh user@host` - Connect to host
- `ssh -p 2222 user@host` - Custom port
- `ssh -i key.pem user@host` - Use key file
- `ssh -L 8080:localhost:80 user@host` - Local port forward
- `ssh -R 8080:localhost:80 user@host` - Remote port forward
- `ssh -D 1080 user@host` - SOCKS proxy
- `ssh-keygen` - Generate SSH key
- `ssh-copy-id user@host` - Copy SSH key

## Firewall (iptables)

- `iptables -L` - List rules
- `iptables -L -n -v` - Detailed list
- `iptables -A INPUT -p tcp --dport 80 -j ACCEPT` - Allow port
- `iptables -D INPUT 1` - Delete rule 1
- `iptables -F` - Flush all rules
- `iptables-save > rules.txt` - Save rules
- `iptables-restore < rules.txt` - Restore rules

## Firewall (UFW)

- `ufw status` - Show status
- `ufw enable` - Enable firewall
- `ufw disable` - Disable firewall
- `ufw allow 80` - Allow port 80
- `ufw allow 22/tcp` - Allow TCP port 22
- `ufw deny 23` - Deny port 23
- `ufw delete allow 80` - Remove rule
- `ufw allow from 192.168.1.0/24` - Allow subnet

## Firewall (firewalld)

- `firewall-cmd --state` - Check status
- `firewall-cmd --list-all` - List settings
- `firewall-cmd --add-port=80/tcp --permanent` - Add port
- `firewall-cmd --remove-port=80/tcp --permanent` - Remove port
- `firewall-cmd --reload` - Reload config

## NetworkManager (nmcli)

- `nmcli device status` - Device status
- `nmcli connection show` - Show connections
- `nmcli connection up eth0` - Activate connection
- `nmcli connection down eth0` - Deactivate
- `nmcli device wifi list` - List WiFi networks
- `nmcli device wifi connect SSID password PASS` - Connect WiFi

## DHCP

- `dhclient eth0` - Request IP via DHCP
- `dhclient -r eth0` - Release DHCP IP
- `dhclient -v eth0` - Verbose DHCP request

## Network Information

- `hostname` - Show hostname
- `hostname -I` - Show IP addresses
- `hostname -f` - Show FQDN
- `cat /etc/hostname` - View hostname file
- `cat /etc/resolv.conf` - View DNS config
- `cat /etc/hosts` - View hosts file

## Subnetting Quick Reference

- `/24` = 255.255.255.0 = 256 IPs (254 usable)
- `/25` = 255.255.255.128 = 128 IPs (126 usable)
- `/26` = 255.255.255.192 = 64 IPs (62 usable)
- `/27` = 255.255.255.224 = 32 IPs (30 usable)
- `/28` = 255.255.255.240 = 16 IPs (14 usable)
- `/29` = 255.255.255.248 = 8 IPs (6 usable)
- `/30` = 255.255.255.252 = 4 IPs (2 usable)

## Common Ports

- `20/21` - FTP
- `22` - SSH
- `23` - Telnet
- `25` - SMTP
- `53` - DNS
- `80` - HTTP
- `110` - POP3
- `143` - IMAP
- `443` - HTTPS
- `3306` - MySQL
- `5432` - PostgreSQL
- `6379` - Redis
- `8080` - HTTP Alt
- `27017` - MongoDB

## Private IP Ranges

- `10.0.0.0/8` - Class A (10.0.0.0 - 10.255.255.255)
- `172.16.0.0/12` - Class B (172.16.0.0 - 172.31.255.255)
- `192.168.0.0/16` - Class C (192.168.0.0 - 192.168.255.255)

## Troubleshooting Steps

1. `ping 127.0.0.1` - Test loopback
2. `ping gateway` - Test local network
3. `ping 8.8.8.8` - Test internet (IP)
4. `ping google.com` - Test DNS resolution
5. `traceroute destination` - Find routing issues
6. `netstat -tuln` - Check listening services
7. `iptables -L` - Check firewall rules

## Network Files

- `/etc/network/interfaces` - Network config (Debian)
- `/etc/sysconfig/network-scripts/` - Network config (RHEL)
- `/etc/resolv.conf` - DNS servers
- `/etc/hosts` - Static hostname mapping
- `/etc/hostname` - System hostname
- `/etc/nsswitch.conf` - Name service switch

## Performance Testing

- `ping -c 100 host` - Latency test
- `mtr host` - Continuous monitoring
- `iperf3 -s` - Start bandwidth test server
- `iperf3 -c server` - Run bandwidth test
- `speedtest-cli` - Internet speed test

## Network Statistics

- `netstat -s` - Protocol statistics
- `ss -s` - Socket statistics summary
- `ip -s link` - Interface statistics
- `cat /proc/net/dev` - Network device stats

## MAC Address

- `ip link show` - Show MAC addresses
- `ifconfig | grep ether` - Show MAC (legacy)
- `cat /sys/class/net/eth0/address` - Show specific MAC

## MTU

- `ip link show` - Show MTU
- `ip link set eth0 mtu 1400` - Set MTU
- `ping -M do -s 1472 host` - Test MTU size

## Network Restart

- `systemctl restart networking` - Debian/Ubuntu
- `systemctl restart network` - RHEL/CentOS
- `systemctl restart NetworkManager` - NetworkManager
- `ifdown eth0 && ifup eth0` - Restart interface
