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

## Miscellaneous
 ```
# Other tools
# download from http://dl.packetstormsecurity.net/UNIX/scanners/snmpenum.zip
# convert to unix
dos2unix.txt
#run
perl snmpenum.pl <targetIP> public windows.txt
```
