# SYMFONOSv5.2
Desarrollo del CTF SYMFONOSv5.2

## 1. Configuración de la VM

- Descargar la VM: https://www.vulnhub.com/entry/symfonos-52,415/
- La VM funciona muy bien en VMWARE WORKSTATION.

## 2. Escaneo de Puertos

```
# Nmap 7.91 scan initiated Sun Apr 11 15:17:32 2021 as: nmap -n -P0 -p- -sC -sV -O -T5 -oA full 10.10.10.155
Nmap scan report for 10.10.10.155
Host is up (0.00070s latency).
Not shown: 65531 closed ports
PORT    STATE SERVICE  VERSION
22/tcp  open  ssh      OpenSSH 7.9p1 Debian 10+deb10u1 (protocol 2.0)
| ssh-hostkey: 
|   2048 16:70:13:77:22:f9:68:78:40:0d:21:76:c1:50:54:23 (RSA)
|   256 a8:06:23:d0:93:18:7d:7a:6b:05:77:8d:8b:c9:ec:02 (ECDSA)
|_  256 52:c0:83:18:f4:c7:38:65:5a:ce:97:66:f3:75:68:4c (ED25519)
80/tcp  open  http     Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Site doesn't have a title (text/html).
389/tcp open  ldap     OpenLDAP 2.2.X - 2.3.X
636/tcp open  ldapssl?
MAC Address: 00:0C:29:99:2E:9A (VMware)
Device type: general purpose
Running: Linux 3.X|4.X
OS CPE: cpe:/o:linux:linux_kernel:3 cpe:/o:linux:linux_kernel:4
OS details: Linux 3.2 - 4.9
Network Distance: 1 hop
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```
<img src="https://github.com/El-Palomo/SYMFONOSv5.2/blob/main/symfonos1.jpg" width=80% />





