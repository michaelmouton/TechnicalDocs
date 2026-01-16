# Expanding your C (boot) Drive in Windows 10 or 11

## TL;DR Plan
* Disable Windows Recovery Environment
* Delete the Recovery Partition
* Resize your other partitions
* Create a new Recovery Partition at the end
* Copy WinRE files
* Re-enable Windows Recovery

## Let's start!

### Step 1: Disable Windows RE
Open Command Prompt with administrative rights, then disable the recovery partition. This unmounts WinRE so the recovery partition can safely be deleted
```
reagentc /disable
```

### Step 2: Identify and Delete Recovery Partition
Run the diskpart tool, select your main disk, select the recovery partition (should be the last one) and delete it
* Replace Y with your boot disk, typically 0
* Replace X with your recovery partition
```
diskpart
list disk
select disk Y
list partition
select partition X
delete partition override
exit
```

### Step 3: Expand C: or Other Partition
Open Disk Management (Win+R, diskmgmt.msc) to expand the C partition until exactly 1024MB is left over

### Step 4: Create a New Recovery Partition
Following the expansion, you can go back to Command Prompt and re-create the recovery partition
We will temporarily assign to it the drive letter R, which is used to copy the WinRE WIM file
```
diskpart
select disk Y
create partition primary size=1024
list partition
select partition X
format quick fs=ntfs label="Recovery"
assign letter=R
exit
```

### Step 5: Copy WinRE.wim to New Partition
```
mkdir R:\Recovery\WindowsRE
xcopy /h C:\Windows\System32\Recovery\Winre.wim R:\Recovery\WindowsRE\
```
Note: if ```Winre.wim``` is missing from ```C:\Windows\System32\Recovery```, it may be in ```C:\Recovery\WindowsRE```

### Step 6: Register New Recovery Partition and Enable RE
```
reagentc /setreimage /path R:\Recovery\WindowsRE
reagentc /enable
```

For a quick sanity check, we need to confirm it is registered correctly
```
reagentc /info
```

Running the command above should give you an output similar to the below...
```
Windows RE location: \\?\GLOBALROOT\device\harddiskY\partitionX\Recovery\WindowsRE
Windows RE status: Enabled
```

### Step 7: Remove Drive Letter
```
diskpart
select volume R
remove letter=R
exit
```

### Important Notes
The above approach is safe if you:
* Make a backup
* Don’t interrupt file copy steps
* Validate with reagentc /info at the end
