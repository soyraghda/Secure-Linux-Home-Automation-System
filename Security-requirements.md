## Security Requirements

### Password security and policy

**Objectives:**
- The root account must be locked with a password.
- Passwords must adhere to a minimum length of 10 characters.
- Passwords must contain a combination of alpha, numeric, and special characters, where the computing system permits (!@#$%^&*_+=?/~';',<>|\).
- Disallow usernames or user IDs to be used as passwords.
- Prevent the user from using the last 3 passwords.
- Passwords must be stored securely and not in plaintext (Hash passwords with function considered as safe sha512).
- Password should not contain simple/easy to guess or simplistic/systematic words.
- Lock the user account for 5 mins after 3 failed trials of entering the password.

**Tools/Files:**
- PAM

**Justification:**
- Locking the root account with a password is a fundamental security measure aimed at preventing unauthorized access to the most privileged account on a system.
- Passwords serve as a means of authentication, verifying that the user is who they claim to be.
- A strong password helps maintain confidentiality. It prevents unauthorized individuals from accessing sensitive data.
- Strong passwords act as a layer of security and make it harder for attackers to guess or crack them.
- Strong passwords lower the possibility of data breaches and illegal data access by making it harder for unauthorized people to get past authentication and access important data.
- Longer passwords increase security by expanding the search space for attackers attempting to guess or crack them. A minimum length of 10 characters ensures complexity.
- Requiring a combination of alpha (letters), numeric (digits), and special characters significantly increases the complexity of passwords. This complexity makes them more resistant to brute force attacks.
- Locking an account temporarily after multiple failed login attempts helps prevent brute force attacks. It discourages attackers from repeatedly guessing passwords.
- Using predictable usernames or IDs as passwords is risky. Disallowing this practice prevents exploitation.
- Reusing passwords poses a security risk. If an old password is compromised, attackers could access other accounts.
- Storing passwords in plaintext is a major flaw. Storing hashed passwords (using SHA-512) ensures irreversible transformation.
- Common passwords like “password” are easily guessable. Encouraging unique, non-dictionary-based choices.

### Kernel Hardening

**Objectives:**
- Restrict access to the dmesg buffer.
- Hide kernel addresses in /proc.
- Completely shut down the system if the Linux kernel behaves unexpectedly.
- File System Hardening Configurations:
  - Allow to prohibit opening FIFOs and regular files that are not owned by the user in sticky folders for everyone to write.
  - Restrict the creation of hard links to files whose user is owner.

**Tools/Files:**
- sysctl

**Justification:**
- The dmesg command displays kernel ring buffer messages, which can contain sensitive information about system events, hardware, and drivers.
- By default, the /proc filesystem exposes information about running processes, including kernel addresses. Hiding kernel addresses reduces the attack surface and makes it harder for malicious actors to target specific memory locations.
- Ensuring system stability is important; hence, it is good practice to shut down the system if the Linux kernel behaves unexpectedly.
- The sticky bit on directories allows only the owner of a file to delete or rename it within that directory. By default, anyone can create files in a directory with the sticky bit set. Without the sticky bit, any user can create files in a directory. Malicious users could create files with harmful content. By setting the sticky bit, we restrict file creation to the owner, preventing unauthorized files from cluttering sensitive directories. This configuration also makes the spoofing attack harder to implement.
- Limiting hard link creation helps prevent unauthorized modification. If an attacker creates a hard link to a sensitive file (e.g., SSH private key), they can modify it indirectly. By restricting hard link creation to file owners, we prevent unauthorized changes.

### SSH Hardening

**Objectives:**
- Create and manage keys for passwordless logins.
- Configure automatic logouts by determining the time to keep the connection alive using ClientAliveInterval. ClientAliveCountMax is the limit of staying unresponsive before being disconnected.
- Disable Root Login via SSH.
- Change the default SSH port.
- Create a banner for users who are trying to access the system using SSH.
- Disable SSH Protocol 1.

**Tools/Files:**
- sshd_config

**Justification:**
- SSH is a widely used protocol for secure remote access to systems and for secure file transfers. By hardening SSH, additional security measures are implemented to mitigate risks and strengthen the overall security posture of our systems.
- The root user denotes a user having superuser access on a system. If attackers somehow access the root account on the system, it leads to disastrous consequences.
- If authenticating SSH using a username and password, an attacker may try brute force against SSH username and password. Hence, it is more secure to
## Principle of Least Privilege and roles separation

**Objectives:**
- Give to the execution of a program only the rights necessary for its execution.
- Give certain properties and role privileges based on each account role with limited trust in root user, and no privilege elevation mechanism.
- Ensure that no SUID binaries, SUID binaries disabled, and all partitions mounted with the nosuid mount option.
- Ensure that no new privileges flag (no_new_privs) set for the PID 1 process.
- Changing the port number increases the little time of the attacker to identify an open SSH port.

**Tools/Files:**
- SELinux

**Justification:**
- Reducing the possibility of exposing several of the privilege escalation paths, such as insecure services, abusing sudo, and misconfigured access controls.
- Prevent attackers from compromising the entire system, accessing sensitive information, or exploiting vulnerabilities and extending their permissions.

## Audit and log all privilege access usage

**Objectives:**
- Through logging, privilege usage are monitored for abuse and suspicious activity.
- Record relevant data with the records (e.g., Date and time, type, and outcome of an event).
- Association of an event with the identity of the user who triggered the event
- Record all modifications to audit configuration and attempts to access audit log files
- Record any changes to /etc/passwd
- Regularly generate a report of all recorded events.

**Tools/Files:**
- systemd
- journald

**Justification:**
- By auditing suspicious behaviors in the system are identified and they can be prevented from happening.
- In case a privilege escalation exploit occurred, investigation and identification of the root cause of the attack can be done to prevent it from happening again.

## Partitions hardening

**Objectives:**
- Separation, partitioning, isolation of different software components (disks)
- Having backups of partitions.
- Leveraging hardware mechanisms (such as memory protection units, secure enclaves, or separate execution domains) to isolate critical components from each other.

**Tools/Files:**
- Fdisk

**Justification:**
- Having separate partitions can enhance security by isolating data. If malware, such as ransomware, infects a specific partition, it may have a lower chance of locking personal files on another partition.
- A malware can be removed by destroying the affected partition only.
- Having backups of partitions in order to be able to recover data.
- Partitioning different software components to prevent unauthorized interactions
- Ensuring that sensitive information remains confidential and that data integrity is maintained.
- Hardware-based isolation helps prevent unauthorized access and data leakage.

## Network Security

**Objectives:**
- Intrusion Detection System (IDS) alerts for any incoming/outgoing connection over HTTP.
- Filter and classify network packets.
- Ports scanning.
- Secure communication protocols (e.g., TLS/SSL)

**Tools/Files:**
- Snort
- NFtables
- nmap
- IDPS

**Justification:**
- IDS, filtering, and ports scanning help in monitoring network activities, detecting threats and insecure network connections, providing real-time alerts, mitigating vulnerabilities, and reducing the attack surface.
- They accelerate and automate network threat detection by alerting security administrators to known or potential threats or by sending alerts to a centralized security tool.
- Secure communication protocols protect data during transmission.
## Web server security

**Objectives:**
- Enable HTTPS to encrypt data transmitted between clients and the server.
- Obtain an SSL certificate from a trusted certificate authority.
- Changing the default port number.
- The use of firewalls to block unauthorized access
- Apply SSH objectives
- Implement Web application Firewall (WAF)
- Regular security patches and updates to the web server software. Vulnerabilities are often discovered and patched by software vendors.
- Use Intrusion Detection and Prevention Systems (IDPS) tools to monitor network traffic and detect suspicious or malicious activity. Set up alerts for potential security incidents.
- Disable any unused or unnecessary services, modules, or features on the web server.

**Tools/Files:**
- SSH
- SSL
- Firewalls

**Justification:**
- Changing the default port number can reduce the chances of attacks.
- The use of protocols like SSH for secure communication is important as web servers often host sensitive data.
- Unauthorized access can lead to data breaches and availability issues. 
- Insecure web servers can be exploited by cybercriminals to launch attacks, which can lead to service disruptions (compromise availability)
- Regular security patches and updates are done because vulnerabilities are often discovered and patched by software vendors.
- Set up firewalls to filter incoming and outgoing traffic. Configure rules to allow only necessary services and block unauthorized access.
- WAF detects and blocks common web application attacks (e.g., SQL injection, cross-site scripting).
- HTTPS (SSL/TLS) is used for protection against eavesdropping as well as Man-in-the-Middle Attacks
- Disabling unused services on the web server reduces the attack surface and minimizes potential vulnerabilities

## System and sensitive data protection

**Objectives:**
- Encrypting disks or partitions
- Encrypting files and directories
- Implement secure boot mechanisms
- Use strong authentication methods (e.g., hardware tokens, certificates) to verify user identities.
- Control access through authorization (role-based access control, least privilege).
- Limit access to sensitive data based on roles and permissions.
- Monitor and audit access to sensitive data.
- Protect the physical device from tampering or unauthorized access.
- Implement secure communication protocols (e.g., TLS/SSL) for data exchange.

**Tools/Files:**
- LUKS
- Veracrypt
- Fscrypt
- encfs

**Justification:**
- Provide disk encryption, which prevents unauthorized access to data as well as encrypting files that have sensitive data. This achieves the security objectives: Confidentiality, Integrity, and Availability.
- If the system is ever lost or stolen, encryption protects the data on the disk.
- Secure boot ensures that the bootloader and kernel haven’t been tampered with which prevents unauthorized or malicious code from executing during startup as it guards against bootloader-level attacks.
- Strong authentication ensures that only authorized users access sensitive data. Hence, it reduces the risk of accidental or intentional data exposure.
- Having secure communication protocols prevent eavesdropping and data manipulation which ensures that sensitive data transmitted over networks remains confidential and unaltered.
- Physical security prevents unauthorized access or tampering which guards against physical attacks (e.g., removing storage media, altering hardware).
- Secure boot ensures the integrity of critical components and prevents unauthorized modifications to the bootloader.

## Minimization

**Objectives:**
- Start with a minimal set of packages during the initial installation
- Only install packages that are essential for the system’s intended purpose. Avoid group installations that include unnecessary components.
- Understand package dependencies and avoid installing unnecessary dependencies.
- Add only the required software components.

**Tools/Files:**
- apt package manager
- systemd

**Justification:**
- Reduce the attack surface by reducing vulnerabilities and things that can go wrong
- A minimal system has fewer installed packages and services. Each installed component represents a potential entry point for attackers. By minimizing the attack surface, the chances of vulnerabilities being exploited are decreased.
- When only essential packages are installed, exposure to known vulnerabilities associated with unnecessary software is reduced. Frequent security updates are required for all installed packages, so minimizing the number of packages simplifies this process.
- Auditing a minimal system is more straightforward. By focusing on a smaller set of components, it can be ensured that each one adheres to security best practices.

### Sudo hardening

**Objectives:**
- Restrictive Sudoers Configuration: Instead of allowing all commands, create a strict sudoers file that specifies which commands each user or group can run.
- Time-Based Restrictions: Use the timestamp_timeout option in the sudoers file to limit the time a user has after authenticating with sudo
- Limit the environment variables that are preserved when running commands with sudo. In the sudoers file, you can use the env_reset option to restrict environment variables.
- Implement Two-Factor Authentication (2FA) for sudo access.

**Tools/Files:**
- /etc/sudoers

**Justification:**
- Limit the attack surface and enhance traceability.
- By limiting the time a user has after authenticating with sudo, the exposure window is reduced during which an attacker can abuse elevated privileges. For example, if a user accidentally leaves a terminal session open with sudo access, an attacker has a limited time to exploit it.
- Limiting the environment variables preserved when running commands with sudo ensures that only necessary variables are available to the privileged command. It follows the principle of least privilege, reducing the attack surface. Moreover, some environment variables might unintentionally affect the behavior of privileged commands.
- Having 2FA adds an extra layer of security by requiring a second form of authentication (e.g., a time-based one-time password or a hardware token).
