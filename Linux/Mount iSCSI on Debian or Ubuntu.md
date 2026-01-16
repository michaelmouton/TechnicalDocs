# How to mount iSCSI connection on a Debian or Ubuntu Server
In this example the iSCSI Target is hosted from a QNAP NAS.

### Optional #1: Ensure you have Open-iSCSI installed
This will work on any Debian-based OS...
```
sudo apt update
sudo apt install open-iscsi
```

### Optional #2: Find your iSCSI initiator to lock the target on the QNAP
```
sudo cat /etc/iscsi/initiatorname.iscsi
```
The output will look something like this...
```
InitiatorName=iqn.2025-03.com.example:target1
```
Only copy ```iqn.2025-03.com.example:target1```, ignore the rest

### Optional #3: add CHAP credentials for logging in
If you want to use CHAP authentication, make the following changes...
```
sudo nano /etc/iscsi/iscsid.conf

node.session.auth.authmethod = CHAP
node.session.auth.username = myusername
node.session.auth.password = mypassword
```

### Step 1: # Discover Available iSCSI Targets
Replace <TARGET-IP> with the iSCSI server's IP.
```
sudo iscsiadm -m discovery -t sendtargets -p <TARGET-IP>
```

### Step 2: # Log in to the iSCSI Target
```
sudo iscsiadm -m node -T <TARGET-NAME> -p <TARGET-IP> --login
```
For example...
```
sudo iscsiadm -m node -T iqn.2025-03.com.example:target1 -p 192.168.1.100 --login
```


### Step 3: # Verify the Disk is Detected
```
lsblk
```

### Step 4: # Format and prepare the disk for mount
If the disk is new, format it (e.g., as ext4):
Replace sdX with your actual device name
```
sudo mkfs.ext4 /dev/sdX  
```
Create a mount point:
```
sudo mkdir /mnt/iscsi_disk
```

### Step 5: # Enable automatic login
```
sudo iscsiadm -m node -T <TARGET-NAME> -p <TARGET-IP> --op update -n node.startup -v automatic
```
Check:
```
sudo nano /etc/iscsi/iscsid.conf

node.startup = automatic
```
In Nano use CTRL+W to search and CTRL+S to save

### Step 6: Enable automatic drive mount
```
sudo nano /etc/fstab
```
iSCSI Mount
```
/dev/sdX /mnt/iscsi_disk ext4 _netdev 0 0
```
Check:
```
sudo mount -a
sudo reboot
```

### Step 7: # Steps you can take when expanding the iSCSI LUN...
Completely optional; only to be used when the LUN has already been expanded on the iSCSI target server.
```
sudo umount /mnt/iscsi_disk
sudo e2fsck -f /dev/sdX
sudo resize2fs /dev/sdX
sudo mount -a
```
