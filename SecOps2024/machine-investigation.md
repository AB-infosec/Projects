

What was the powershell cmdlet that was used to extract the certificate that later granted the attackers VPN access to the ACME network?

https://learn.microsoft.com/en-us/powershell/module/pki/export-certificate?view=windowsserver2022-ps

I have modified the example from the MS article to get the cert stored on the .vhd file.
$cert = Get-ChildItem -Path Cert:\CurrentUser\My\EEDEF61D4FF6EDBAAD538BB08CCAADDC3EE28FF

Export-Certificate -Cert $cert -FilePath C:\Certs\user.cer

 VPN Certificate Issuer
40

What was the common name of the issuer of the certificate that granted the attackers VPN access to the ACME network?

Simply opening the cert file revealed the information about the issuer.


 Crypto Ransom
20

What cryptocurrency was the ransom amount requested in?


After finding the notepad file stored in the startup folder, I navigated to the TOR address to find details about the ransom.

 Attacker Wallet Address
20

What was the wallet address provided by the attackers?


Similarly, information about this flag was displayed on the ransom site. 

 Hacker Group
20

What is the name of the hacker group that pwned ACME?


this was revealed in the ransome note and by visiting the pwned site.

