# Volatility 3


## Common Uses of Volatility 3

1. **List running processes**
   ```
   volatility3 -f memory.dump windows.pslist
   ```

2. **Display process tree**
   ```
   volatility3 -f memory.dump windows.pstree
   ```

3. **Scan for hidden/terminated processes**
   ```
   volatility3 -f memory.dump windows.psscan
   ```

4. **List network connections**
   ```
   volatility3 -f memory.dump windows.netscan
   ```

5. **Dump a process's memory**
   ```
   volatility3 -f memory.dump windows.dumpfiles --pid 1234
   ```

6. **Scan for malware using YARA rules**
   ```
   volatility3 -f memory.dump windows.vadyarascan --yara-rule=/path/to/rule.yar
   ```

7. **List loaded kernel modules**
   ```
   volatility3 -f memory.dump windows.modules
   ```

8. **Examine command line arguments**
   ```
   volatility3 -f memory.dump windows.cmdline
   ```

9. **List open handles for processes**
   ```
   volatility3 -f memory.dump windows.handles
   ```

10. **Extract registry hives**
    ```
    volatility3 -f memory.dump windows.registry.hivelist
    volatility3 -f memory.dump windows.registry.printkey --key "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run"
    ```

## Key Modules

Volatility 3 includes numerous modules for Windows, Linux, and Mac analysis. Some key modules include:

- `windows.pslist`, `windows.psscan`, `windows.pstree`: Process analysis
- `windows.netscan`, `windows.netstat`: Network connection analysis
- `windows.malfind`: Potential malware detection
- `windows.dlllist`, `windows.modules`: Loaded module analysis
- `windows.handles`: Open handle analysis
- `windows.registry.*`: Registry analysis
- `yarascan`: YARA rule scanning


Volatility 3 is constantly evolving, and new modules may be added or existing ones updated. Always refer to the official documentation for the most up-to-date information.

## Volatility3 for Memory Forensics

### Introduction to Volatility3

Volatility3 is a powerful memory forensics framework used to analyze volatile memory (RAM) dumps. It helps investigators uncover evidence of malicious activities by examining memory artifacts that may not be present on disk. Volatility3 supports various memory dump formats and provides numerous plugins for detailed analysis.

### Tools for Extracting Memory Dumps

Before using Volatility3, you need to extract memory dumps from the target system. Here are some tools for extracting memory from both bare metal systems and virtual machines (VMs):

#### Bare Metal Systems
- **FTK Imager**: Creates forensic images of physical and logical drives.
- **Redline**: Memory and malware analysis tool.
- **DumpIt.exe**: Simple tool for capturing memory dumps.
- **win32dd.exe / win64dd.exe**: Tools for acquiring physical memory dumps.
- **Memoryze**: Analyzes memory images and live memory.
- **FastDump**: Captures memory dumps quickly.

#### Virtual Machines
- **VMWare**: `.vmem` files
- **Hyper-V**: `.bin` files
- **Parallels**: `.mem` files
- **VirtualBox**: `.sav` files (note: only partial memory captures)

### Identifying the Memory Image

If the source of the memory dump is unknown, you can use the `imageinfo` plugin in Volatility3 to identify the image details:

```sh
volatility3 -f <memory-dump> imageinfo
```

### Essential Volatility3 Plugins

Volatility3 provides a wide range of plugins to analyze different aspects of the memory dump. Here are some commonly used plugins:

- **`windows.pslist`**: Lists active processes.
- **`windows.psscan`**: Scans for unlinked (hidden) processes.
- **`windows.pstree`**: Displays processes in a tree format for better visualization.
- **`windows.netstat`**: Lists network connections (not fully reliable).
- **`windows.dlllist`**: Lists loaded DLLs for each process.
- **`windows.dumpfiles`**: Dumps files from the memory of a specific process.

### Advanced Analysis for Evasion Techniques

Adversaries often use various hooking techniques to evade detection of their malicious code. Volatility3 includes modules to detect these evasion methods:

- **SSDT Hooks**: Alterations in the System Service Descriptor Table.
- **IRP Hooks**: Interception of I/O Request Packets.
- **IAT Hooks**: Modifications in the Import Address Table.
- **EAT Hooks**: Changes in the Export Address Table.
- **Inline Hooks**: Direct code manipulation.

To dig deeper if evasion techniques are suspected, use the following Volatility3 modules:

- **`windows.ssdt`**: Scans for SSDT hooks.
- **`windows.driverscan`**: Scans for loaded drivers.
- **`windows.modules`**: Lists loaded kernel modules.

### Example Command

Dumping files from a specific process ID (PID) can be done with the `windows.dumpfiles` plugin:

```sh
volatility3 -f <memory-dump> windows.dumpfiles --pid 740
```

### Useful Resources

For more in-depth usage of Volatility3 and its plugins, refer to the following cheatsheet:

- [Volatility3 Cheatsheet](https://blog.onfvp.com/post/volatility-cheatsheet/)

---


Volatility 3 Framework 2.7.0
usage: volatility [-h] [-c CONFIG] [--parallelism [{processes,threads,off}]] [-e EXTEND] [-p PLUGIN_DIRS] [-s SYMBOL_DIRS] [-v] [-l LOG] [-o OUTPUT_DIR] [-q] [-r RENDERER] [-f FILE]
                  [--write-config] [--save-config SAVE_CONFIG] [--clear-cache] [--cache-path CACHE_PATH] [--offline] [--filters FILTERS] [--single-location SINGLE_LOCATION]
                  [--stackers [STACKERS ...]] [--single-swap-locations [SINGLE_SWAP_LOCATIONS ...]]
                  plugin ...

An open-source memory forensics framework

options:
  -h, --help            Show this help message and exit, for specific plugin options use 'volatility <pluginname> --help'
  -c CONFIG, --config CONFIG
                        Load the configuration from a json file
  --parallelism [{processes,threads,off}]
                        Enables parallelism (defaults to off if no argument given)
  -e EXTEND, --extend EXTEND
                        Extend the configuration with a new (or changed) setting
  -p PLUGIN_DIRS, --plugin-dirs PLUGIN_DIRS
                        Semi-colon separated list of paths to find plugins
  -s SYMBOL_DIRS, --symbol-dirs SYMBOL_DIRS
                        Semi-colon separated list of paths to find symbols
  -v, --verbosity       Increase output verbosity
  -l LOG, --log LOG     Log output to a file as well as the console
  -o OUTPUT_DIR, --output-dir OUTPUT_DIR
                        Directory in which to output any generated files
  -q, --quiet           Remove progress feedback
  -r RENDERER, --renderer RENDERER
                        Determines how to render the output (quick, none, csv, pretty, json, jsonl)
  -f FILE, --file FILE  Shorthand for --single-location=file:// if single-location is not defined
  --write-config        Write configuration JSON file out to config.json
  --save-config SAVE_CONFIG
                        Save configuration JSON file to a file
  --clear-cache         Clears out all short-term cached items
  --cache-path CACHE_PATH
                        Change the default path (/home/parrot/.cache/volatility3) used to store the cache
  --offline             Do not search online for additional JSON files
  --filters FILTERS     List of filters to apply to the output (in the form of [+-]columname,pattern[!])
  --single-location SINGLE_LOCATION
                        Specifies a base location on which to stack
  --stackers [STACKERS ...]
                        List of stackers
  --single-swap-locations [SINGLE_SWAP_LOCATIONS ...]
                        Specifies a list of swap layer URIs for use with single-location

Plugins:
  For plugin specific options, run 'volatility <plugin> --help'

  plugin
    banners.Banners     Attempts to identify potential linux banners in an image
    configwriter.ConfigWriter
                        Runs the automagics and both prints and outputs configuration in the output directory.
    frameworkinfo.FrameworkInfo
                        Plugin to list the various modular components of Volatility
    isfinfo.IsfInfo     Determines information about the currently available ISF files, or a specific one
    layerwriter.LayerWriter
                        Runs the automagics and writes out the primary layer produced by the stacker.
    linux.bash.Bash     Recovers bash command history from memory.
    linux.capabilities.Capabilities
                        Lists process capabilities
    linux.check_afinfo.Check_afinfo
                        Verifies the operation function pointers of network protocols.
    linux.check_creds.Check_creds
                        Checks if any processes are sharing credential structures
    linux.check_idt.Check_idt
                        Checks if the IDT has been altered
    linux.check_modules.Check_modules
                        Compares module list to sysfs info, if available
    linux.check_syscall.Check_syscall
                        Check system call table for hooks.
    linux.elfs.Elfs     Lists all memory mapped ELF files for all processes.
    linux.envars.Envars
                        Lists processes with their environment variables
    linux.iomem.IOMem   Generates an output similar to /proc/iomem on a running system.
    linux.keyboard_notifiers.Keyboard_notifiers
                        Parses the keyboard notifier call chain
    linux.kmsg.Kmsg     Kernel log buffer reader
    linux.library_list.LibraryList
                        Enumerate libraries loaded into processes
    linux.lsmod.Lsmod   Lists loaded kernel modules.
    linux.lsof.Lsof     Lists all memory maps for all processes.
    linux.malfind.Malfind
                        Lists process memory ranges that potentially contain injected code.
    linux.mountinfo.MountInfo
                        Lists mount points on processes mount namespaces
    linux.proc.Maps     Lists all memory maps for all processes.
    linux.psaux.PsAux   Lists processes with their command line arguments
    linux.pslist.PsList
                        Lists the processes present in a particular linux memory image.
    linux.psscan.PsScan
                        Scans for processes present in a particular linux image.
    linux.pstree.PsTree
                        Plugin for listing processes in a tree based on their parent process ID.
    linux.sockstat.Sockstat
                        Lists all network connections for all processes.
    linux.tty_check.tty_check
                        Checks tty devices for hooks
    linux.vmayarascan.VmaYaraScan
                        Scans all virtual memory areas for tasks using yara.
    mac.bash.Bash       Recovers bash command history from memory.
    mac.check_syscall.Check_syscall
                        Check system call table for hooks.
    mac.check_sysctl.Check_sysctl
                        Check sysctl handlers for hooks.
    mac.check_trap_table.Check_trap_table
                        Check mach trap table for hooks.
    mac.dmesg.Dmesg     Prints the kernel log buffer.
    mac.ifconfig.Ifconfig
                        Lists network interface information for all devices
    mac.kauth_listeners.Kauth_listeners
                        Lists kauth listeners and their status
    mac.kauth_scopes.Kauth_scopes
                        Lists kauth scopes and their status
    mac.kevents.Kevents
                        Lists event handlers registered by processes
    mac.list_files.List_Files
                        Lists all open file descriptors for all processes.
    mac.lsmod.Lsmod     Lists loaded kernel modules.
    mac.lsof.Lsof       Lists all open file descriptors for all processes.
    mac.malfind.Malfind
                        Lists process memory ranges that potentially contain injected code.
    mac.mount.Mount     A module containing a collection of plugins that produce data typically found in Mac's mount command
    mac.netstat.Netstat
                        Lists all network connections for all processes.
    mac.proc_maps.Maps  Lists process memory ranges that potentially contain injected code.
    mac.psaux.Psaux     Recovers program command line arguments.
    mac.pslist.PsList   Lists the processes present in a particular mac memory image.
    mac.pstree.PsTree   Plugin for listing processes in a tree based on their parent process ID.
    mac.socket_filters.Socket_filters
                        Enumerates kernel socket filters.
    mac.timers.Timers   Check for malicious kernel timers.
    mac.trustedbsd.Trustedbsd
                        Checks for malicious trustedbsd modules
    mac.vfsevents.VFSevents
                        Lists processes that are filtering file system events
    timeliner.Timeliner
                        Runs all relevant plugins that provide time related information and orders the results by time.
    windows.bigpools.BigPools
                        List big page pools.
    windows.callbacks.Callbacks
                        Lists kernel callbacks and notification routines.
    windows.cmdline.CmdLine
                        Lists process command line arguments.
    windows.crashinfo.Crashinfo
                        Lists the information from a Windows crash dump.
    windows.devicetree.DeviceTree
                        Listing tree based on drivers and attached devices in a particular windows memory image.
    windows.dlllist.DllList
                        Lists the loaded modules in a particular windows memory image.
    windows.driverirp.DriverIrp
                        List IRPs for drivers in a particular windows memory image.
    windows.drivermodule.DriverModule
                        Determines if any loaded drivers were hidden by a rootkit
    windows.driverscan.DriverScan
                        Scans for drivers present in a particular windows memory image.
    windows.dumpfiles.DumpFiles
                        Dumps cached file contents from Windows memory samples.
    windows.envars.Envars
                        Display process environment variables
    windows.filescan.FileScan
                        Scans for file objects present in a particular windows memory image.
    windows.getservicesids.GetServiceSIDs
                        Lists process token sids.
    windows.getsids.GetSIDs
                        Print the SIDs owning each process
    windows.handles.Handles
                        Lists process open handles.
    windows.iat.IAT     Extract Import Address Table to list API (functions) used by a program contained in external libraries
    windows.info.Info   Show OS & kernel details of the memory sample being analyzed.
    windows.joblinks.JobLinks
                        Print process job link information
    windows.ldrmodules.LdrModules
                        Lists the loaded modules in a particular windows memory image.
    windows.malfind.Malfind
                        Lists process memory ranges that potentially contain injected code.
    windows.mbrscan.MBRScan
                        Scans for and parses potential Master Boot Records (MBRs)
    windows.memmap.Memmap
                        Prints the memory map
    windows.mftscan.ADS
                        Scans for Alternate Data Stream
    windows.mftscan.MFTScan
                        Scans for MFT FILE objects present in a particular windows memory image.
    windows.modscan.ModScan
                        Scans for modules present in a particular windows memory image.
    windows.modules.Modules
                        Lists the loaded kernel modules.
    windows.mutantscan.MutantScan
                        Scans for mutexes present in a particular windows memory image.
    windows.netscan.NetScan
                        Scans for network objects present in a particular windows memory image.
    windows.netstat.NetStat
                        Traverses network tracking structures present in a particular windows memory image.
    windows.poolscanner.PoolScanner
                        A generic pool scanner plugin.
    windows.privileges.Privs
                        Lists process token privileges
    windows.pslist.PsList
                        Lists the processes present in a particular windows memory image.
    windows.psscan.PsScan
                        Scans for processes present in a particular windows memory image.
    windows.pstree.PsTree
                        Plugin for listing processes in a tree based on their parent process ID.
    windows.registry.certificates.Certificates
                        Lists the certificates in the registry's Certificate Store.
    windows.registry.hivelist.HiveList
                        Lists the registry hives present in a particular memory image.
    windows.registry.hivescan.HiveScan
                        Scans for registry hives present in a particular windows memory image.
    windows.registry.printkey.PrintKey
                        Lists the registry keys under a hive or specific key value.
    windows.registry.userassist.UserAssist
                        Print userassist registry keys and information.
    windows.sessions.Sessions
                        lists Processes with Session information extracted from Environmental Variables
    windows.skeleton_key_check.Skeleton_Key_Check
                        Looks for signs of Skeleton Key malware
    windows.ssdt.SSDT   Lists the system call table.
    windows.statistics.Statistics
                        Lists statistics about the memory space.
    windows.strings.Strings
                        Reads output from the strings command and indicates which process(es) each string belongs to.
    windows.svcscan.SvcScan
                        Scans for windows services.
    windows.symlinkscan.SymlinkScan
                        Scans for links present in a particular windows memory image.
    windows.thrdscan.ThrdScan
                        Scans for windows threads.
    windows.truecrypt.Passphrase
                        TrueCrypt Cached Passphrase Finder
    windows.vadinfo.VadInfo
                        Lists process memory ranges.
    windows.vadwalk.VadWalk
                        Walk the VAD tree.
    windows.vadyarascan.VadYaraScan
                        Scans all the Virtual Address Descriptor memory maps using yara.
    windows.verinfo.VerInfo
                        Lists version information from PE files.
    windows.virtmap.VirtMap
                        Lists virtual mapped sections.
    yarascan.YaraScan   Scans kernel memory using yara rules (string or file).

The following plugins could not be loaded (use -vv to see why): volatility3.plugins.windows.cachedump, volatility3.plugins.windows.hashdump, volatility3.plugins.windows.lsadump

