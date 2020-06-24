# Recovery And IDentification #

**Category:**	Miscellaneous  
**Points:**	150

**Prompt:** 
* Given two out of three disk images used in a RAID array, see if you can recover the data: [disk1.img.xz](./disk1.img.xz) [disk2.img.xz](./disk2.img.xz)

**Hints:** 
* [Creating your own RAID array](./https://unix.stackexchange.com/questions/302766/persistent-use-of-loop-block-device-in-mdadm) would be a good place to start.
* Try to [use your existing array to assemble a RAID array with the given disk images](https://superuser.com/questions/962395/assemble-3-drive-software-raid5-with-one-disk-missing).
* The flag will be in one of the recovered files

**Solution:**
* Follow the example at the second link to set up a raid array under 'full example'
```
truncate -s 1G 1.img
truncate -s 1G 2.img
truncate -s 1G 3.img
sudo mdadm --create --verbose /dev/md0 --level=5 --raid-devices=3 /dev/loop{3,4,5}
mdadm --stop /dev/md0
losetup -d /dev/loop3
cp ../disk1.img 2.img 
cp ../disk2.img 3.img
```
* View status of raid setups
```
cat /proc/mdstat
```
* View status of loopbacks
```
losetup -a
```
* Examine a loopback
```
mdadm --examine /dev/loop4
```
* Stop a raid array
```
mdadm --stop /dev/md126
```
* Okay.  Now that we are familiar with some RAID related commands.  Do the following.
```
cp ../disk1.img ./1.img
cp ../disk2.img ./2.img
truncate -s 1G 3.img
losetup loop1 1.img
losetup loop2 2.img
losetup loop3 3.img
cat /proc/mdstat
```
* The loop1 and loop2 drives auto started an array.  We need to stop it.
```
mdadm --stop /dev/md127
mdadm --create --verbose /dev/md0 --level=5 --raid-devices=3 /dev/loop{1,2,3}
```
* The new array has restored itself over 3 disks. Now lets mount the raid array and extract the files
```
mkdir -p /mnt/md0
mount /dev/md0 mnt/md0
cp /mnt/md0/images/* ./
```
* cat all the files
* ACI{350deee867129eb795468a89e2a}
