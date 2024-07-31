### Sysmon in Windows Forensics

Sysmon (System Monitor) is a powerful tool used in Windows forensics for monitoring and logging system activity to the Windows event log. This information is crucial for analyzing security incidents, detecting malicious activity, and understanding system behavior.

#### Event Storage

Sysmon events are stored in the following location:
```
Applications and Services Logs/Microsoft/Windows/Sysmon/Operational
```

#### Configuration

Sysmon requires a configuration file to specify which events to monitor and log. A highly recommended configuration file, maintained by SwiftOnSecurity, can be found here:
[SwiftOnSecurity Sysmon Config](https://github.com/SwiftOnSecurity/sysmon-config)

This configuration file provides comprehensive monitoring rules that cover various types of suspicious activities and system behaviors.

#### Common Uses of Sysmon

1. **Intrusion Detection**:
   - **Port Monitoring**: Sysmon can monitor network connections, helping to detect unauthorized access. For example, ports 4444 and 5555 are commonly used by Metasploit for reverse shells and other attacks.

2. **File Monitoring**:
   - **Alternative Data Streams (ADS)**: Sysmon can be used to hunt for ADS, which are used by malware to hide data within files. These streams are labeled as `$DATA`.

3. **Process and Thread Monitoring**:
   - **DLL Injection**: Attackers inject malicious DLLs into legitimate processes. Sysmon can log such activities.
   - **Thread Hijacking**: This involves taking control of an existing thread in a legitimate process. Sysmon helps in detecting these actions.
   - **Process Hollowing**: Attackers start a new process in a suspended state and replace its memory with malicious code. Sysmon logs these remote thread creations via the `CreateRemoteThread` API.

#### Example of Sysmon Use

1. **Detecting Suspicious Network Activity**:
   - Analyzing logs for unusual connections on specific ports, such as 4444 and 5555.
   - Example event log entry:
     ```plaintext
     EventID: 3 (Network connection detected)
     Source IP: 192.168.1.100
     Destination IP: 10.10.10.10
     Destination Port: 4444
     ```

2. **Hunting for ADS**:
   - Searching for `$DATA` entries in Sysmon logs to detect hidden data streams.
   - Example event log entry:
     ```plaintext
     EventID: 15 (FileCreateStreamHash)
     File Name: C:\Users\Public\hidden.txt:secret_data.txt
     Stream Type: $DATA
     ```

3. **Detecting Remote Thread Creation**:
   - Monitoring for the use of `CreateRemoteThread` API which is often used in DLL Injection, Thread Hijacking, and Process Hollowing.
   - Example event log entry:
     ```plaintext
     EventID: 8 (CreateRemoteThread detected)
     Source Process: explorer.exe
     Target Process: svchost.exe
     ```

