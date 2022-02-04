[Back to home](https://gl0wyy.github.io/HackTheBox/)

<p align="center">
  Write-up by
  <a href="https://app.hackthebox.com/profile/216556">gl0wy</a>
  <br><br>
  <img src="https://user-images.githubusercontent.com/98056797/152444836-64f25e88-70b9-488d-9890-daeb0cef110f.svg">
</p>

### Nmap Scan

-p- Scans all ports <br>
-sC Script Scan using default NSE scripts <br>
-sV Attempts to determine the version of the service running on the port(s) <br>
-T4 T0-T5 are scan speeds, generally more detectable the higher you go <br>
--min-rate The minimum packets send per second
<br>
```markdown
┌──(gl0wy㉿kali)-[~]
└─$ nmap -p- -sV -sC -T4 --min-rate 1000 10.10.10.239
Starting Nmap 7.92 ( https://nmap.org ) at 2022-02-03 15:43 EST
Warning: 10.10.10.239 giving up on port because retransmission cap hit (6).
Nmap scan report for 10.10.10.239
Host is up (0.025s latency).
Not shown: 65399 closed tcp ports (conn-refused), 117 filtered tcp ports (no-response)
PORT      STATE SERVICE      VERSION
80/tcp    open  http         Apache httpd 2.4.46 ((Win64) OpenSSL/1.1.1j PHP/7.3.27)
|_http-server-header: Apache/2.4.46 (Win64) OpenSSL/1.1.1j PHP/7.3.27
| http-cookie-flags: 
|   /: 
|     PHPSESSID: 
|_      httponly flag not set
|_http-title: Voting System using PHP
135/tcp   open  msrpc        Microsoft Windows RPC
139/tcp   open  netbios-ssn  Microsoft Windows netbios-ssn
443/tcp   open  ssl/http     Apache httpd 2.4.46 (OpenSSL/1.1.1j PHP/7.3.27)
| ssl-cert: Subject: commonName=staging.love.htb/organizationName=ValentineCorp/stateOrProvinceName=m/countryName=in
| Not valid before: 2021-01-18T14:00:16
|_Not valid after:  2022-01-18T14:00:16
| tls-alpn: 
|_  http/1.1
|_ssl-date: TLS randomness does not represent time
|_http-server-header: Apache/2.4.46 (Win64) OpenSSL/1.1.1j PHP/7.3.27
|_http-title: 403 Forbidden
445/tcp   open  microsoft-ds Windows 10 Pro 19042 microsoft-ds (workgroup: WORKGROUP)
3306/tcp  open  mysql?
| fingerprint-strings: 
|   DNSVersionBindReqTCP, NULL, RPCCheck, WMSRequest, X11Probe: 
|_    Host '10.10.14.25' is not allowed to connect to this MariaDB server
5000/tcp  open  http         Apache httpd 2.4.46 (OpenSSL/1.1.1j PHP/7.3.27)
|_http-title: 403 Forbidden
|_http-server-header: Apache/2.4.46 (Win64) OpenSSL/1.1.1j PHP/7.3.27
5040/tcp  open  unknown
5985/tcp  open  http         Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
5986/tcp  open  ssl/http     Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
| ssl-cert: Subject: commonName=LOVE
| Subject Alternative Name: DNS:LOVE, DNS:Love
| Not valid before: 2021-04-11T14:39:19
|_Not valid after:  2024-04-10T14:39:19
| tls-alpn: 
|_  http/1.1
|_ssl-date: 2022-02-03T21:09:25+00:00; +21m34s from scanner time.
|_http-title: Not Found
7680/tcp  open  pando-pub?
47001/tcp open  http         Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
49664/tcp open  msrpc        Microsoft Windows RPC
49665/tcp open  msrpc        Microsoft Windows RPC
49666/tcp open  msrpc        Microsoft Windows RPC
49667/tcp open  msrpc        Microsoft Windows RPC
49668/tcp open  msrpc        Microsoft Windows RPC
49669/tcp open  msrpc        Microsoft Windows RPC
49670/tcp open  msrpc        Microsoft Windows RPC
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
Service Info: Hosts: www.example.com, LOVE, www.love.htb; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb-os-discovery: 
|   OS: Windows 10 Pro 19042 (Windows 10 Pro 6.3)
|   OS CPE: cpe:/o:microsoft:windows_10::-
|   Computer name: Love
|   NetBIOS computer name: LOVE\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2022-02-03T13:09:10-08:00
|_clock-skew: mean: 2h21m34s, deviation: 4h00m00s, median: 21m33s
| smb2-security-mode: 
|   3.1.1: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2022-02-03T21:09:14
|_  start_date: N/A
| smb-security-mode: 
|   account_used: <blank>
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 246.24 seconds
```
### Findings
```markdown
Domain www.love.htb
Subdomain staging.love.htb
OS Windows 10 Pro 19042
SMB Ports 139,445
SSL on 443,5986
HTTP on 80,5000,5040,5985,47001
```
### I check www.love.htb - we have a login page that requires a voter ID and password
![1a](https://user-images.githubusercontent.com/98056797/152440542-776aa497-b3f1-4eaa-9d6e-637bf92c0e44.PNG)

### Further enumeration - I find an admin directory and attempted to login using common credentials with no success
![2b](https://user-images.githubusercontent.com/98056797/152441068-00236554-a2a8-4dda-a49a-60a181b5980b.PNG)

### While trying to login to the admin page I launched another GoBuster scan on the admin directory - I still couldn't access these pages but not due to credentials specifically as I was being redirected back to the login page
![2a](https://user-images.githubusercontent.com/98056797/152441237-9bbefa8d-9412-416a-84ed-07ac9fd04392.PNG)

### I tried some of the other HTTP ports but they were forbidden
![7](https://user-images.githubusercontent.com/98056797/152441421-41b03c32-bef8-43a2-8698-b38ed1872738.PNG)

### I then tried the staging subdirectory http://staging.love.htb and found a file scanning tool
![14](https://user-images.githubusercontent.com/98056797/152441618-549f9048-6216-4b69-a994-135f3edd50f5.PNG)

### I scanned the local machine 127.0.0.1 and saw the vote login page again - I then scanned the /admin/home.php
![2](https://user-images.githubusercontent.com/98056797/152441774-d9479190-07fa-4193-98ba-cbd290a501ea.PNG)

### I could now see the content of the admin dashboard but was unable to do anything else. I decided to scan some of the other HTTP ports and found admin credentials
![1](https://user-images.githubusercontent.com/98056797/152441887-cd738da5-5f8b-4cf3-974a-7ff7cac88681.PNG)

### I logged into the admin page with the credentials found
![3](https://user-images.githubusercontent.com/98056797/152441933-a5307db5-684e-402e-adeb-a3c7a90d8733.PNG)

### I immediately clicked to create a new voter and it allowed me to upload a .php reverse shell as a profile picture(remember that it has to use windows processes!) - the shell saved at http://love.htb/images/winphp.php and was accessible
![5](https://user-images.githubusercontent.com/98056797/152442039-bc4593a5-08ca-40a7-9065-703ed8b23153.PNG)

### I now had shell as user: Phoebe
![6](https://user-images.githubusercontent.com/98056797/152442061-48effe66-9d68-4e81-8f14-4a5721513889.PNG)

### Further enumeration - I started a python http server on my local machine and curled winPEAS.exe (wget was not available)
![8](https://user-images.githubusercontent.com/98056797/152442281-b33039ec-1f8a-4a93-b071-374453f1270a.PNG)

![9](https://user-images.githubusercontent.com/98056797/152442289-d66a6466-7b39-4198-b043-3bb55fb48cf1.PNG)

### Scrolling through the winPEAS output this stood out to me - this means all executables will be run as system/administrator
![10](https://user-images.githubusercontent.com/98056797/152442329-2a56fb20-7fc8-48df-8443-283ad31577b7.PNG)

### Creating a payload
![11](https://user-images.githubusercontent.com/98056797/152442412-a1ace076-777e-41ee-b043-37844871f88a.PNG)

### Downloading the payload to love
![12](https://user-images.githubusercontent.com/98056797/152442432-11fccdca-1748-4e79-8820-8f05daac37f8.PNG)

### Shell as administrator - root flag!
![13](https://user-images.githubusercontent.com/98056797/152442467-ca05b71e-f176-4a82-a2be-a936b18dbd24.PNG)


