# SecOps 2024 CTF Writeup: VPN Certificate Extraction and Ransomware Analysis

## PowerShell Cmdlet for Certificate Extraction

### Task: Extracting the VPN Access Certificate

To determine the PowerShell cmdlet used to extract the certificate that later granted the attackers VPN access to the ACME network, I referred to the Microsoft documentation on exporting certificates. The command was modified to extract the certificate from the .vhd file.

#### Steps Taken:

1. **Locate the Certificate:**
   - Used the `Get-ChildItem` cmdlet to locate the certificate:
     ```powershell
     $cert = Get-ChildItem -Path Cert:\CurrentUser\My\EEDEF61D4FF6EDBAAD538BB08CCAADDC3EE28FF
     ```

2. **Export the Certificate:**
   - Exported the certificate to a file using the `Export-Certificate` cmdlet:
     ```powershell
     Export-Certificate -Cert $cert -FilePath C:\Certs\user.cer
     ```

#### Cmdlet Used:
The PowerShell cmdlet used was `Export-Certificate`.

## VPN Certificate Issuer

### Task: Identify the Certificate Issuer

To find the common name of the issuer of the certificate that granted the attackers VPN access to the ACME network, I simply opened the extracted certificate file.

#### Steps Taken:

1. **Open the Certificate File:**
   - Opened the file `user.cer` using a text editor or certificate viewer.

2. **Read Issuer Information:**
   - Located the issuer's common name in the certificate details.

#### Issuer Common Name:
The common name of the issuer was identified directly from the certificate details.

## Crypto Ransom

### Task: Identify the Cryptocurrency for the Ransom

The ransom details, including the cryptocurrency requested, were found by locating a notepad file in the startup folder and visiting the provided TOR address.

#### Steps Taken:

1. **Find the Ransom Note:**
   - Located a notepad file in the startup folder that contained a TOR address.

2. **Visit the TOR Address:**
   - Navigated to the TOR address to find details about the ransom demand.

#### Cryptocurrency:
The ransom amount was requested in XMR

## Attacker Wallet Address

### Task: Identify the Attacker's Wallet Address

The wallet address provided by the attackers was displayed on the ransom site accessed via the TOR address.

#### Steps Taken:

1. **Access the Ransom Site:**
   - Visited the TOR address provided in the ransom note.

2. **Retrieve Wallet Address:**
   - Located the wallet address information on the site.


## Hacker Group

### Task: Identify the Hacker Group

The name of the hacker group that compromised ACME was revealed in the ransom note and confirmed by visiting the pwned site.

#### Steps Taken:

1. **Examine the Ransom Note:**
   - Reviewed the ransom note found in the startup folder for clues about the hacker group.

2. **Visit the Pwned Site:**
   - Confirmed the hacker group's identity by visiting the site mentioned in the ransom note.
