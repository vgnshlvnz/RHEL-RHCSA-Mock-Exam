# RHEL RHCSA
## Task
Attach the RHEL 8 ISO image to the VM and mount it persistently to `/mnt/cdrom`. Define access to both repositories and confirm.

### Answer
You need to mount the iso. This can be done by:
- `mount -o ro /dev/sr0 /mnt/cdrom`
However this is not persistent. To make it persistent, you need to add an entry in `/etc/fstab` . Add the following entry:
- `/dev/sr0 /mnt/cdrom iso9660 ro 0 0`.

To verify, type `mount -a`. You should see it mounted at `/mnt/cdrom`. 

Next, create `local.repo` under `/etc/yum.repos.d/`.

Enter the following in the file:
~~~
[BaseOS]
name=BaseOS
baseurl=file:///mnt/cdrom/BaseOS
gpgcheck=0
enabled=1

[AppStream]
name=AppStream
baseurl=file:///mnt/cdrom/AppStream
gpgcheck=0
enabled=1
~~~

Finally run `yum repolist` or `dnf repolist` and run `yum update` or `dnf update`. You should be able to see that the program updates the local repo we have created.

> Written with [StackEdit](https://stackedit.io/).
