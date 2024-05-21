# Capture The Flag (CTF) Writeup

## Introduction

This writeup documents my participation in the SecOps 2024 CTF event, held on 17/05/2024. The event focused on challenging participants with various cybersecurity tasks across different categories, including Web, Forensics, and Cryptography. I successfully solved [Number of Challenges Solved] challenges, demonstrating a range of techniques and tools.

## Challenges and Solutions

### Challenge Name: Phishing Attachment Compression

#### Category: Forensics

#### Description
This challenge involved analyzing a phishing email to determine the tool used to create the phishing attachment, which was a Self-Extracting Archive (SFX).

#### Steps to Solve

##### Tools Used
- munpack
- text editor
- unzip
- strings
- VirusTotal
- Google
- binwalk
- foremost

##### Detailed Steps

1. **Extracting the .eml File:**
   - Used `munpack -f -t` to extract the email file.

2. **Finding the Attachment Password:**
   - Examined the .eml file in a text editor and found the password for the attachment.

3. **Unzipping the Attachment:**
   - Located the `.docx.zip` attachment and unzipped it using the found password, which was part of the obfuscation technique.

4. **Identifying Suspicious File:**
   - Found a `.docx.exe` file within the unzipped attachment.
   - Used `strings` for further analysis of the file.

5. **VirusTotal Scan:**
   - Ran a VirusTotal scan on the file's hash.
   - The scan indicated a potential tool used: `trojan.meterpreter/rozena`.
   - VirusTotal link: [VirusTotal Report](https://www.virustotal.com/gui/file/eac27ea606e7f61b9c1a0467212b1adcfe01041dc8885a5d3220509b9b812824).

6. **Research on SFX Technique:**
   - Googled the SFX technique and found an informative article: [How SFX Archive Malware Works](https://medium.com/@drthkol478/how-sfx-archive-malware-works-ecb95d280841).
   - The article mentioned the software used to create the SFX attachment, which was entered as the flag.

7. **SFX Script Commands:**
   - The comment below contains SFX script commands:
     ```
     Setup=nc.exe
     Setup=link.url
     TempMode
     Silent=1
     Overwrite=1
     Title=Word Document
     Text
     Word Document
     nc.exe
     ```

##### Solution
The tool used to create the SFX attachment was identified based on the clues from the VirusTotal report and research.

### Challenge Name: Internet Message ID

#### Category: Forensics

#### Description
Determine the Internet Message ID of the phishing email.

#### Steps to Solve

##### Tools Used
- Various text editors

##### Detailed Steps
- Opened the .eml file in different text editors to locate the Internet Message ID.

#### Solution
The Internet Message ID of the phishing email was found by examining the .eml file.

### Challenge Name: Phishing Attachment Hash

#### Category: Forensics

#### Description
Find the SHA256 hash of the phishing attachment.

#### Steps to Solve

##### Tools Used
- sha256sum

##### Detailed Steps
- Ran `sha256sum` on the attachment file to compute its hash.

#### Solution
The SHA256 hash of the phishing attachment was calculated using the `sha256sum` command.

### Challenge Name: Phishing Attachment Content

#### Category: Forensics

#### Description
Identify the file extension used in the SFX to redirect to a webpage.

#### Steps to Solve

##### Tools Used
- strings
- hexdump

##### Detailed Steps
- Analyzed the file using `strings` and `hexdump` to find the file extension.

#### Solution
The file extension used in the SFX to redirect to a webpage was `.URL`.

### Challenge Name: Phishing Attachment URL

#### Category: Forensics

#### Description
Identify the URL that the phishing attachment opened.

#### Steps to Solve

##### Tools Used
- Ghidra
- Virtual machine
- vol3.py

##### Detailed Steps

1. **Initial Analysis with Ghidra:**
   - Analyzed `nc.exe` with Ghidra for any hardcoded IPs or URLs.

2. **Virtual Machine Execution:**
   - Attempted to detonate `nc.exe` in a virtual machine, but encountered performance issues with VirtualBox.

3. **Memory Analysis:**
   - Used `vol3.py` to find the PID of the running `nc.exe` from a memory dump.
   - Command:
     ```bash
     python3 vol.py -f memdump.mem windows.pslist
     ```

4. **Process Tree Examination:**
   - Extracted process information and identified a suspicious `fun.exe`.

#### Solution
The URL used by the phishing attachment was extracted from the analysis and memory dump findings.

## Techniques

### Reverse Engineering
Reverse Engineering involves analyzing code to understand its functionality and uncover hidden features. It is used to bypass software protections or analyze malware behavior.
