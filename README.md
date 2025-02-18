# BitLocker Encryption Tool

## Overview
The **BitLocker Encryption Tool** is a PowerShell script designed to **automate the encryption of non-system drives** using **BitLocker**. This tool ensures that all data drives except the system drive (`C:`) are encrypted for enhanced security.

## How It Works
1. **Identifies all non-system drives**
   - Retrieves all drives except the system drive (`C:`).
2. **Checks BitLocker encryption status**
   - Determines whether each drive is fully encrypted.
3. **Enables BitLocker for unencrypted drives**
   - Uses **XtsAes256 encryption method** for strong security.
   - Protects the drive with a **recovery password**.
4. **Enables auto-unlock for encrypted drives**
   - Ensures that the drive automatically unlocks when the system starts.
5. **Outputs encryption status**
   - Provides real-time feedback on encryption progress.

## PowerShell Script
```powershell
$drives = Get-PSDrive -PSProvider FileSystem | Where-Object { $_.Name -ne 'C' }
$allEncrypted = $true

foreach ($drive in $drives) {
    $driveLetter = $drive.Name + ":"
    Write-Host "Checking BitLocker status on drive $driveLetter"

    # Check if the drive is encrypted
    $bitLockerStatus = Get-BitLockerVolume -MountPoint $driveLetter
    if ($bitLockerStatus.VolumeStatus -ne 'FullyEncrypted') {
        $allEncrypted = $false
        Write-Host "Drive $driveLetter is not fully encrypted. Enabling BitLocker."

        # Enable BitLocker with recovery password protector
        Enable-BitLocker -MountPoint $driveLetter -EncryptionMethod XtsAes256 -RecoveryPasswordProtector

        # Enable auto-unlock for the drive
        Enable-BitLockerAutoUnlock -MountPoint $driveLetter
    } else {
        Write-Host "Drive $driveLetter is already fully encrypted."
    }
}

# Check if all drives are encrypted
if ($allEncrypted) {
    Write-Host "All data drives are encrypted."
} else {
    Write-Host "Started encryption on some drives. Please wait for the process to complete."
}
```

## Usage
1. **Run the script in PowerShell** with administrative privileges.
2. **Monitor the output**, which will display the encryption status for each drive.
3. **If a drive is not encrypted**, the script will automatically enable BitLocker encryption.
4. **Auto-unlock is enabled** for seamless access upon system startup.

## Example Output
```
Checking BitLocker status on drive D:
Drive D:\ is not fully encrypted. Enabling BitLocker.
Checking BitLocker status on drive E:
Drive E:\ is already fully encrypted.
Started encryption on some drives. Please wait for the process to complete.
```

## Why Use This Tool?
✅ **Ensures all non-system drives are encrypted** for maximum security.  
✅ **Automates BitLocker deployment**, reducing manual configuration.  
✅ **Uses industry-standard AES256 encryption** for data protection.  
✅ **Enables auto-unlock**, making access seamless while maintaining security.  

---
This script provides an efficient way to enforce **BitLocker encryption** on all non-system drives, ensuring **data security** in both enterprise and personal environments.
