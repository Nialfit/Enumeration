**Enumeration (Walkthrough)**

To start my lab, I accessed the Metasploitable 2 terminal and executed the "ifconfig" command. This allowed me to identify the target machine's network configuration specifically its IPv4 address, which is 192.168.0.223. I also noted the IPv6 link-local address as fe80::a00:27ff:fe94:ba85, ensuring I had the correct target details for both network protocols before proceeding with further enumeration.

<img width="732" height="408" alt="1ifconfig" src="https://github.com/user-attachments/assets/185fe3db-f373-44c0-b463-9873b74ace27" />

**Challange 1 - NetBIOS Enumeration**

I used the command "nbtstat -a 192.168.0.223" in the Windows command prompt to perform NetBIOS enumeration. This command retrieved the NetBIOS Remote Machine Name Table for the target confirming the hostname as METASPLOITABLE and the workgroup as WORKGROUP.

<img width="500" height="450" alt="Challenge 1 - nbtstat" src="https://github.com/user-attachments/assets/5aa6b01f-5000-4088-8cc5-998b4231813f" />

**Challenge 2 - Fast Nmap Scan**

I executed the command "nmap -F 192.168.0.223" to perform a fast scan of the target's most common ports. The scan revealed numerous open services including FTP (21), SSH (22), Telnet (23) and HTTP (80).
 
<img width="845" height="627" alt="image" src="https://github.com/user-attachments/assets/d4156341-2fb0-4586-9ed9-a037b9dde309" />


**Challenge 3 - DNS Records**

I used three different commands to perform a comprehensive DNS enumeration of the domain roblox.com. First, I used "nslookup" to perform a basic query, which returned the primary IP address for the domain. Second, I used the "dig ANY" command to retrieve all available DNS records in a single query, providing a broader view of the domain's configuration. Finally, I executed the "dig MX" command specifically to identify the Mail Exchange records, which revealed the mail servers responsible for receiving email on behalf of the domain.

<img width="708" height="473" alt="image" src="https://github.com/user-attachments/assets/1ec38f04-b7a8-4cba-8356-ced20e1a6919" />
<img width="777" height="837" alt="image" src="https://github.com/user-attachments/assets/b03fd259-a7c5-4111-9629-da62288e6bcd" />
<img width="777" height="837" alt="image" src="https://github.com/user-attachments/assets/f0e6e56b-0121-49e8-acb8-a6b530115be0" />

**Challenge 5 - TTL OS Fingerprinting**

I performed basic network connectivity testing and initial OS fingerprinting by using the "ping 192.168.0.223" command from my Kali Linux terminal. The successful ICMP replies confirmed the target was reachable and I analyzed the Time to Live (TTL) value of 64. This specific TTL value is a characteristic indicator that the target machine is running a Linux/Unix operating system

<img width="597" height="628" alt="Challenge 5 - OS Fingerprinting" src="https://github.com/user-attachments/assets/900efd30-a22e-4d02-9c24-13b4212fa895" />

**Challenge 9 - FTP Banner**

I used the Netcat command "nc 192.168.0.223 21" to connect to the target's FTP port. Upon connection, the server immediately returned a service banner identifying itself as vsFTPd 2.3.4.



**Challenge 10 - Anonymous FTP Login**

I attempted an anonymous login to the target at "192.168.0.223" by using the "ftp" command. I successfully logged in using the username anonymous with no password, which confirmed that the service allows unauthenticated access. Once connected, I executed the "ls -al" command to list the directory contents, verifying that I could interact with the remote file system. This discovery highlights a significant security misconfiguration as it allows any user on the network to potentially view or download files from the server.

<img width="815" height="458" alt="image" src="https://github.com/user-attachments/assets/0420bc5a-077b-4e34-bbc5-f02f4bd20add" />


**Challenge 11 - SMB NSE Enumeration**

I first ran the "nmap --script smb-os-discovery -p445 192.168.0.223" command to identify the operating system, which confirmed the target is running Samba 3.0.20 on a Debian system. I then used the "nmap --script smb-enum-users -p445 192.168.0.223" command to extract a complete list of local user accounts, such as root, postfix, and postgres, which provided the account names and their associated RIDs.

<img width="742" height="875" alt="image" src="https://github.com/user-attachments/assets/eceeeb8b-58f3-4842-84d2-a0b670f8a51d" />
<img width="742" height="875" alt="image" src="https://github.com/user-attachments/assets/19ad4717-cb12-4759-a19d-461521256834" />
<img width="551" height="833" alt="Challenge 11 - SMB NSE Enumeration-Users2" src="https://github.com/user-attachments/assets/6b9f5b9d-1645-4f54-8c5a-6c419a9ac04a" />

**Challenge 16 - Version Detection**

I used the command "nmap -sV 192.168.0.223" to perform service version detection on all open ports. This allowed me to identify the exact software and version numbers for various services such as vsftpd 2.3.4 on port 21, Apache httpd 2.2.8 on port 80 and Samba smbd 3.X.

<img width="1133" height="700" alt="image" src="https://github.com/user-attachments/assets/e0f23d4c-dc3a-4a6b-a774-925fd2d68f7b" />



**Challenge 19 - RPC Info**

I used the command "rpcinfo -p 192.168.0.223" to enumerate the Remote Procedure Call (RPC) services on the target. This command provided a list of all RPC programs registered with the portmapper including nfs, mountd and portmapper itself along with their associated port numbers and protocols.

<img width="467" height="545" alt="image" src="https://github.com/user-attachments/assets/065c95aa-4fb1-456b-a136-12159904ecba" />


**Challenge 27 - IPv6 Discovery**

I used the command "nmap -6 -O fe80::a00:27ff:fe94:ba85" to perform a scan and OS detection over the IPv6 protocol. This command confirmed that the target is active on the IPv6 network and identified its operating system as Linux 2.6.X.
<img width="906" height="452" alt="image" src="https://github.com/user-attachments/assets/a5217455-1893-47e2-97e6-d2d055501010" />



**Challenge 29 - SMTP Enumeration via Nmap**

I executed "nmap -p25 --script=smtp-enum-users 192.168.0.223" to attempt to list valid users on the server though the command returned an unhandled status code for the RCPT method. Next, I ran "nmap -p25 --script=smtp-open-relay 192.168.0.223" to check if the server could be used to send unauthorized emails. The results confirmed that the server is not an open relay, indicating that this specific mail vulnerability is not present on the target.


<img width="845" height="265" alt="image" src="https://github.com/user-attachments/assets/d6746aab-d6e9-47cc-928d-7f6461701844" />
<img width="845" height="265" alt="image" src="https://github.com/user-attachments/assets/2cbf3a68-1747-4cb0-bdaf-ceeed76619de" />

