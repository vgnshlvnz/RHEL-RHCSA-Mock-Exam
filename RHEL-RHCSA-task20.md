# RHEL RHCSA

## Task
Configure local user to use sudo without being prompted for their password

### Answer
Switch to root user, execute `visudo` command.You will get something like following:
~~~
root    ALL=(ALL)       ALL

## Allows members of the 'sys' group to run networking, software,
## service management apps and more.
# %sys ALL = NETWORKING, SOFTWARE, SERVICES, STORAGE, DELEGATING, PROCESSES, LOCATE, DRIVERS

## Allows people in group wheel to run all commands
%wheel  ALL=(ALL)       NOPASSWD:ALL
~~~
After `root` entry add following line,

~~~
<USERNAME>	ALL=(ALL)	NOPASSWD:ALL
~~~
Save and close the file. Log out of root and execute `sudo <USERNAME>` and it will no longer prompt for a password.


> Written with [StackEdit](https://stackedit.io/).
