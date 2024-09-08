### Sysinternals Tooling Kit

The Sysinternals suite is a set of utilities to manage, diagnose, troubleshoot, and monitor a Windows environment. It includes tools for file and disk management, networking, process management, security, and system information.

#### Process Monitor (Procmon)

Process Monitor is a real-time monitoring tool that shows file system, registry, and process/thread activity.

- **Usage Example**:
  ```plaintext
  \\live.sysinternals.com\tools\procmon.exe
  ```
  This command runs Process Monitor from a remote source. Note that additional steps, such as accepting the EULA and adjusting permissions, may be required.

- **Common Uses**:
  - **Debugging**: Capturing and analyzing system events to troubleshoot application issues.
  - **Security**: Monitoring for unauthorized registry and file system modifications.

#### Sigcheck

Sigcheck is a command-line utility that verifies the digital signatures of files, ensuring their integrity and authenticity.

- **Usage Example**:
  ```plaintext
  sigcheck -u -e C:\Windows\System32
  ```
  - **-u**: Shows files that are unknown by VirusTotal or have a non-zero detection.
  - **-e**: Scans executable images only.

- **Common Uses**:
  - **Integrity Checks**: Verifying that system files have not been tampered with.
  - **Malware Detection**: Identifying unsigned or suspicious files.

#### SDelete (Secure Delete)

SDelete is a command-line utility for securely deleting files and cleansing free space on a disk.

- **Usage Example**:
  ```plaintext
  sdelete -z C:
  ```
  This command zeroes out the free space on drive C:.

- **Common Uses**:
  - **Data Sanitization**: Ensuring deleted files cannot be recovered.
  - **Compliance**: Meeting data destruction standards, such as DOD 5220.22-M.

#### TCPView

TCPView is a network monitoring tool that provides a detailed list of all TCP and UDP endpoints on your system.

- **Usage Example**:
  ```plaintext
  tcpview -accepteula
  ```
Some useful options:
Set filter for TCP v4 only
Enable resolve addresses, makes it easier to spot malicious addresses.

- **Common Uses**:
  - **Network Troubleshooting**: Identifying open connections and their associated processes.
  - **Security Monitoring**: Detecting unauthorized network activity.

#### Autoruns

Autoruns is a utility that shows what programs are configured to run during system bootup or login.

- **Usage Example**:
  Simply running `autoruns.exe` will open the graphical interface.

- **Common Uses**:
  - **Startup Optimization**: Identifying and disabling unnecessary startup programs.
  - **Malware Detection**: Finding persistence mechanisms used by malware.

#### PsExec

PsExec is a light-weight telnet-replacement that lets you execute processes on other systems, complete with full interactivity for console applications.

- **Usage Example**:
  ```plaintext
  psexec \\remote-computer -u username -p password cmd
  ```

- **Common Uses**:
  - **Remote Administration**: Running commands on remote machines.
  - **Lateral Movement**: Adversaries often use PsExec for moving across a network.


