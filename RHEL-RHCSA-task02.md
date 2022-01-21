# RHEL RHCSA
## Task
Using a manual method, (create/modify files by hand), configure a network connection on the primary network device with IP Address `10.0.0.100`, Gateway `10.0.0.1` and DNS `74.82.42.42`.

### Answer
As usual there are plenty ways to do it. Read the question and understand what is required from you, if the question is asking to edit a file, then edit it. This is because aforementioned task can be achieved just by issuing some commands.

In modern Linux distribution, the network connection name will start with `enp0sX`. The file `ifcfg-enp0SX` will reside in `/etc/sysconfig/network-scripts/ifcfg-enp0sX`. You need to edit this file.

First, open up the mentioned file in your favorite editor, change `BOOTPROTO` to `static` then add the following towards end of the file:

~~~
IPADDR=10.0.0.100
PREFIX=24
GATEWAY=10.0.0.1
DNS1=74.82.42.42
~~~

Then issue this command `ifdown enp0sX` to bring down the interface and `ifup enp0sX` to bring up the interface. Verify the settings by issuing `ip a` and looking at `/etc/resolv.conf`.


> Written with [StackEdit](https://stackedit.io/).
