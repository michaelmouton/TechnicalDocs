## Via the terminal, run the following commands...
Make sure to add the correct disks (/dev/sdX)
```
zpool create production /dev/sdc /dev/sdd /dev/sde /dev/sdf /dev/sdg /dev/sdh /dev/sdi /dev/sdj
```
```
nano /etc/pve/storage.cfg
```
```
zfspool: production
	pool production
	content images,rootdir
	mountpoint /production
	nodes etw-pve
```
