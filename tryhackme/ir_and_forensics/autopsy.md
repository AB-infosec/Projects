Supported file types of Autopsy:

    Raw Single (For example: *.img, *.dd, *.raw, *.bin)
    Raw Split (For example: *.001, *.002, *.aa, *.ab, etc)
    EnCase (For example: *.e01, *.e02, etc)
    Virtual Machines (For example: *.vmdk, *.vhd)
	
Documentation: 
http://sleuthkit.org/autopsy/docs/user-docs/4.12.0/ds_page.html

ingest modules are esentially plugins

Right click run ingest modules to reinitiate if the source was already added.

The Data Sources tree view item allows windows explorer like browsing of the volume's content.

File filtering by MIME type can help to bypass attacker obfuscation techniques. 

Content viewer section will have the following fields:
S - Score (notable, suspicous, etc. analysed by the modules)
C - Comment
O - Occurence (how many times we seen this file/folder. Needs central repo)


You can generate a report and continue the investigation outside of Autopsy.

Under Data Sources a summary is available for overview. 

The Timeline tool is composed of three areas:

    Filters: Narrow the events displayed based on the filter criteria
    Events: The events are displayed here based on the View Mode
    Files/Contents: Additional information on the event(s) is displayed in this area

There are three view modes:

    Counts: The number of events is displayed in a bar chart view
    Details: Information on events is displayed, but they are clustered and collapsed, so the UI is not overloaded
    List: The events are displayed in a table view
	
some key elements of this function:

apply filters: fore example file events only
right click on the graph and apply Zoom into Time Range

change the chart to linear if needed.

notes from investigating a test case:

The following are the room objectives:

Certainly. Here's the formatted markdown Q&A section based on the full file you provided:

# Windows Forensics Investigation Q&A

## Image Information

**Q: What is the MD5 hash of the E01 image?**

A: [Check the Data Summary for this information]

## System Information

**Q: What is the computer account name?**

A: [Look under Results: Operating System information]

## User Accounts and Login Information

**Q: List all the user accounts. (alphabetical order)**

A: [same tree, under Operating System User Account]

**Q: Who was the last user to log into the computer?**

A: [under the same location, the Date Accessed table will show this data, sort it.]

## Network Information

**Q: What was the IP address of the computer?**

A: [this was challanging, I tried to look at OS info, keyword search for ipconfig, looking at hosts file, no luck. Turns out there is a networking tool installed, with an .ini config file which includes the IP and mac address]

**Q: What was the MAC address of the computer? (XX-XX-XX-XX-XX-XX)**

A: [same file as above]

**Q: What is the name of the network card on this computer?**

A: [after a lot of searching, I've found the relevant registry entry, which I exported: SOFTWARE\Microsoft\Windows NT\CurrentVersion\NetworkCards

## Software and Tools

**Q: What is the name of the network monitoring tool?**

A: [we actually found this out earlier in the installed programs tab

## User Activities

**Q: A user bookmarked a Google Maps location. What are the coordinates of the location?**

A: [Results -> Extracted Content -> Web Bookmarks conviniently contains this information]

**Q: A user has his full name printed on his desktop wallpaper. What is the user's full name?**

A: [This was surprisingly difficult, I've located the wallpaper file for multiple users, and it was a small text in on of the files. The name was later referenced in searches, etc.]

**Q: A user had a file on her desktop. It had a flag but she changed the flag using PowerShell. What was the first flag?**

A: [This was tricky, but running a keyword search for flag{ revealed a ConsoleHost_history.txt file, which had the original flag]

## Security and Exploits

**Q: The same user found an exploit to escalate privileges on the computer. What was the message to the device owner?**

A: [simply looked at the same user, the desktop had a .ps1 file with this flag and a priv. esc. script]

**Q: 2 hack tools focused on passwords were found in the system. What are the names of these tools? (alphabetical order)**

A: [the first one was easy, mimikatz was in the download files, and many other locations. However, I had to search through MS Defender files to find the other malicious file]

**Q: There is a YARA file on the computer. Inspect the file. What is the name of the author?**

A: [Tools: File search by attribute allows searching for .yar and .yara files]

**Q: One of the users wanted to exploit a domain controller with an MS-NRPC based exploit. What is the filename of the archive that you found? (include the spaces in your answer)**

A: [the question is referencing CVE-2020–1472 (also known as “Zerologon”) so I just searched for terms related to zerologon, such as python files, zip files etc. keyword search revealed the answer]