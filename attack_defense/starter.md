Attack Defense Tips & Tricks

ssh root@ip vostru

ss -tulpn

cautati procese interesante

cat /proc/PID_PROCES_INTERESANT/cmdline

cat /proc/PID_PROCES_INTERESANT/environ

cd /proc/PID_PROCES_INTERESANT/cwd

Procese interesante:
- socat
- xinetd
- gotty

Procese mediu interesante:
- python3
- node
- perl
- php

Procese cel mai putin interesante:
- apache/nginx
- socat/xinetd/gotty - 99% conectare cu netcat
- cautam readme, readme.md. readme.txt -> aplicatia mare, cautam versiunea -> cautam CVE 
- LFI/RFI, Remote Code Execution
- default credentials : admin/admin
- /var/www/

Pentru privesc:
sudo -l
find / -perm /4000 2>/dev/null

Locatie pentru flaguri:
/opt/flag.txt
/root/flag.txt

Viteza la reverse shell:
revshells.com
