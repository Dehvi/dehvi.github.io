---
layout: post
title: Vulnhub - Mercury writeup
---

Mercury is a boot2root machine that is about enumerating the web system  to find the SQL vulnerability and exploiting it to get credentials for SSH login. After that exploiting a known vulnerability to privilege escalation.

---

## Finding the ip address and enumeration
```sh
netdiscover -i eth1 -r 192.168.9.0/24
```

![[vulnhub-mercury-netdiscover.png]]
The ip address for the box is 192.168.9.21
### Port scanning

```sh
nmap -sC -sV -oA nmap/mercury 192.168.9.21
```
![[vulnhub-mercury-nmap.png]]

The website on port 8080 is under development, so there isn't much to see on homepage.

![[vulnhub-mercury-website.png]]

When trying to go to a page that does not exist the following error page comes up.
![[vulnhub-mercury-website-error.png]]

There looks to be a interesting folder '/mercuryfacts/'

![[vulnhub-mercury-website-mercuryfacts-page.png]]

![[vulnhub-mercury-website-mercuryfacts-todo.png]]

![[vulnhub-mercury-website-mercuryfacts-facts.png]]
Mercury facts page seems to be retrieving the data from a SQL database.

Using sqlmap to get the users table.
```sh
sqlmap http://192.168.9.21:8080/mercuryfacts/1 --dump
```
![[vulnhub-mercury-sqlmap-db-dump.png]]
## SSH login
Now using the credentials to login with SSH.
![[vulnhub-ssh-login-webmaster.png]]

First flag is in the /home directory of the webmaster in a file "user_flag.txt"
user_flag_8339915c9a454657bd60ee58776f4ccd


## Privilege escalation

Running the [linpeas](https://github.com/carlospolop/PEASS-ng/tree/master/linPEAS) script to find a possible way to gain privilege escalation

The system is vulnerable to CVE-2021-4034.

Github is a great place for finding exploits for CVEs.


![[vulnhub-mercury-priv-exploit-root-flag.png]]



Last flag is found in the /root directory the "root_flag.txt" file. root_flag_69426d9fda579afbffd9c2d47ca31d90
