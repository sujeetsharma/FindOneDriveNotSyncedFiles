# FindOneDriveNotSyncedFiles
## PowerShell Script to show all local OneDrive files which are excluded from sync due to OneDrive file exclusion policy defined by your organization.

```
#Parameters. Default OneDrive path with below command.
$OneDrivePath = $env:OneDrive

$Files = Get-ChildItem -Path $OneDrivePath -Recurse -Force | Select FullName
$Status = @()
foreach($I in $files)
{
    $path = $I.FullName
    $shell = New-Object -COMObject Shell.Application
    $folder = Split-Path $path
    $file = Split-Path $path -Leaf
    $shellfolder = $shell.Namespace($folder)
    $shellfile = $shellfolder.ParseName($file)
    $SyncStatus = $shellfolder.GetDetailsOf($shellfile, 303)

    $obj = New-Object psobject -Property @{ "FullName" = $I.FullName; "ODBSyncStatus" = $SyncStatus}
    $Status += $obj | Select FullName, ODBSyncStatus
}
#$Status | Out-GridView
$Status | ?{$_.ODBSyncStatus -match "Not"} | Out-GridView
```

Sample Output:
```
FullName																	ODBSyncStatus        
--------																	-------------        
C:\Users\sujeetkumar\.849C9593-D756-4E56-8D6E-42412F2A707B                 Excluded (not synced)
C:\Users\sujeetkumar\desktop.ini                                           Excluded (not synced)
C:\Users\sujeetkumar\Desktop\desktop.ini                                   Excluded (not synced)
C:\Users\sujeetkumar\Documents\desktop.ini                                 Excluded (not synced)
C:\Users\sujeetkumar\Pictures\desktop.ini                                  Excluded (not synced)
C:\Users\sujeetkumar\Pictures\Camera Roll\desktop.ini                      Excluded (not synced)
C:\Users\sujeetkumar\Pictures\Screenshots\desktop.ini                      Excluded (not synced)
C:\Users\sujeetkumar\Pictures\Screenshots\Screenshot 2023-07-05 132233.png Excluded (not synced)
C:\Users\sujeetkumar\Pictures\Screenshots\Screenshot 2023-07-05 151745.png Excluded (not synced)
```
