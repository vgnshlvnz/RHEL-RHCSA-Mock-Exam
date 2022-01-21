# RHEL RHCSA

## Task
Create a logical volume call lvol1 of size 300MB in vgtest volume group. Mount the ext4 filesystem persistently to `/mnt/mnt1`

### Answer
For this question, its better to explore LVM and understand it in depth, so that it will make your life easier when doing this task. 

We are going to do 3 things:
- initialize physical volume (PV)
- initialize volume group (VG)
- initialize logical volume (LV)

#### Initialize physical volume (PV)
Assuming we have a 4 disks of 250MB, should be enough to initialize PV using at most 2 disks.
- First check if any PV is available, there should be as most modern Linux distribution rely on LVM. The command is:
  - `pvs`
Next you need to determine the disks in your system. This can be checked by following command:
- `lsblk`
For the sake of this write-up I have 2 250MB disks in my system. I am going to use them to initialize PV. 

**Note** You can choose to label your disk as `MBR` or `GPT` or add raw disks into physical volume (PV).

The command to create PV disks is following:
- `pvcreate /dev/sdX /dev/sdY`

Next, we need to create a volume group called `vgtest`. Lets create them, type in following command:
- `vgcreate -vs 4 vgtest /dev/sdX /dev/sdY` - **note** : You are expected to know what is PE and LE. 

To verify that you have created volume group `vgtest`: 
- `vgs vgtest`

Next we are going to create logical volume of 300MB, use the following command. Remember we set PE size of 4MB, therefore there will be two ways of creating them, one by specifying the size, one by specifying the total PE.

First command, specifying size and the LV name by passing `-n` argument :
- `lvcreate -vL 300 -n lvol1 vgtest`

Second command, will specify the total PE to create a logical volume, before that run `vgdisplay` to check the PE size, the formula to calculate is how many PE is required for 300MB where a PE is 4MB, so divide 300 by 4 and you will get 75 PEs:
- `lvcreate -vl 75 -n lvol1 vgtest`

Thirdly you need to format the logical volume with `ext4` filesystem, run following command:
- `mkfs.ext4 /dev/vgtest/lvol1`

To verify that `ext4` is applied, run:
- `lsblk -f` - It will show you that the two pv are created with `ext4` filesystem.

Finally we are going to add an entry to `/etc/fstab` to automount the LV to `/mnt/mnt1`. Be sure the directory exist first.

We will use the UUID of the LV created so that it will be easy to mount. Following must be present in `/etc/fstab`. UUID can be obtained by typing `blkid`.

~~~
UUID=<UUID_OF_THE_LV> /mnt/mnt1 ext4 defaults 0 0
~~~

Now run `mount -a` you should not see error. 


##### Additional Notes
Configure local storage

-   List, create, delete partitions on MBR and GPT disks
	- check the disklabel type if it is MBR or GPT disk 
 -- Answer 1:
	    - `blkid` - will get you UUID of attached disks
	    - `lsblk` - will show you attached disks and block devices
	    - `partprobe` - will show you partition changes
	    - `fdisk -l` - list of disks and partition
	    - `fdisk /dev/sdX` - start fdisk with physical disk
	    -  `o` - create new DOS partition table
	    - `g` - create new GPT partition table
	    - `p` - print the partition table
	    - `n` - create new partition
	    - `e` - for logical partition
	    - `w` - write partition
	    - `d` - delete partition
	    - `l` - list file system types
	    - `t` - change partition type
	    - `gdisk /dev/sdX` - start the disk with gpt-disk program
	    - `n` - new partition
	    - `L` - list file system types
	    - `w` - write changes
	    - `gdisk -l /dev/sdX` - show information
-- Answer 2:
	- Using a combination of parted and fdisk
	    - `parted /dev/sdX print` - take note of the error, the disk label is not set
	    - `parted /dev/sdX mklabel msdos/gpt` - set disklabel
	     - `parted /dev/sdX mkpart primary/extended 1 101m` - create a 100mb primary partition with position starting from 1MB (beginning of the disk)
	     - `parted /dev/sdX rm 1` - removes partition 1
-- GPT disk - gdisk procedure
         - `gdisk /dev/sdX` - start `gdisk` with a empty disk or MBR disk
         - `o` - assign `gpt` partition table type 
         - `p` - verify `GUID` partition table creation
         - `n` - create partition of selected size with default type "Linux filesystem"
         - `w` - write the changes
         - `d1` - delete partition number 1, use `d` if you have multiple partitions
- Virtual Data Optimiser (VDO) steps

-- Answer:
         - `dnf install -y vdo kmod-kvdo` - install VDO and it's kernel module
         - `systemctl --now enable vdo` - start and enable VDO
         - `systemctl status vdo` - check if the service is running
         - `vdo create --name vdo-vol1 --device /dev/sdX --vdoLogicalSize 16G --vdoSlabSize 128` - create a vdo volume with logical size and slab size (slab is the size of the increment by which vdo volumes grow)
         - `vdo list` - list the new volume
         - `lsblk` - use `lsblk` to list the vdo volume
         - `vdostats --hu` - usage status of the volumes
         - `vdostats --verbose` - show detailed statistics for the volume including configuration option
         - `vdo status --name vdo-vol` - show detailed statistics for the volume including configuration options
         - `vdo status --name vdo-vol | grep -i compression` - shows if compression is enabled
         - `vdo status --name vdo-vol1 | grep -i deduplication` - shows if deduplication is enabled
         - `vdo remove --name vdo-vol1` - removes the volume
-   Create and remove physical volumes
 -- Answer:
         - `parted /dev/sdX mklabel msdos/gpt` - make partition table
         - `parted /dev/sdX mkpart primary <START> <END>m` - create partition
         - `parted /dev/sdX set 1 lvm on` - set `lvm` flag on
         - `pvcreate /dev/sdX<PARTITION_NUM> -v` - create new physical volume, can be multiple devices
         - `pvs/pvdisplay` - view physical volumes
         - `pvremove /dev/sdX<PARTITION_NUM>` - remove physical volume, can be multiple devices
         - `parted /dev/sdX rm <PARTITION_NUM>` - remove partition
-   Assign physical volumes to volume groups
 -- Answer:
         - `vgcreate -v -s <PE_SIZE_IN_MB> <VOLUME_GROUP_NAME> /dev/sdX<PARTITION_NUM>` - create volume group, assign PE size and add physical volumes to the volume group
         - `vgs/vgdisplay` - view volume groups
-   Create and delete logical volumes
 -- Answer:
          - `lvcreate -vL <SIZE> <VOLUME_GROUP>` - create a logical volume with desired size from selected volume group - `-L` is to specify in size in MB, GB
          - `lvcreate -l <TOTAL_LE/PE> -n <CUSTOME_LV_NAME> <VOLUME_GROUP>` - create a logical volume with LE and a custom name
          - `lvremove /dev/<VG>/<LV> -f` - Will remove the logical volumes
-   Configure systems to mount file systems at boot by universally unique ID (UUID) or label
-   Add new partitions and logical volumes, and swap to a system non-destructively


> Written with [StackEdit](https://stackedit.io/).
