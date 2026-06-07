# Enumeration Walkthrough

## Initial Enumeration

To begin the lab, I accessed the Metasploitable 2 terminal and executed the `ifconfig` command to obtain the target machine's network information. This allowed me to identify the IPv4 address as **192.168.0.223** and the IPv6 link-local address as **fe80::a00:27ff:fe94:ba85**. These addresses were recorded for use in subsequent enumeration activities.

![ifconfig](https://github.com/user-attachments/assets/185fe3db-f373-44c0-b463-9873b74ace27)

---

## Challenge 1 – NetBIOS Enumeration

To enumerate NetBIOS information, I executed the command `nbtstat -a 192.168.0.223` from a Windows command prompt. The output revealed the NetBIOS Name Table of the remote host, confirming the hostname as **METASPLOITABLE** and the associated workgroup as **WORKGROUP**. This information helps identify systems and resources within the network environment.

![Challenge 1 - NetBIOS Enumeration](https://github.com/user-attachments/assets/5aa6b01f-5000-4088-8cc5-998b4231813f)

---

## Challenge 2 – Fast Nmap Scan

I performed a fast port scan using the command `nmap -F 192.168.0.223`. This scan focused on the most commonly used ports and quickly identified several open services on the target. Notable services included FTP on port 21, SSH on port 22, Telnet on port 23, and HTTP on port 80.

![Challenge 2 - Fast Nmap Scan](https://github.com/user-attachments/assets/d4156341-2fb0-4586-9ed9-a037b9dde309)

---

## Challenge 3 – DNS Records Enumeration

To gather DNS-related information for the domain `roblox.com`, I performed several DNS queries using different tools and record types.

First, I used `nslookup` to obtain the primary IP address associated with the domain. Next, I executed `dig ANY roblox.com` to retrieve a broader set of DNS records, providing additional insight into the domain configuration. Finally, I used `dig MX roblox.com` to identify the mail exchange servers responsible for handling email traffic for the domain.

These queries provided a comprehensive overview of the domain's DNS infrastructure.

![DNS Enumeration 1](https://github.com/user-attachments/assets/1ec38f04-b7a8-4cba-8356-ced20e1a6919)

![DNS Enumeration 2](https://github.com/user-attachments/assets/b03fd259-a7c5-4111-9629-da62288e6bcd)

![DNS Enumeration 3](https://github.com/user-attachments/assets/f0e6e56b-0121-49e8-acb8-a6b530115be0)

---

## Challenge 5 – TTL OS Fingerprinting

I conducted a basic connectivity test and operating system fingerprinting using the command `ping 192.168.0.223` from Kali Linux. The target responded successfully to ICMP echo requests, confirming network reachability.

The observed TTL value was **64**, which is commonly associated with Linux and Unix-based operating systems. Based on this characteristic, I concluded that the target machine was likely running a Linux distribution.

![Challenge 5 - TTL OS Fingerprinting](https://github.com/user-attachments/assets/900efd30-a22e-4d02-9c24-13b4212fa895)

---

## Challenge 9 – FTP Banner Grabbing

To identify the FTP service running on the target, I connected to port 21 using Netcat with the command `nc 192.168.0.223 21`. Upon establishing the connection, the server displayed its service banner, revealing that the FTP service was running **vsFTPd version 2.3.4**.

Banner grabbing provides valuable information about service versions that can later be cross-referenced against known vulnerabilities.

---

## Challenge 10 – Anonymous FTP Login

I attempted to access the FTP service anonymously using the `ftp` client. By entering the username **anonymous** and leaving the password field blank, I successfully authenticated to the server.

After gaining access, I executed the `ls -al` command to view the contents of the remote directory. The successful login confirmed that anonymous FTP access was enabled, which represents a security weakness because unauthorized users may be able to browse, download, or potentially upload files without authentication.

![Challenge 10 - Anonymous FTP Login](https://github.com/user-attachments/assets/0420bc5a-077b-4e34-bbc5-f02f4bd20add)

---

## Challenge 11 – SMB NSE Enumeration

To gather information about the SMB service, I used Nmap's NSE scripts.

The first command executed was:

```bash
nmap --script smb-os-discovery -p445 192.168.0.223
```

This scan identified the target as running **Samba 3.0.20 on Debian Linux**.

Next, I executed:

```bash
nmap --script smb-enum-users -p445 192.168.0.223
```

This script enumerated user accounts available on the system, including accounts such as **root**, **postfix**, and **postgres**, along with their corresponding Relative Identifiers (RIDs).

The information gathered can assist in identifying potential targets for authentication attacks and privilege escalation attempts.

![SMB Enumeration 1](https://github.com/user-attachments/assets/eceeeb8b-58f3-4842-84d2-a0b670f8a51d)

![SMB Enumeration 2](https://github.com/user-attachments/assets/19ad4717-cb12-4759-a19d-461521256834)

![SMB Enumeration 3](https://github.com/user-attachments/assets/6b9f5b9d-1645-4f54-8c5a-6c419a9ac04a)

---

## Challenge 16 – Service Version Detection

To identify detailed service information, I ran the following command:

```bash
nmap -sV 192.168.0.223
```

The scan successfully detected the versions of multiple services running on the target system. Key findings included:

- vsFTPd 2.3.4 on Port 21
- Apache HTTP Server 2.2.8 on Port 80
- Samba smbd 3.x on SMB-related ports

Version detection is an important step because it enables security analysts to map discovered services against known vulnerabilities and publicly available exploits.

![Challenge 16 - Version Detection](https://github.com/user-attachments/assets/e0f23d4c-dc3a-4a6b-a774-925fd2d68f7b)

---

## Challenge 19 – RPC Enumeration

I used the following command to enumerate Remote Procedure Call (RPC) services:

```bash
rpcinfo -p 192.168.0.223
```

The output displayed a list of RPC services registered with the portmapper service, including components such as **nfs**, **mountd**, and **portmapper** itself. The scan also revealed the corresponding protocol types and port numbers associated with each service.

This information can help identify exposed network services and potential attack vectors.

![Challenge 19 - RPC Enumeration](https://github.com/user-attachments/assets/065c95aa-4fb1-456b-a136-12159904ecba)

---

## Challenge 27 – IPv6 Discovery

To verify IPv6 connectivity and perform operating system detection, I executed the following command:

```bash
nmap -6 -O fe80::a00:27ff:fe94:ba85
```

The scan confirmed that the target was accessible through IPv6 and identified the operating system as **Linux Kernel 2.6.x**. This demonstrates that the host is configured to support both IPv4 and IPv6 communication.

![Challenge 27 - IPv6 Discovery](https://github.com/user-attachments/assets/a5217455-1893-47e2-97e6-d2d055501010)

---

## Challenge 29 – SMTP Enumeration via Nmap

To assess the SMTP service, I executed the following command:

```bash
nmap -p25 --script=smtp-enum-users 192.168.0.223
```

The script attempted to enumerate valid user accounts through the SMTP service; however, the server returned an unexpected response code, preventing successful enumeration.

Next, I tested whether the mail server was configured as an open relay using:

```bash
nmap -p25 --script=smtp-open-relay 192.168.0.223
```

The results indicated that the server was **not configured as an open relay**, meaning it could not be abused to send unauthorized email messages on behalf of external users.

These findings suggest that although SMTP services were accessible, the specific open relay vulnerability was not present.

![SMTP Enumeration 1](https://github.com/user-attachments/assets/d6746aab-d6e9-47cc-928d-7f6461701844)

![SMTP Enumeration 2](https://github.com/user-attachments/assets/2cbf3a68-1747-4cb0-bdaf-ceeed76619de)

---

## Conclusion

Throughout this enumeration exercise, multiple reconnaissance techniques were used to gather information about the Metasploitable 2 target. Services such as FTP, SSH, HTTP, SMB, RPC, SMTP, and DNS were successfully identified and analyzed. Additional information, including operating system details, service versions, user accounts, and network configurations, was also obtained.

The collected information provides a solid foundation for vulnerability assessment and further penetration testing activities by highlighting exposed services, potential misconfigurations, and known vulnerable software versions present on the target system.
