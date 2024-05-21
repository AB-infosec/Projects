```
Some of the tools for extracting from bare metal:


    FTK Imager
    Redline
    DumpIt.exe
    win32dd.exe / win64dd.exe
    Memoryze
    FastDump

and for VMs, the following file types are expected:


    VMWare - .vmem
    Hyper-V - .bin
    Parallels - .mem
    VirtualBox - .sav (only partial)
	
imageinfo can help you to identify the given image if the source is unknown. 

windows.pslist - to list processes
windows.psscan - for unlinked processes 
windows.pstree - for a better view
windows.netstat - listing network connections. Not fully reliable. 
windows.dlllist - for listing the dlls running 
windows.dumpfiles - to get the dumps of the specific PID

Adversarys will attempt to evade detection of their malicious code by the following methods:

    SSDT Hooks
    IRP Hooks
    IAT Hooks
    EAT Hooks
    Inline Hooks

The following modules can be used to dig deeper if evasion is used:
windows.ssdt
windows.driverscan
windows.modules
  