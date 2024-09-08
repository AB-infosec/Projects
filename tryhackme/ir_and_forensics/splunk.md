# Splunk

### forwarder
It's the agent / hook on the end device. 
What it collects:
Event logs, web server traffic, DB connection request, response, error

### indexer
gets all the data from #forwarders 
Normalizes data into field-value pairs, checks datatype, stores them as events. 

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

GUI= Settings, indexes will show all current indexes and their log count.

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
---

## Splunk Room: **Benign**

### Difficulty: Medium  
### Estimated Time: 150 mins

---

### Situation Overview

An IDS alert was triggered, indicating potential malicious activity on one of the HR department's hosts. The alert flagged suspicious processes related to network information gathering and scheduled tasks, suggesting a possible compromise. Due to resource constraints, only process execution logs (Event ID: 4688) were pulled and ingested into Splunk under the index **win_eventlogs** for further investigation. Your task is to analyze these logs, identify suspicious activities, and answer the following questions.

---

### Task 1: Log Analysis

**Q1: How many logs are ingested from March 2022?**

1. Start by adding the relevant index to your search:

   ```splunk
   index=win_eventlogs
   ```

2. Set the time frame to March 2022:

   - In the search bar, use the time picker to select March 2022 or manually add the time range using:
     ```splunk
     earliest="03/01/2022:00:00:00" latest="03/31/2022:23:59:59"
     ```

3. Count the total number of logs:

   ```splunk
   stats count
   ```

---

### Task 2: Identify the Imposter

**Q2: What is the name of the imposter account observed in the logs?**

1. Look for unique usernames in the logs to identify any anomalies:

   ```splunk
   index=win_eventlogs | rare limit=20 UserName
   ```

2. Examine the results for any suspicious or unfamiliar usernames that do not belong to the HR department.

---

### Task 3: Scheduled Task Execution

**Q3: Which user from the HR department was observed running scheduled tasks?**

1. Filter logs where the process `schtasks.exe` was executed:

   ```splunk
   index=win_eventlogs ProcessName="*\schtasks.exe"
   ```

2. Focus on the `UserName` field to determine which HR user ran the task. Cross-reference these usernames with the HR department information provided in the room intro.

---

### Task 4: LOLBIN Execution

**Q4: Which user from the HR department executed a system process (LOLBIN) to download a payload from a file-sharing host?**

1. Research Living Off The Land Binaries (LOLBINs) and focus on processes like `certutil.exe` that are often used in such attacks:

   ```splunk
   index=win_eventlogs ProcessName="*Certutil.exe"
   ```

2. Identify the user from the HR department who executed this process.

---

### Task 5: Bypassing Security Controls

**Q5: To bypass security controls, which system process (LOLBIN) was used to download a payload from the internet?**

1. From your previous search, confirm that `certutil.exe` was used as the LOLBIN for this task.

---

### Task 6: Timeline of Events

**Q6: What was the date that this binary was executed by the infected host? (format YYYY-MM-DD)**

1. Find the event log that corresponds to the execution of the binary (`certutil.exe`) and note the timestamp.

2. Format the date as `YYYY-MM-DD`.

---

### Task 7: Malicious Payload Download

**Q7: Which third-party site was accessed to download the malicious payload?**

1. Use the `CommandLine` field in your previous searches to identify the URL or IP address that `certutil.exe` connected to for downloading the payload.

---

### Task 8: File Saved on Host

**Q8: What is the name of the file that was saved on the host machine from the C2 server during the post-exploitation phase?**

1. Inspect the `CommandLine` field in the relevant log to find the name of the file that was saved on the host.

---

### Task 9: Malicious Content Detection

**Q9: The suspicious file downloaded from the C2 server contained malicious content with the pattern THM{..........}; what is that pattern?**

1. Analyze the contents of the file (if available) or use threat intelligence to identify the pattern in the malicious content.

---

### Task 10: Infected Host Connection

**Q10: What is the URL that the infected host connected to?**

1. Copy the URL identified in your previous searches.

---


## Random information from various Splunk rooms:


EventCode 11 file created
EventCode 8 CreateRemoteThread
EventCode 12 - reg key manipulation

This will find out the DNS queries:
index="main" Image="C:\\Windows\\Temp\\OUTSTANDING_GUTTER.exe" TaskCategory="Dns query (rule: DnsQuery)" 
| table QueryName

Essentially: from the INTERESTING FIELDS section, select TaskCategory, Dns query (rule: DnsQuery)

