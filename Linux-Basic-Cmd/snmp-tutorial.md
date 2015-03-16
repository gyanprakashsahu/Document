###SNMP Introduction
---
"SNMP, or Simple Network Management Protocol, is widely used to communicate with and monitor network devices, servers, and more, all via IP. In this case, we’ll be installing an SNMP agent on a CentOS server, which will allow for collection of data from our server, and make the information available to a remote SNMP manager."

### Steps:

```
1. yum -y install net-snmp net-snmp-utils

2. configure your snmpd.conf file enter your community string at the place of "public" if you want to make it snmp server and if you want to make it client use following command :-

echo rocommunity YourCommunityString >> /etc/snmp/snmpd.conf

I am going to make it snmp server and suppose there is already a client whose community string is "saurabh" and ip is 192.168.62.250.

3. service snmpd restart

4. chkconfig snmpd on

5. snmpwalk -v 2c -c public -O e 127.0.0.1   # test the snmp service is running fine or not.

```

### SNMP Commands: 

#### snmpwalk
```
The snmpwalk command performs a sequence of chained GETNEXT requests automatically. It is a work saving command. Rather than having to issue a series of snmpgetnext requests, one for each object ID, or node, in a sub-tree, you can simply issue one snmpwalk request on the root node of the sub-tree and the command gets the value of every node in the sub-tree.

snmpwalk -mALL -v1 -c saurabh 192.168.62.250 system

It will give you all the information related to subtree "system". You can use various MIBs and OIDs at the place of "system" or you can use subtree like "system".

```

#### snmpget
```
this command returns an administratively assigned name for this managed node. By convention, this is the node’s fully-qualified domain name. If the name is unknown, the value returned is the zero-length string.

snmpget -v2c -c saurabh -mALL 192.168.62.250 sysName.0 

```

#### snmpbulkwalk 
```
The snmpbulkwalk command uses the GETBULK SNMP protocol feature to query for an entire tree of information about a network entity. This command can pack more objects into the packets by specifying “repeaters.” As a result, the snmpbulkwalk command is faster than the snmpwalk command.

An object identifier (OID) may be given on the command line. This OID specifies which portion of the object identifier space will be searched using GETBULK requests. All variables in the subtree below the given OID are queried and their values presented to the user.

If the network entity has an error processing the request packet, an error packet will be returned and a message will be shown, helping to pinpoint why the request was malformed

If the tree search causes attempts to search beyond the end of the MIB, the message "End of MIB" will be displayed.  

### snmpbulkwalk -v2c -Os -c saurabh 192.168.62.250 system

In contrast to snmpwalk, this information will typically be gathered in a single transaction with the agent, rather than one transaction per variable found. snmpbulkwalk is thus more efficient in terms of network utilisation, which may be especially important when retrieving large tables.  

```

#### snmptable: 

```
The snmptable command retrieves the contents of an SNMP table and displays the contents in a tabular format, that is, one table row at a time, such that the resulting output resembles the table being retrieved. This is contrasted with the snmpwalk command, which displays the contents of the table one column at a time.

### snmptable -mALL -v2c -c saurabh 192.168.62.250 sysORTable

Use -Ci for indexing of output and -Cb for getting abbreviated output.

```

#### snmpset :

```
While the syntax of the snmpset command is similar to that of the snmpget command, the commands are quite different. The snmpget command merely reads the value of the specified object ID, while the snmpset command writes the value specified to the object ID. Further, along with the value to be written to the object ID, you must also specify the data type of the object ID in the snmpset command because SNMP objects support more than one data type.

The following example shows how use of the snmpget and snmpset commands together. The sequence of steps is as follows:

1.	Use the snmpget command to check to current value of the MIB object.

snmpget -mALL -v2c -c saurabh 192.168.62.250 ilomCtrlHttpEnabled.0

2.	Use the snmpset command to change the value of the MIB object.

snmpset -mALL -v2c -c saurabh 192.168.62.250 ilomCtrlHttpEnabled.0 i 1

3.	Use the snmpget command to verify that the MIB object was in fact changed to the requested value.

snmpget -mALL -v2c -c saurabh 192.168.62.250 ilomCtrlHttpEnabled.0

Note that if you try to execute this snmpset command using a public community, instead of private, it will not work. This is because the private community has write permission, but the public community does not. The Reason code returned by the command does not make this clear because it simply states that the object is not writable.

```

#### snmtrapd :

```
snmptrapd is an SNMP application that receives and logs SNMP Trap and Inform messages. Before your system can receive such messages, you must configure the trap daemon to listen for these messages.

To configure a trap daemon, perform the following steps:

1.	Configure an SNMP trap destination.

snmpset -mALL -v2c -c saurabh 192.168.62.250 ilomCtrlAlertSeverity.1 i 2 ilomCtrlAlertType.1 i 2 ilomCtrlAlertDestinationIP.1 a dest_Ip_address

2.	Start the trap receiver application, snmptrapd.

snmptrapd -mALL -Lo -f -t -OvQ -e -F "%H.%J.%K:%W:%w %q from %A:%V,% %v\n"

3.	Generate a test trap to verify that traps are being sent by the agent and received by the trap receiver.

set /SP/alertmgmt/rules testalert=true

```
Response from a device not supporting SNMP: 
Timeout: No Response from 192.168.62.250

#### Configuration of snmp on your Cisco router
```
Connect to your virtual Cisco router: default login and passwd is cisco.
enable
conf t
snmp-server community YourCommunityString ro 99
access-list 99 permit 10.10.0.0 0.0.255.255
access-list 99 permit 10.10.254.0 0.0.255.255
exit
exit     # until you get back to your PC
```
### Key Definitions:

#### OIDs:

SNMP works by querying “Objects”. An object is simply something that we can gather information about on a network device.

                   OIDs are very structured, and follow a hierarchical tree pattern – much like a folder structure on your computer. Unlike folders however, all SNMP objects are numbered. So, the top level after the root is ISO, and has the number “1”. The next level, ORG, has the number “3”, since it is the 3rd object under ISO, and so on. OIDs are always written in a numerical form, instead of a text form. So the top three object levels are written as 1.3.1 not iso\org\dod. Because of the way the tree is structured, most SNMP values we’re interested in will always start with the same set of objects – 1.3.6.1. And, in the example above, Interface Status would have an OID of 1.3.6.1.2.1.2.2.1.8.

#### MIBs:

          A MIB is like a translator that helps a Management Station to understand SNMP responses obtained from your network devices. All SNMP devices generally support something called MIB-2, which is a standard set of objects that can be monitored. The Interface status object mentioned earlier is part of this group, as are most system objects like CPU or memory utilization.

#### Three Importand MIBs and OIDs for bandwidth monitoring:
```
.1.3.6.1.2.1.2.2.1.10
ifInOctets 

.1.3.6.1.2.1.2.2.1.16
ifOutOctets 

.1.3.6.1.2.1.2.2.1.5
ifSpeed 
```
