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

