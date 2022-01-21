# RHEL RHCSA

## Task
Set `SELinux` to permissive mode

### Answer

-   Set enforcing and permissive modes for SELinux
- - Answer: edit `/etc/selinux/config`, under`SELINUX` insert `PERMISSIVE` or `ENFORCING`. To disable it, put in `DISABLED`. Reboot the system. To enable/disable `SELinux`, you can use `setenforce <BOOLEAN_VALUE>`. 


> Written with [StackEdit](https://stackedit.io/).
