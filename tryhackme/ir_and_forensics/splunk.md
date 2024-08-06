#SPLUNK

#forwarder
It's the agent / hook on the end device. 
What it collects:
Event logs, web server traffic, DB connection request, response, error

#indexer
gets all the data from #forwarders 
Normalizes data into field-value pairs, checks datatype, stores them as events. 
markdown

## Splunk: Essential Commands and Usage

Splunk is a powerful platform for searching, monitoring, and analysing machine-generated data. We'll cover some of the most useful commands, how to search a specific index, and how to find Indicators of Compromise (IOCs).

### Useful Commands

- `index` - Specifies the index to search within.
- `sourcetype` - Filters search results by the type of data source.
- `stats` - Generates statistics based on the search results.
- `table` - Formats the search results into a table.
- `eval` - Creates new fields or changes the values of existing fields.
- `rex` - Extracts fields using regular expressions.
- `where` - Filters results based on a condition.
- `fields` - Includes or excludes specific fields in the search results.

### Searching a Specific Index

To search within a specific index, use the `index` command:

`index="your_index_name" | search error

This command will search for the term "error" within the specified index.

### Finding IOCs

Indicators of Compromise (IOCs) can be identified using specific search queries tailored to detect malicious activity. Here are a few examples:

#### Searching for Malicious IPs

`index="your_index_name" sourcetype="your_sourcetype" src_ip="malicious_ip"

Replace `your_index_name`, `your_sourcetype`, and `malicious_ip` with the appropriate values.

#### Detecting Suspicious Executables

`index="your_index_name" sourcetype="your_sourcetype" file_name="*.exe"

This command searches for executable files in the specified index and sourcetype.

#### Finding Unusual Login Attempts

`index="your_index_name" sourcetype="your_sourcetype" "login attempt" | stats count by user | where count > 5

This command identifies users with more than five login attempts.

### Example: Combining Commands

Here’s an example that combines multiple commands to find IOCs effectively:

`index="security" sourcetype="windows
`" EventCode=4625 | stats count by src_ip | where count > 10`

This search looks for failed login attempts (EventCode 4625) in the "security" index, counts the occurrences by source IP, and filters results to show only those with more than 10 attempts.

## Rooms

#### Benign
Difficulty: Medium
Time: 150 mins


One of the client’s IDS indicated a potentially suspicious process execution indicating one of the hosts from the HR department was compromised. Some tools related to network information gathering / scheduled tasks were executed which confirmed the suspicion. Due to limited resources, we could only pull the process execution logs with Event ID: 4688 and ingested them into Splunk with the index **win_eventlogs** for further investigation.



How many logs are ingested from the month of March, 2022?

Let's start by adding the index: `index = win_eventlogs`
Now, set the time frame to all time. 

Imposter Alert: There seems to be an imposter account observed in the logs, what is the name of that user?

Lets look at unique user names: `index = win_eventlogs| rare limit=20 UserName`

Which user from the HR department was observed to be running scheduled tasks?

`index = win_eventlogs ProcessName="*\schtasks.exe"`

Filter for usernames, search for unusual names, cross reference with HR department from the room intro.

Which user from the HR department executed a system process (LOLBIN) to download a payload from a file-sharing host.

A quick research on LOLBIN, aka Living of the Land Binaries reveals that certutil is often used for this technique. https://www.securityhq.com/blog/security-101-lolbins-malware-exploitation/

`index = win_eventlogs ProcessName="*Certutil.exe"`

To bypass the security controls, which system process (lolbin) was used to download a payload from the internet?

Revealed in the previous article

What was the date that this binary was executed by the infected host? format (YYYY-MM-DD)

After finding out the key event with the download, the rest of the questions are easily answered. 

Which third-party site was accessed to download the malicious payload?

Part of the `index = win_eventlogs HostName=HR_01 UserName=haroon ProcessName="C:\\Windows\\System32\\certutil.exe"` result

What is the name of the file that was saved on the host machine from the C2 server during the post-exploitation phase?

can be found at the end of the CommandLine field.

The suspicious file downloaded from the C2 server contained malicious content with the pattern THM{..........}; what is that pattern?

Navigate to the URL to find out. 

What is the URL that the infected host connected to?

Copy paste the URL. 