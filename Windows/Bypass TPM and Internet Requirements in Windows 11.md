# Bypass TPM and Internet Requirements in Windows 11

## Install Windows 11 on a Virtual Machine by Disabling TPM Check

1. Mount the Windows 11 .ISO and boot the Virtual Machine
2. When Windows 11 asks for the product key or installation SKU, press **SHIFT + F10**.  This brings up the command prompt
3. Add a Registry Key to disable the TPM check
```
REG ADD HKLM\SYSTEM\Setup\LabConfig /v BypassTPMCheck /t REG_DWORD /d 1
```
4. Make sure that the operation is completed successfully
![Figure1.1](./Images/Figure1.1.png)
5. Proceed with the Installation

## Bypass internet requirement during setup
* To be cleaned up one day when I feel like it...
* also screenshots to be updated
* **SHIFT + F10**
* ```OOBE\BYPASSNRO```
