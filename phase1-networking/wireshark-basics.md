## Wireshark Basics — SOC Analyst Notes.

## What is wireshark?
Wireshark is a network protocol analyzer that lets you capture and interactively browse the traffic running on a computer network.
It has both a GUI and a CLI for analysts who want prefer any of them. The GUI is recommended when you want to analyze a file with the size of 500MB.
Anything more that will cause wireshark to Bog down and that's why T-shark is used for larger file sizes.

## Why defenders Use It
Soc analyst use this tool to analyze packets that run through the network. it makes them identify traffics that may have come from an attacker

## Analytical tools i learnt

Capture File Properties: This provides the start time, end time, and duration of the capture, 
which is vital for verifying if the pcap matches the timeframe of a reported incident

Protocol Hierarchy: This lists all protocols present in the pcap (such as SMTP, HTTP, or ICMP), 
allowing analysts to identify unusual activity, like mail traffic in a non-mail environment

Conversations and Endpoints: These tools identify top talkers by sorting traffic by bytes, 
helping to spot large, potentially malicious outbound data transfers

Follow TCP Stream: This feature reassembles packets to show the full communication between a client and a server in a readable format

Export Objects: Analysts can extract files (e.g., images or potential malware) sent via HTTP for further analysis

## Basic navigations

The Wireshark GUI is divided into several primary panes for detailed analysis:
- Packet List Pane: Provides a high-level chronological view of all captured packets
- Packet Details Pane: Organized by OSI Layers (Layers 1 through 4 and above), allowing analysts to drill down into specific protocol headers
- Packet Bytes Pane: Displays the raw data of a selected packet in both Hex and ASCII formats
- Display Filter Bar: Located at the top, this allows users to isolate traffic by protocol (e.g., TCP, IP) or specific attributes

## Managing Large PCAPs with T-Shark
there is a feature in wireshark that has to be enabled when you want to analyze large pcap files. it's called T-shark.
It's the command line version of wireshark that is included in the standard installation. It can read the large file filter for 
a specific traffic and output a smaller and more manageable Pcap.

Commands: Using PowerShell, analysts can run commands like t-shark -r [input file] -w [output file] [filter] to isolate relevant data

## command line with T-shark

When faced with large pcaps (e.g., 2 GB), analysts should use T-Shark via PowerShell or 
a terminal to filter data before opening it in the GUI

## Core Commands:
-r: Read an existing pcap file
-w: Write the filtered output to a new, smaller pcap file

## Filtering Logic:
ip.addr == [IP]: Captures bidirectional traffic (both source and destination)
ip.src or ip.dst: Used for narrow, unidirectional searches

## Wireshark for detecting port scan
- To detect port scan, you can filter connections like TCP SYNs. example; tcp.flags.syn == 1 && tcp.flags.ack == 0

## Key Takeaways
- To avoid software instability, use T-Shark to filter pcaps larger than 500 MB down to a manageable size before attempting to open them in the Wireshark GUI
- Understanding how wireshark works helps soc analyst to know what traffic came from the attacker.
- Understand the difference between ip.addr (bidirectional) and ip.src/dst (unidirectional)
to ensure you are capturing the full context of a conversation during an investigation
