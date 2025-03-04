# Linux Privilege Escalation - A Practical Exploration

## Introduction
In this hands-on exercise, we explore different privilege escalation techniques on a Linux system. Initially, we attempt to exploit **SUID binaries**, but after failing to find a viable vulnerability, we pivot to exploiting **sudo misconfigurations** to gain root access.

## Environment
- **Operating System:** Kali Linux
- **Kernel Version:** 6.8.11-amd64
- **User:** `kali`
- **Privilege Escalation Methods:** SUID binaries, sudo misconfiguration

## Step 1: Searching for SUID Binaries
The first approach in privilege escalation is to identify **SUID binaries**, which execute with elevated privileges.

```bash
find / -perm -4000 2>/dev/null
```

📸 **SUID Binaries Output**  
![SUID Output](https://raw.githubusercontent.com/radu2208/linux-privilege-escalation/main/suid-binaries-output.png)

### Output Analysis
The command lists several SUID binaries, including `/usr/bin/pkexec`. `pkexec` is part of **Polkit**, a system service often associated with privilege escalation vulnerabilities.

## Step 2: Checking `pkexec` for Known Vulnerabilities
To see if our version of `pkexec` is vulnerable, we check its version:

```bash
pkexec --version
```

📸 **pkexec Version Output**  
![pkexec Version](https://raw.githubusercontent.com/radu2208/linux-privilege-escalation/main/pkexec-version.png)

### Output:
```
pkexec version 125
```

Next, we search for exploits:

```bash
searchsploit pkexec
```

📸 **pkexec Exploit Search Output**  
![pkexec Exploit Search](https://raw.githubusercontent.com/radu2208/linux-privilege-escalation/main/pkexec-searchsploit.png)

### Analysis
The available exploits target older versions. Since our installed `pkexec` is version 125, no working exploit is found. This means we need to **pivot** and look for alternative privilege escalation paths.

## Step 3: Checking `sudo` Permissions
Since `pkexec` was not exploitable, we inspect our **sudo** privileges:

```bash
sudo -l
```

📸 **Sudo -l Output**  
![Sudo -l Output](https://raw.githubusercontent.com/radu2208/linux-privilege-escalation/main/sudo-l-output.png)

### Output:
```
User kali may run the following commands on kali:
    (ALL : ALL) ALL
```

This output confirms that our user can run **any command** as `root`.

## Step 4: Exploiting `sudo` Misconfiguration
With unrestricted sudo access, escalating privileges is straightforward:

```bash
sudo su
```

Then, verify our new privileges:

```bash
whoami
```

📸 **Root Access via Sudo**  
![Root Access](https://raw.githubusercontent.com/radu2208/linux-privilege-escalation/main/sudo-root-success.png)

### Expected Output:
```
root
```

## Step 5: Confirming Full Root Access
We further validate our privileges:

```bash
id
```

📸 **Root Privileges Confirmation**  
![Root Privileges](https://raw.githubusercontent.com/radu2208/linux-privilege-escalation/main/id-root-confirmation.png)

### Expected Output:
```
uid=0(root) gid=0(root) groups=0(root)
```

## Conclusion
This practical walkthrough highlights how attackers systematically explore privilege escalation vectors:
1. **Initial attempt:** Checking SUID binaries (`pkexec`) for exploits.
2. **Pivoting:** Since `pkexec` wasn’t exploitable, we explored `sudo -l`.
3. **Exploitation:** A misconfigured `sudo` allowed full root access.

### Key Takeaways
🔹 **Limit sudo privileges** – Avoid `ALL` permissions for non-admin users.  
🔹 **Monitor SUID binaries** – Regularly audit executable permissions.  
🔹 **Patch vulnerabilities** – Keep software up to date.

