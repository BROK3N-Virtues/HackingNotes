# Resources
Seriously use payloadallthethings\
https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/XXE%20Injection/README.md
# Tools
## ffuf
Example:\
``` ffuf -u http://x.x.x.x/vulnerable.php -X POST -H 'Cookie: PHPSESSID=xx' -d query=FUZZ' -w sweetwordlist -H 'Content-Type:application/x-www-form-urlencoded' ```
