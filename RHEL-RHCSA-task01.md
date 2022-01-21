
# RHEL RHCSA

## Task
Assuming the root password is lost, and your system is running in multi-user target with no current root session open. Reboot the system into an appropriate target, and reset the root user password to your choice of password.

### Answer
-   Interrupt the boot process in order to gain access to a system
-- Answer 1:
	- reboot the server to console
	- edit grub, find the line that starts with `linux` include `rd.break` at the end and boot the machine
	- chroot `chroot /sysroot`
	- mount the filesystem `mount -o remount,rw /`
	- change the password
	- `touch .autorelabel`
	- reboot the system

-- Answer 2:
 - edit grub, look for `ro`, replace it with `rw init=sysroot/bin/sh`
	- `chroot sysroot`
	- `passwd`
	- `touch .autorelabel`
	- reboot

> Written with [StackEdit](https://stackedit.io/).
