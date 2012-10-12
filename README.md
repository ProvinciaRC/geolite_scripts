geolite_scripts
===============

geolite geoip downloader &amp; check IP scripts from http://ts1-en.blogspot.it/

>Tuesday, June 23, 2009
>SSH access control with GeoIP
>All admins of our systems are in Japan, so it is reasonable to block SSH login attempts from other countries. (Disabling password login is the best to block bruteforce attacks, but in some cases it simply can't be done for some reason... Also, iptables -m recent does good job for that.)
>
>On Debian-based systems it is possible to call external program from /etc/hosts.allow to allow/deny a client. I implemented a country filter based on GeoIP for SSH protection.
>
>Firstly, we need to regularly update GeoIP database. Free database seems to be updated every month. I run this script by cron.daily to check if it is updated and download it if it is. (Database is saved to the directory where the script is.)
>
>/etc/hosts.allow is something like this:
>sshd: ALL: aclexec /usr/local/geoip/check %a
>then in /etc/hosts.deny:
>sshd: ALL
>so that connections not explicitly allowed are denied.
>
>Finally this is the script I call from aclexec. Filtering rules are written in function "rule". In this case clients from Japan and unknown are allowed to connect.
>Both scripts use syslog for diagnostic output.
>
>This setup can be used for other services such as FTP.

on CentOS
```
yum install geoip
yum install python-GeoIP
```
clone git (or download) and put '**geoip_check**' and  '**geoip_update**' somewhere like '/usr/local/share/geolite_scripts/' and make executables

modify in **geoip_update** "IT" with your country code:
```python
...
def rule(code):
    if code == "IT": return ALLOW
    if code == None: return ALLOW
    return DENY
...
```


crontab:
```
@weekly /usr/local/share/geolite_scripts/geoip_update
```
hosts.allow:
```
sshd : ALL : spawn /usr/local/share/geolite_scripts/geoip_check %a
```

hosts.deny
```
sshd : ALL
```

