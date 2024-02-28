---
description: >-
  This is a free linux, proving grounds machine, you can also download it from
  vulhub.
---

# SoSimple

#### NMAP SCAN

```bash
nmap -sV -sC -T4 -oN $HOME/Desktop/labs/SoSimple 192.168.199.78
```

with the command above to scan fully the machine , fellow ports where seen

```
Starting Nmap 7.94 ( https://nmap.org ) at 2023-11-16 19:35 EAT
Nmap scan report for 192.168.199.78
Host is up (0.25s latency).
Not shown: 998 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.1 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 5b:55:43:ef:af:d0:3d:0e:63:20:7a:f4:ac:41:6a:45 (RSA)
|   256 53:f5:23:1b:e9:aa:8f:41:e2:18:c6:05:50:07:d8:d4 (ECDSA)
|_  256 55:b7:7b:7e:0b:f5:4d:1b:df:c3:5d:a1:d7:68:a9:6b (ED25519)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-server-header: Apache/2.4.41 (Ubuntu)
|_http-title: So Simple
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 34.27 seconds

```

port 22 and 80 are open, On checking the **auth-methods** we the following on how to access the ssh

```bash
nmap -p 22 --script ssh-auth-methods 192.168.199.78
```

```
Starting Nmap 7.94 ( https://nmap.org ) at 2023-11-16 19:44 EAT
Nmap scan report for 192.168.199.78
Host is up (0.25s latency).

PORT   STATE SERVICE
22/tcp open  ssh
| ssh-auth-methods: 
|   Supported authentication methods: 
|     publickey
|_    password


```

**ssh-hostkey**

```bash
nmap -p 22 --script ssh-hostkey --script-args ssh_hostkey=full 192.168.199.78
```

```
Starting Nmap 7.94 ( https://nmap.org ) at 2023-11-16 19:50 EAT
Nmap scan report for 192.168.199.78
Host is up (0.26s latency).

PORT   STATE SERVICE
22/tcp open  ssh
| ssh-hostkey: 
|   ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDZJ+y+c4YDmXPBY8hytP7uA0bmTKJfUnWpZn1744GxKheNmQqG98tALhL+Hz4OxWRhLzoGa/klypcYMEstzpopxZvLIUil5PPfrCTW+dwCixiULgi8Q9ImfBgKNYaQ6aog7qXG0N0GUazyJj/O2Sfx8qc32cvgh27SOOfiIvZ3s3xeh1DOqjC1kEkzJG9YeMRKRc0AC2TCtRmGbvBGL3iKjjuLS+lXxgtNEnjGI3m+n7RwgMDe0iv82ThCc1oRjeTEysstm4baIJvsdRs/trfvV/2cfAAfl77B0p6HS3rPWZYj2WCoSyG6Z3bK+kjjt+FG5V+zhQ8G4yntT/brCIXGa0iSe1vGrLk6dIrRsmbPsG3V3dkggyOL/aWkL6Q2bnb3suFINJ98Hvjd9Pe3ngsnv5iefgRaHwu/GgP7sVpLsKGdvo2smS7PTmHrZFqP74SeGC+TQ2BIhxYe9uAoL5NRappcCyA0ZF3kB9907nSggM/1bZ2uXnWqzKwPD5dBvTM=
|   ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBO/ko3XtMH5m6keCi750yCg/B93iEWSBbyGrmJZ4sHThaowuRlW6sm/WuHR6AUeoCsU0su07XVlgPtCJOf35ByU=
|_  ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIKkLRPLyIQqo5WToErae3vTYq6M2ZYupOFtsl1oNG0rp

Nmap done: 1 IP address (1 host up) scanned in 8.10 seconds

```

## scanning port 80

Here we see we have a simple website and we have nothing suspicious in the source code so i decided to look for directories and files that might be present on the server

#### feroxbuster

```bash
feroxbuster -u http://192.168.199.78/ -x html,php,txt
```

!\[\[Screenshot\_2023-11-16\_20-46-34.png]]

And with some time manage to see that their a **/wordpress** folder present which indicates that their might be a wordpress website present and its true.

!\[\[1.png]]

When you look closely down you see a post **hello world!** that was created and this was posted by **admin** which means we have a user on the system called **admin**. So lets try to find the password of admin to the wordpress login.

With the use of **Wpscan** we can brute force the password for our user **admin**.

wpscan :

```bash
wpscan --url http://192.168.199.78/wordpress -U admin -P /usr/share/wordlists/rockyou.txt

```

!\[\[2.png]]

ccvbn [google](https://google.com)

```shell
nmap -sV -sC ip
```

## nmap

```shell
wpscan --url http://192.168.239.78/wordpress/ -e ap,u --passwords /usr/share/wordlists/rockyou.txt
```
