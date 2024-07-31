# Windows Event Viewer and wevtutil.exe

Windows Event Viewer is a component of Microsoft's Windows NT operating system that lets administrators and users view the event logs on a local or remote machine. The command-line equivalent, wevtutil.exe, provides powerful options for viewing and managing event logs.

## Overview of wevtutil.exe

Wevtutil.exe (Windows Events Command Line Utility) is a command-line tool that enables you to retrieve information about event logs and publishers, install and uninstall event manifests, run queries, and export, archive, and clear logs.

To view all available commands:

```powershell
wevtutil.exe /?
```

## Key Commands

1. `el` or `enum-logs`: List log names
2. `gl` or `get-log`: Get log configuration information
3. `sl` or `set-log`: Modify configuration of a log
4. `ep` or `enum-publishers`: List event publishers
5. `gp` or `get-publisher`: Get publisher configuration information
6. `im` or `install-manifest`: Install event publishers and logs from manifest
7. `um` or `uninstall-manifest`: Uninstall event publishers and logs from manifest
8. `qe` or `query-events`: Query events from a log or log file
9. `gli` or `get-log-info`: Get log status information
10. `epl` or `export-log`: Export a log
11. `al` or `archive-log`: Archive an exported log
12. `cl` or `clear-log`: Clear a log

## PowerShell Alternative

In PowerShell, you can use the `Get-WinEvent` cmdlet to interact with Windows event logs. To list all available logs:

```powershell
Get-WinEvent -ListLog *
```

## Step-by-Step use case

### 1. Investigating a Potential Security Breach

Step 1: List all security-related logs
```powershell
wevtutil el | findstr /i security
```

Step 2: Query the Security log for failed logon attempts (Event ID 4625)
```powershell
wevtutil qe Security /c:10 /f:text /q:"*[System[(EventID=4625)]]"
```

Step 3: Export the Security log for offline analysis
```powershell
wevtutil epl Security C:\SecurityLog.evtx
```

### 2. Monitoring for Unauthorized Software Installation

Step 1: Query the Application log for new software installations (Event ID 11707)
```powershell
wevtutil qe Application /c:50 /f:text /q:"*[System[(EventID=11707)]]"
```

### 3. Tracking System Changes

Step 1: Query the System log for recent system changes (Event ID 104)
```powershell
wevtutil qe System /c:20 /f:text /q:"*[System[(EventID=104)]]"
```

### 4. Investigating PowerShell Activity

Step 1: Query the PowerShell operational log for executed scripts (Event ID 4104)
```powershell
wevtutil qe Microsoft-Windows-PowerShell/Operational /c:10 /f:text /q:"*[System[(EventID=4104)]]"
```

### 5. Detecting Potential Lateral Movement

Step 1: Query the Security log for successful logons (Event ID 4624) with logon type 3 (network)
```powershell
wevtutil qe Security /c:50 /f:text /q:"*[System[(EventID=4624)] and EventData[Data[@Name='LogonType']='3']]"
```

## Advanced Tips

1. Use the `/f:xml` option instead of `/f:text` to get more detailed output in XML format.

2. Combine multiple event IDs in a single query:
   ```powershell
   wevtutil qe Security /c:50 /f:text /q:"*[System[(EventID=4624 or EventID=4625)]]"
   ```

3. Filter events by time range:
   ```powershell
   wevtutil qe Security /c:100 /f:text /q:"*[System[TimeCreated[@SystemTime>='2023-06-01T00:00:00' and @SystemTime<='2023-06-30T23:59:59']]]"
   ```

4. Use PowerShell for more complex queries and analysis:
   ```powershell
   Get-WinEvent -FilterHashtable @{LogName='Security';ID=4625} | Select-Object TimeCreated, Message
   ```

