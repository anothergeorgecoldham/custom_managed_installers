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

**This was written with Bing Chat**

