### NMAP EXAMPLES

```
# Scan a System with Hostname and IP Address

nmap server2.tecmint.com

# Scan Multiple Hosts

nmap 192.168.0.101 192.168.0.102 192.168.0.103

# Scan a whole Subnet

 nmap 192.168.0.*

#  Scan Multiple Servers using last octet of IP address

nmap 192.168.0.101,102,103

#  Scan list of Hosts from a File

nmap -iL nmaptest.txt

# Scan an IP Address Range

nmap 192.168.0.101-110

# Scan Network Excluding Remote Hosts

nmap 192.168.0.* --exclude 192.168.0.100

# Scan OS information and Traceroute

nmap -A 192.168.0.101

# Enable OS Detection with Nmap

nmap -O server2.tecmint.com

# Scan a Host to Detect Firewall

nmap -sA 192.168.0.101

# Scan a Host to check its protected by Firewall

nmap -PN 192.168.0.101

## Find out Live hosts in a Network

nmap -sP 192.168.0.*

# Perform a Fast Scan

nmap -F 192.168.0.101

# Scan Ports Consecutively

nmap -r 192.168.0.101

# Print Host interfaces and Routes

nmap --iflist

# Scan for specific Port

nmap -p 80 server2.tecmint.com

# Scan a TCP Port

nmap -p T:8888,80 server2.tecmint.com

#  Scan a UDP Port

nmap -sU 53 server2.tecmint.com

# Scan Multiple Ports

nmap -p 80,443 192.168.0.101

# Scan Ports by Network Range

nmap -p 80-160 192.168.0.101

#  Find Host Services version Numbers

nmap -sV 192.168.0.101

## Scan remote hosts using TCP ACK (PA) and TCP Syn (PS)

nmap -PS 192.168.0.101

# Scan Remote host for specific ports with TCP ACK

nmap -PA -p 22,80 192.168.0.101

# Scan Remote host for specific ports with TCP Syn

 nmap -PS -p 22,80 192.168.0.101

# Remote host for specific ports with TCP Syn

 nmap -PS -p 22,80 192.168.0.101

## Perform a stealthy Scan

nmap -sS 192.168.0.101

# Check most commonly used Ports with TCP Syn

 nmap -sT 192.168.0.101

# Perform a tcp null scan to fool a firewall

nmap -sN 192.168.0.101

```
