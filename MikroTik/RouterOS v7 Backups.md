## Install the Rose package, and run the following command...
```
/disk add slot=Backup_Mount smb-address=172.17.10.11 smb-user=Administrator smb-password=P@ssword smb-share=MikroTik_Backups type=smb
```

## Then add the following script to a new schedular task
```
:local smbmountpoint "Backup_Mount"

:local identity [/system/identity get name]
:local date [/system/clock get date]
:local model [/system/routerboard get model]

:local filename ($smbmountpoint . "/" . $identity . "-" . $date . "-" . $model)

/system/backup save dont-encrypt=yes name=$filename
/export show-sensitive file=$filename
```

![Figure1.1](./Images/Figure1.1.png)
