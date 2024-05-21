# Capture The Flag (CTF) Writeup

## Introduction

This writeup documents my participation in the SecOps 2024 CTF event, held on [Event Date]. The event focused on challenging participants with various cybersecurity tasks across different categories, including Web, Forensics, and Cryptography. I successfully solved [Number of Challenges Solved] challenges, demonstrating a range of techniques and tools.

## Challenges and Solutions

### Challenge Name: Victim Web Server Ports

#### Category: Web

#### Description
Determine the highest open port on the victim webserver.

#### Steps to Solve

##### Tools Used
- Text editor
- Port scanning tools

##### Detailed Steps

1. **Analyzing the .vhd File:**
   - The challenge included a .vhd file, which contained the victim webserver used to compromise the ACME company.
   - Mounted the .vhd file to examine the webserver configuration.

2. **Finding the Configuration File:**
   - Located and read the configuration file using the command:
     ```bash
     sudo cat /mnt/acmewebserver/var/www/html/config.yaml
     ```
   - The configuration file revealed various settings, including port information.

3. **Identifying the Highest Open Port:**
   - Based on the configuration and further port scans, identified the highest open port on the webserver.

##### Solution
The highest open port on the victim webserver was identified.

### Challenge Name: Webserver Engine

#### Category: Web

#### Description
Identify the webserver engine running on the victim website.

#### Steps to Solve

##### Tools Used
- Text editor
- Configuration file analysis

##### Detailed Steps

1. **Analyzing the Configuration File:**
   - From the configuration file located in `/mnt/acmewebserver/var/www/html/config.yaml`, it was determined that the webserver was running nginx.

##### Solution
The webserver engine running on the victim website was identified as nginx.

### Challenge Name: Reverse Shell Listener Port

#### Category: Web

#### Description
Identify the listener port used in the first reverse shell.

#### Steps to Solve

##### Tools Used
- Log file analysis

##### Detailed Steps

1. **Analyzing Log Files:**
   - Examined the log files and the configuration file from the mounted .vhd.
   - Identified that the listener port used in the first reverse shell was the default port 443.

##### Solution
The listener port used in the first reverse shell was identified as port 443.

### Challenge Name: Ransomware Language

#### Category: Web

#### Description
Determine the programming language used for the ransomware.

#### Steps to Solve

##### Tools Used
- nc.exe analysis
- Examination of files within the .vhd

##### Detailed Steps

1. **Analyzing nc.exe:**
   - Investigated the nc.exe file found in the .vhd for clues.
   - The ransom note and executable code provided hints about the programming language used.

2. **Determining the Language:**
   - Based on the file structure and code analysis, determined the programming language used for the ransomware.

##### Solution
The ransomware was written in [Programming Language].

### Additional Findings

#### History File Examination
Analyzed the `.bash_history` file and found several interesting commands executed on the server:
```bash
cat > .bash_history 
exit
cat .bash_history 
exit
cat /etc/nginx/nginx.conf 
exit
sudo systemctl start webapp
exit
systemctl status tcpdumpd.service 
systemctl start tcpdumpd.service 
sudo systemctl start tcpdumpd.service 
systemctl status tcpdumpd.service 
ls
python3 MicrosoftDefenderATPOffboardingLinuxServer_valid_until_2023-12-08.py
sudo python3 MicrosoftDefenderATPOffboardingLinuxServer_valid_until_2023-12-08.py
wget https://github.com/microsoft/mdatp-xplat/raw/master/linux/installation/mde_installer.sh
ls
chmod +x mde_installer.sh 
./mde_installer.sh -r
sudo ./mde_installer.sh -r
systemctl start nginx
sudo systemctl start nginx
sudo systemctl start webapp.service 
ls /var/www/html/
```

### Nginx Logs Examination

#### Attempted to identify the attack vector by examining the nginx logs:
4.180.247.194 - - [30/Nov/2023:12:37:30 +0000] "PROPFIND / HTTP/1.1" 405 150 "-" "Mozilla/5.0 (compatible; Nmap Scripting Engine; https://nmap.org/book/nse.html)"
4.180.247.194 - - [30/Nov/2023:12:37:30 +0000] "GET /HNAP1 HTTP/1.1" 301 162 "-" "Mozilla/5.0 (compatible; Nmap Scripting Engine; https://nmap.org/book/nse.html)"
4.180.247.194 - - [30/Nov/2023:12:37:30 +0000] "GET /evox/about HTTP/1.1" 404 146 "-" "Mozilla/5.0 (compatible; Nmap Scripting Engine; https://nmap.org/book/nse.html)"
4.180.247.194 - - [30/Nov/2023:12:37:30 +0000] "OPTIONS / HTTP/1.1" 301 162 "-" "Mozilla/5.0 (compatible; Nmap Scripting Engine; https://nmap.org/book/nse.html)"
4.180.247.194 - - [30/Nov/2023:12:37:30 +0000] "OPTIONS / HTTP/1.1" 405 150 "-" "Mozilla/5.0 (compatible; Nmap Scripting Engine; https://nmap.org/book/nse.html)"
4.180.247.194 - - [30/Nov/2023:12:37:30 +0000] "OPTIONS / HTTP/1.1" 301 162 "-" "Mozilla/5.0 (compatible; Nmap Scripting Engine; https://nmap.org/book/nse.html)"
4.180.247.194 - - [30/Nov/2023:12:37:30 +0000] "OPTIONS / HTTP/1.1" 301 162 "-" "Mozilla/5.0 (compatible; Nmap Scripting Engine; https://nmap.org/book/nse.html)"
4.180.247.194 - - [30/Nov/2023:12:37:30 +0000] "OPTIONS / HTTP/1.1" 405 150 "-" "Mozilla/5.0 (compatible; Nmap Scripting Engine; https://nmap.org/book/nse.html)"
4.180.247.194 - - [30/Nov/2023:12:37:31 +0000] "OPTIONS / HTTP/1.1" 301 162 "-" "Mozilla/5.0 (compatible; Nmap Scripting Engine; https://nmap.org/book/nse.html)"
4.180.247.194 - - [30/Nov/2023:12:37:31 +0000] "OPTIONS / HTTP/1.1" 301 162 "-" "Mozilla/5.0 (compatible; Nmap Scripting Engine; https://nmap.org/book/nse.html)"
4.180.247.194 - - [30/Nov/2023:12:37:35 +0000] "GET / HTTP/1.1" 301 162 "-" "-"
141.98.7.250 - - [30/Nov/2023:12:43:31 +0000] "CONNECT blog.roblox.com:443 HTTP/1.1" 400 150 "-" "-"
216.218.206.69 - - [30/Nov/2023:12:51:39 +0000] "GET / HTTP/1.1" 200 7912 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/110.0.0.0 Safari/537.36 Edg/110.0.1587.57"
216.218.206.69 - - [30/Nov/2023:13:00:16 +0000] "GET / HTTP/1.1" 200 7912 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/110.0.0.0 Safari/537.36 Edg/110.0.1587.57"
198.199.118.121 - - [30/Nov/2023:13:42:20 +0000] "GET / HTTP/1.1" 301 162 "-" "Mozilla/5.0 zgrab/0.x"
52.56.121.155 - - [30/Nov/2023:13:57:56 +0000] "\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00" 400 150 "-" "-"
52.56.121.155 - - [30/Nov/2023:13:59:22 +0000] "\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00" 400 150 "-" "-"
78.31.238.8 - - [30/Nov/2023:14:00:52 +0000] "GET / HTTP/2.0" 200 7899 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/119.0.0.0 Safari/537.36"
78.31.238.8 - - [30/Nov/2023:14:00:52 +0000] "GET /favicon.ico HTTP/2.0" 200 3870 "https://0x00.hu/" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/119.0.0.0 Safari/537.36"
35.177.229.240 - - [30/Nov/2023:14:15:23 +0000] "\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00" 400 150 "-" "-"
57.129.23.166 - - [30/Nov/2023:14:16:37 +0000] "GET /.env HTTP/1.1" 301 162 "-" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/81.0.4044.129 Safari/537.36"
35.177.229.240 - - [30/Nov/2023:14:17:07 +0000] "\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00" 400 150 "-" "-"
57.129.23.166 - - [30/Nov/2023:14:16:37 +0000] "POST / HTTP/1.1" 301 162 "-" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/81.0.4044.129 Safari/537.36"
52.233.242.214 - - [30/Nov/2023:14:48:39 +0000] "GET /robots.txt HTTP/2.0" 200 78 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/119.0.0.0 Safari/537.36 Edg/119.0.0.0"
185.180.143.6 - - [30/Nov/2023:14:55:29 +0000] "GET / HTTP/1.1" 301 162 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/60.0.3112.113 Safari/537.36"
185.180.143.6 - - [30/Nov/2023:14:55:30 +0000] "GET /cf_scripts/scripts/ajax/ckeditor/ckeditor.js HTTP/1.1" 301 162 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/60.0.3112.113 Safari/537.36"
78.153.140.222 - - [30/Nov/2023:15:02:39 +0000] "GET / HTTP/1.1" 400 248 "-" "SAMSUNG-S8000/S8000XXIF3 SHP/VPP/R5 Jasmine/1.0 Nextreaming SMM-MMS/1.2.0 profile/MIDP-2.1 configuration/CLDC-1.1 FirePHP/0.3"
78.153.140.222 - - [30/Nov/2023:15:02:39 +0000] "GET / HTTP/1.1" 200 7912 "-" "Mozilla/5.0 (Linux; Android 4.4.2; en-us; SAMSUNG SGH-M919 Build/KOT49H) AppleWebKit/537.36 (KHTML, like Gecko) Version/1.5 Chrome/28.0.1500.94 Mobile Safari/537.36"
78.153.140.222 - - [30/Nov/2023:15:02:39 +0000] "GET /admin/config.php HTTP/1.1" 404 117 "-" "Mozilla/5.0 (Windows NT 6.1; WOW64; rv:38.0) Gecko/20100101 IceDragon/38.0.5 Firefox/38.0.5"
78.153.140.222 - - [30/Nov/2023:15:02:39 +0000] "GET /admin/config.php HTTP/1.1" 400 248 "-" "Mozilla/5.0 (Linux; U; Android 4.1.2; en-us; SCH-R760X Build/JZO54K) AppleWebKit/534.30 (KHTML, like Gecko) Version/4.0 Mobile Safari/534.30"
107.170.232.47 - - [30/Nov/2023:15:07:25 +0000] "GET /actuator/health HTTP/1.1" 301 162 "-" "Mozilla/5.0 zgrab/0.x"
45.135.232.36 - - [30/Nov/2023:15:08:52 +0000] "GET / HTTP/1.1" 200 7912 "-" "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/535.7 (KHTML, like Gecko) Chrome/16.0.912.36 Safari/535.7"
57.129.23.166 - - [30/Nov/2023:15:14:19 +0000] "GET /.env HTTP/1.1" 301 162 "-" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/81.0.4044.129 Safari/537.36"
57.129.23.166 - - [30/Nov/2023:15:14:19 +0000] "POST / HTTP/1.1" 301 162 "-" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/81.0.4044.129 Safari/537.36"


