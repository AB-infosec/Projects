# Windows Forensics Documentation


## Important Directories and Files

### System Registry Files

The following files are located in `C:\Windows\System32\Config` and are mounted at specific locations in the Windows Registry:

- `DEFAULT` (mounted on `HKEY_USERS\DEFAULT`)
- `SAM` (mounted on `HKEY_LOCAL_MACHINE\SAM`)
- `SECURITY` (mounted on `HKEY_LOCAL_MACHINE\Security`)
- `SOFTWARE` (mounted on `HKEY_LOCAL_MACHINE\Software`)
- `SYSTEM` (mounted on `HKEY_LOCAL_MACHINE\System`)

### User-Specific Files

- `C:\Users\<username>\AppData\Local\Microsoft\Windows\NTUSER.DAT` (mounted on `HKEY_CURRENT_USER` when a user logs in)
- `C:\Users\<username>\USRCLASS.DAT` (mounted on `HKEY_CURRENT_USER\Software\CLASSES`)

### Program Execution and Compatibility

- `C:\Windows\AppCompat\Programs\Amcache.hve`: Provides information on programs that were recently run on the system.

## Registry Keys and Their Uses

### System Information

- **OS Information**: `SOFTWARE\Microsoft\Windows NT\CurrentVersion`
- **Configuration Data**:
  - `SYSTEM\ControlSet001`
  - `SYSTEM\ControlSet002`
  - `HKLM\SYSTEM\CurrentControlSet`
  - `SYSTEM\Select\LastKnownGood`

- **Computer Name**: `SYSTEM\CurrentControlSet\Control\ComputerName\ComputerName`
- **Time Zone**: `SYSTEM\CurrentControlSet\Control\TimeZoneInformation`
- **Network Interfaces and Past Networks**:
  - `SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\Interfaces`
  - `SOFTWARE\Microsoft\Windows NT\CurrentVersion\NetworkList\Signatures\Unmanaged`
  - `SOFTWARE\Microsoft\Windows NT\CurrentVersion\NetworkList\Signatures\Managed`

### User Information

- **User Accounts**: `SAM\Domains\Account\Users`

### Recent Files and MRU Lists

- **Recent Documents**:
  - `NTUSER.DAT\Software\Microsoft\Windows\CurrentVersion\Explorer\RecentDocs`
  - `NTUSER.DAT\Software\Microsoft\Windows\CurrentVersion\Explorer\RecentDocs\.pdf`
  
- **Microsoft Office Files**:
  - `NTUSER.DAT\Software\Microsoft\Office\VERSION`
  - `NTUSER.DAT\Software\Microsoft\Office\VERSION\UserMRU\LiveID_####\FileMRU`

- **Last Save Locations**:
  - `NTUSER.DAT\Software\Microsoft\Windows\CurrentVersion\Explorer\ComDlg32\OpenSavePIDlMRU`
  - `NTUSER.DAT\Software\Microsoft\Windows\CurrentVersion\Explorer\ComDlg32\LastVisitedPidlMRU`

- **Explorer/Search Bar History**:
  - `NTUSER.DAT\Software\Microsoft\Windows\CurrentVersion\Explorer\TypedPaths`
  - `NTUSER.DAT\Software\Microsoft\Windows\CurrentVersion\Explorer\WordWheelQuery`

### Execution Information

- **Application Run Count**: 
  - `NTUSER.DAT\Software\Microsoft\Windows\Currentversion\Explorer\UserAssist\{GUID}\Count`
  
- **Executable Information**:
  - `SYSTEM\CurrentControlSet\Control\Session Manager\AppCompatCache`
  
- **Executed Program Paths**:
  - `SYSTEM\CurrentControlSet\Services\bam\UserSettings\{SID}`
  - `SYSTEM\CurrentControlSet\Services\dam\UserSettings\{SID}`

### USB Devices

- **USB Storage Devices**:
  - `SYSTEM\CurrentControlSet\Enum\USBSTOR`
  - `SYSTEM\CurrentControlSet\Enum\USB`

- **USB Device Usage Times**:
  - `SYSTEM\CurrentControlSet\Enum\USBSTOR\Ven_Prod_Version\USBSerial#\Properties\{83da6326-97a6-4088-9453-a19231573b29}\####`
    - `0064`: First Connection time
    - `0066`: Last Connection time
    - `0067`: Last removal time

## Useful Tools

### KAPE
- **KAPE**: A tool for capturing registry files.
  - Documentation: [Windows Forensics Cheatsheet](file:///C:/Users/necro/Downloads/WindowsForensicsCheatsheet-TryHackMe-1642092762578-1.pdf)

### NTFS Journal and Volume Shadow Copies
- **NTFS Journal**: The `$LOGFILE` keeps the journaling file.
- **Volume Shadow Copies**: Helps restore previous versions but is often targeted by ransomware.

### Browser Artifacts
- **Internet Explorer and Other Browsers**: Use Alternate Data Streams to identify files downloaded from the internet. Also attack vector for threat actors. 

### Eric Zimmerman's Tools
- **MFTECmd**: Browse parsed data from `$MFT`, `$Boot`.
  - Command: `MFTECmd.exe -f <path-to-$MFT-file> --csv <path-to-save-results-in-csv>`
  
- **EZviewer**: View the output from `MFTECmd`.

### Prefetch Files
- **Prefetch Directory**: When a program is run, it creates an entry in the `C:\Windows\Prefetch` directory as a `.pf` file.

### Windows 10 Timeline
- **Timeline File**: Stored in `C:\Users\<username>\AppData\Local\ConnectedDevicesPlatform\{randomfolder}\ActivitiesCache.db`.
  - Tool: `WxTCmd.exe` to view this info.
  - Command: `WxTCmd.exe -f <path-to-timeline-file> --csv <path-to-save-csv>`

### Edge Browser
- **WebCache Files**: Located at `C:\Users\<username>\AppData\Local\Microsoft\Windows\WebCache\WebCacheV*.dat`.

### LECmd
- **LECmd**: Tool to view recent files, last opened files, etc.
  - Command: `LECmd.exe -d C:\Users\John\AppData\Roaming\Microsoft\Windows\Recent\ — csv C:\`

### Setup Logs
- **Setup API Log**: Reveals any newly attached device.
  - Path: `C:\Windows\inf\setupapi.dev.log`

### Memory Forensics

# Redline: FireEye's Endpoint Security Tool

## Overview

Redline is a free, powerful tool developed by FireEye (now part of Mandiant) for endpoint investigation and threat hunting. It allows security professionals to analyze and triage potentially compromised hosts.

## Key Features

- Memory analysis
- File system analysis
- Registry analysis
- Network connection examination
- Process inspection
- IOC (Indicators of Compromise) scanning

## Installation

1. Download Redline
2. Install the application on a forensic workstation (not on the system being analyzed)

## Common Use Cases

### 1. Memory Analysis

Redline can capture and analyze system memory to identify active processes, loaded modules, and network connections.

Example:
```
Redline.exe -MemoryAcquisition -StandardCollection
```

### 2. Threat Hunting with IOCs

Use Redline to scan systems for known Indicators of Compromise (IOCs).

Example:
1. Create an IOC file using the IOC Editor
2. In Redline, select "IOC Search Collector"
3. Choose the IOC file and run the analysis

### 3. Timeline Analysis

Redline can create a timeline of system events to help identify suspicious activities.

Example:
1. Collect data using "Comprehensive Collector"
2. Open the analysis session
3. Navigate to the "Timelines" tab

### 4. Malware Detection

Analyze running processes and files for potential malware.

Example:
1. Run a "Standard Collector"
2. Review the "Processes" tab
3. Look for suspicious processes, unusual parent-child relationships, or unexpected network connections

### 5. Rootkit Detection

Redline can help identify potential rootkits by comparing memory-resident data with on-disk data.

Example:
1. Run a "Comprehensive Collector"
2. Review the "System Information" tab
3. Look for discrepancies in driver lists or hidden processes

## Basic Workflow

1. Data Collection:
   - Use Redline to create a collector
   - Run the collector on the target system
   - Retrieve the collected data

2. Analysis:
   - Open the collected data in Redline
   - Use various tabs (Processes, File System, Registry, etc.) to investigate

3. Reporting:
   - Generate reports within Redline
   - Export data for further analysis or documentation

