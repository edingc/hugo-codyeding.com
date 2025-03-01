+++
Categories = ["Quick Notes"]
Description = "Recursively add permissions to a Public Folder tree in Exchange Online using Windows PowerShell."
Tags = ["Office 365", "Exchange", "PowerShell"]
date = "2016-05-05T16:32:34-06:00"
title = "Add permission to Public Folder recursively with PowerShell"

+++

We had a request to add permissions for a customer throughout a deeply nested structure in our Exchange Online Public Folders.

These commands **will not** override or change permissions where they are already set.

### Connect to Exchange Online PowerShell

	$Cred = Get-Credential
	Connect-MSOLService -Credential $Cred
	$Session = New-PSSession -ConfigurationName Microsoft.Exchange -ConnectionUri https://ps.outlook.com/powershell -Credential $Cred -Authentication Basic -AllowRedirection
	Import-PSSession $Session

### Add Permissions and Verify Success

In PowerShell, run:

    Get-PublicFolder -Identity "\Folder Name" -Recurse | Add-PublicFolderClientPermission -User jsmith -AccessRights Owner
	
Verify the change was successful:

    Get-PublicFolder -Identity "\Folder Name" -Recurse | Get-PublicFolderClientPermission | Where-Object { $PSItem.User -like "SMITH*" }
	
Use the customer's name in the *Where-Object* cmdlet. All of our accounts are named in a "LASTNAME, FIRSTNAME" format, and the command reflects that. This command will print all of the customer's rights through the tree.