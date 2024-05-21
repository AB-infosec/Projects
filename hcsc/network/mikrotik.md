used 
sudo env "PATH=$PATH" autorecon 10.10.1.10

found 

[~/results/10.10.1.10/report/report.md/10.10.1.10/Services/Service - tcp-32860-ssh]

ssh admin@10.10.1.10 -p 32860 worked
used default password. (no need to bruteforce)

the magic key for RouterOS is Tab. This helped me to navigate.

found out users. 

admin is read only

adns user is full admin. 

locations of interest: /file

pub
and 
skins

grep -Eo '[Hh][Cc][Ss][Cc]{[^}]*}'