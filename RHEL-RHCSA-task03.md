# RHEL RHCSA
## Task
Using a manual method (modify the file by hand), set the system hostname to `rhcsa1.example.com` and alias rhcsa1. Make sure the new hostname is reflected in the command prompt.

### Answer
There are few ways to get this done. We will go for 2 ways, one is setting the hostname in `/etc/hostname` and then setting the alias in `/etc/hosts`.

Edit `/etc/hostname` and add `rhcsa1.example.com` and save the file. Restart `systemd-hostnamed` service by issuing: `systemctl restart systemd-hostnamed`. Then edit `/etc/hosts` file. Add following entry in the file,
~~~
192.168.1.100	rhcsa1.example.com rhcsa1
~~~
Save the file. Log out of current session, and login, you should see the alias and if you execute `hostnamectl get-hostname`, it will show you the correct hostname.


> Written with [StackEdit](https://stackedit.io/).
