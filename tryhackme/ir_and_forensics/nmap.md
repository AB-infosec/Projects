# Nmap (Network Mapper)

Nmap is a powerful open-source tool used for network discovery and security auditing.
https://www.hackthebox.com/files/cheatsheet-network-enumeration-with-nmap.pdf
## Common Commands and Options

1. Basic scan:
   ```
   nmap 192.168.1.1
   ```

2. Scan multiple targets:
   ```
   nmap 192.168.1.1 192.168.1.2 192.168.1.3
   ```

3. Scan a range of IPs:
   ```
   nmap 192.168.1.1-254
   ```

4. Scan a subnet:
   ```
   nmap 192.168.1.0/24
   ```

5. Scan from a file:
   ```
   nmap -iL targets.txt
   ```

6. Enable OS and version detection:
   ```
   nmap -A 192.168.1.1
   ```

7. Fast scan (100 most common ports):
   ```
   nmap -F 192.168.1.1
   ```

8. SYN scan (stealthier, default for sudo):
   ```
   sudo nmap -sS 192.168.1.1
   ```

## All-Port Scan

To perform an all-port scan:

```
nmap -p- 192.168.1.1
```

This scans all 65535 ports instead of just the 1000 most common ports.

## NSE (Nmap Scripting Engine) Examples

1. Scan for vulnerabilities:
   ```
   nmap --script=vuln 192.168.1.1
   ```

2. Heartbleed vulnerability check:
   ```
   nmap -p 443 --script ssl-heartbleed 192.168.1.1
   ```

3. SQL injection vulnerability scan:
   ```
   nmap -p 80 --script http-sql-injection 192.168.1.1
   ```

4. FTP server vulnerability scan:
   ```
   nmap -p 21 --script ftp-* 192.168.1.1
   ```

5. SMB vulnerability scan:
   ```
   nmap -p 445 --script smb-vuln* 192.168.1.1
   ```
- NSE script categories:
    safe:- Won't affect the target
    intrusive:- Not safe: likely to affect the target
    vuln:- Scan for vulnerabilities
    exploit:- Attempt to exploit a vulnerability
    auth:- Attempt to bypass authentication for running services (e.g. Log into an FTP server anonymously)
    brute:- Attempt to bruteforce credentials for running services
    discovery:- Attempt to query running services for further information about the network (e.g. query an SNMP server).
NSE https://nmap.org/book/nse-usage.html


## Additional Notes

- Filtered state means the port is behind a firewall.
- Firewalls can also send back a RST packet, indicating a closed port (default is drop).
- `-f` option fragments packets to bypass firewalls.
- `-Pn` forces Nmap to consider the target as alive, even if it doesn't respond to ICMP echo requests.
- For a ping sweep: `nmap -sn 192.168.0.1-254`
- `-PP` or `-PM` can request timestamp or address mask, another way to avoid firewall blocks.
- For aggressive scanning: `masscan MACHINE_IP/24 --top-ports 100`

