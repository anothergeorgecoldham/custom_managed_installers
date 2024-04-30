# Custom Managed Installers for WDAC

A custom managed installer in the context of Application Control, such as Windows Defender Application Control (WDAC), is a designated software distribution solution that helps balance security and manageability when enforcing application control policies1.

## Here’s how it works:

**Designation:** Managed installer uses a special rule collection in AppLocker to designate binaries that are trusted by your organization as an authorized source for application installation1.

**Monitoring:** When one of these trusted binaries runs, Windows monitors the binary’s process (and any child processes it launches) and watches for files being written to disk1.

**Tagging:** As files are written, they’re tagged as originating from a managed installer1.

**Trust Configuration:** You can then configure WDAC to trust files that are installed by a managed installer by adding the “Enabled:Managed Installer” option to your WDAC policy1.

**Execution:** When that option is set, WDAC will check for managed installer origin information when determining whether or not to allow a binary to run. As long as there are no deny rules for the binary, WDAC will allow it to run based purely on its managed installer origin.

**Security Considerations:** Since managed installer is a heuristic-based mechanism, it doesn’t provide the same security guarantees as explicit allow or deny rules do1. Managed installer is best suited where users operate as standard user, and where all software is deployed and installed by a software distribution solution such as MEMCM. Users with administrator privileges, or malware running as an administrator user on the system, may be able to circumvent the intent of your WDAC policies when the managed installer option is allowed.

## Verification:
Customers using WDAC with Managed Installer (MI) enabled can use fsutil.exe to determine whether a file was created by a managed installer process2. This verification is done by querying the Extended Attributes (EAs) on a file using fsutil.exe and looking for the KERNEL.SMARTLOCKER.ORIGINCLAIM EA2.

### Example:

` fsutil.exe file queryEA C:\\Users\\Temp\\Downloads\\application.exe `

From the output, find the first row of data labeled “0000:”, which is then followed by 16 two-character sets. Every four sets form a group known as a ULONG. The two-character set at the front of the first ULONG will always be “01”. If there is “00” in the fifth position of the output (the start of the second ULONG), that indicates the EA is related to managed installer2.

Note: Some capabilities of Windows Defender Application Control are only available on specific Windows versions.

## Applying Applocker Policy using XML
You will need to apply the Applocker XML file to the systems you wish the managed installer to work on.  You can use your desktop management solution such as Intune and push the policy, use group policy in Window AD, or PowerShell.  The instructions below are for powershell so you can test it beforehand.

**Merging an Existing Policy:** To merge an existing AppLocker policy with a new one, you can use the Set-AppLockerPolicy cmdlet. This command sets the specified Group Policy Object (GPO) to contain the specified AppLocker policy. If no Lightweight Directory Access Protocol (LDAP) is specified, the local GPO is the default. Here’s an example:

```
Get-AppLockerPolicy -Local | Set-AppLockerPolicy -XMLPolicy C:\Path\To\Your\ExistingPolicy.xml -Merge
```
Replace C:\Path\To\Your\ExistingPolicy.xml with the actual path to your existing policy XML file.
This command will merge the rules from the existing policy into the local AppLocker rule set.


**Replacing the Entire Policy:** If you want to replace the entire AppLocker policy, you can create a new policy and set it using the same Set-AppLockerPolicy cmdlet. Here’s an example:
```
Set-AppLockerPolicy -XMLPolicy C:\Path\To\Your\NewPolicy.xml 
```
Replace C:\Path\To\Your\NewPolicy.xml with the path to your new policy XML file.
This command will replace the existing policy with the rules defined in the new policy.

### Test Test Test!
Remember to test your policies thoroughly before applying them to ensure they meet your security requirements. If you need to retrieve an existing AppLocker policy or generate rules based on file information, you can also use the Get-AppLockerPolicy and New-AppLockerPolicy cmdlets. Happy scripting! 🚀

**This was co-written with Bing Chat**

