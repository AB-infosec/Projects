---

# Linux Hardening Documentation

## Defense in Depth

**Key Principle:** "Boot access = root access"

Physical access to a machine often equates to full control, making it essential to implement multiple layers of defense, especially for servers and sensitive devices.

### Physical Security Measures

**GRUB Password Protection:**
Setting a GRUB password can prevent unauthorized users from modifying boot parameters and gaining root access by resetting passwords. This is especially useful on non-cloud physical machines.

To set up a GRUB password hash:

```bash
root@AttackBox# grub2-mkpasswd-pbkdf2
Enter password:  
Reenter password:  
PBKDF2 hash of your password is grub.pbkdf2.sha512.10000.534B77859C13DCF094E90B926E26C586F5DC9D00687853487C4BB1500D57EC29E2D6D07A586262E093DCBDFF4B3552742A25700BAB6B76A8206B3BFCB273EEB4.4BA1447590EA8451CD224AA1C5F8623FE85D23F6D34E2026E3F08C5AA79282DB65B330BAB4944E9374EC51BF11EFF418EDA5D66FF4D7AAA86F662F793B92DA61
```

This method is recommended for physical servers but may not be practical for cloud-based instances where console access is controlled differently.

### Disk Encryption

**LUKS (Linux Unified Key Setup):**
LUKS provides full disk encryption, ensuring that even if a disk is physically accessed, its contents remain secure without the encryption key.

**Disk Layout with LUKS:**
- **LUKS Header:** Contains metadata and key material.
- **Key Slots (KM1-KMn):** Master key storage, enabling multiple users to decrypt the disk.
- **Encrypted Data:** The actual data protected by encryption.

[Learn more about LUKS and full disk encryption](https://www.cyberciti.biz/security/howto-linux-hard-disk-encryption-with-luks-cryptsetup-command/).

### Firewalls and Access Control

**SELinux and AppArmor:**
- SELinux and AppArmor are Mandatory Access Control (MAC) systems that enforce security policies and provide an additional layer of defense by restricting processes' access to the system.
  - [SELinux Project](https://github.com/SELinuxProject)
  - [AppArmor](https://www.apparmor.net/)

**Netfilter with `iptables` and `nftables`:**
- `iptables` and `nftables` are frontends for configuring Netfilter, the packet filtering framework in the Linux kernel.
- `nftables` is the newer, more flexible successor to `iptables`.

Example `nftables` configuration:
```bash
nft add chain fwfilter fwinput { type filter hook input priority 0 \; }
nft add chain fwfilter fwoutput { type filter hook output priority 0 \; }
```

**UFW (Uncomplicated Firewall):**
- UFW simplifies firewall management and is particularly useful for quickly setting up and managing firewall rules.

### SSH Hardening

1. **Disable Root Login:**
   Prevent direct root login to minimize the attack surface. This forces users to authenticate with a non-root account before elevating privileges.

   In `/etc/ssh/sshd_config`:
   ```bash
   PermitRootLogin no
   ```

2. **Enforce Public Key Authentication:**
   Disable password-based authentication and require SSH key pairs for stronger, cryptographic login mechanisms.

   In `/etc/ssh/sshd_config`:
   ```bash
   PasswordAuthentication no
   ```

### User Account Management

**Sudo Privilege Elevation:**
- Instead of frequently using `sudo`, add users to the sudo group to grant them the necessary privileges.

```bash
usermod -aG sudo username
```

**Disable Root Login Shell:**
- Modify the root account to disable its login shell, preventing direct logins while still allowing necessary administrative functions.

```bash
# Change root shell to nologin
sudo usermod -s /sbin/nologin root
```

**Password Complexity Enforcement with `libpwquality`:**
- The `libpwquality` library adds complexity requirements for passwords, reducing the risk of weak passwords.

To configure:
```bash
sudo cat /etc/security/pwquality.conf
```

**Account Locking:**
- To disable an account while keeping it intact (e.g., for auditing or archiving), modify the user's shell to `nologin`.

Example:
```bash
michael:x:1000:1000:Michael:/home/michael:/sbin/nologin
```

---
