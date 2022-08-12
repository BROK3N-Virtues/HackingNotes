# Linux kiosk breakout #

### Open Firefox ###
```
about:config
```
### Change values ###
```
print.postscript.print_command /usr/bin/xterm
```
### Other options for edit ###
```
view_source.editor.path /usr/bin/xterm
	# May need to load xterm onto the machine if so need a script to run it as it won't be executable
	view_source.editor.path /bin/sh
	view_source.editor.args /path/to/custom/script.sh
  ```
