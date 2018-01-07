
**Last document update**: January 6, 3:00 PM PST.

The recent disclosure of a [new class of CPU vulnerabilities](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002) known as speculative execution side-channel attacks has resulted in questions from customers seeking more clarity.  

The infrastructure that runs Azure and isolates customer workloads from each other is protected.  This means that other customers running on Azure cannot attack your application using these vulnerabilities.

## Keeping your Operating Systems up-to-date

While an OS update is not required to isolate your applications running on Azure from other customers running on Azure, it is always a best practice to keep your OS versions up-to-date. 

In the following offerings, here are our recommended actions to update your Operating System: 

|Offering	| Recommended Action |
|-----------|--------------------|
| Azure Cloud Services	| Enable auto update or ensure you are running the newest Guest OS. |
| Azure Linux Virtual Machines | Install updates from your operating system provider when available. |
| Azure Windows Virtual Machines	| - Verify that you are running a supported antivirus application before you install OS updates. Contact your antivirus software vendor for compatibility information. 
| | - Install the [January security rollup](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002). |
| Other Azure PaaS Services	| There is no action needed for customers using these services. Azure automatically keeps your OS versions up-to-date. |

## Additional guidance if you are running untrusted code 

No additional customer action is needed unless you are running untrusted code. If you allow code that you do not trust (for example, you allow one of your customers to upload a binary or code-snippet that you then execute in the cloud within your application), then the following additional steps should be taken.  


### Windows 
If you are using Windows and hosting untrusted code, you should also enable a Windows feature called Kernel Virtual Address (KVA) Shadowing which provides additional protection against speculative execution side-channel vulnerabilities. This feature is turned off by default and may impact performance if enabled. 
Follow [Windows Server KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) instructions for Enabling Protections on the Server. If you are running Azure Cloud Services, verify that you are running WA-GUEST-OS-5.15_201801-01 or WA-GUEST-OS-4.50_201801-01 (available starting on January 10th) and enable the registry key via a startup task.


### Linux
If you are using Linux and hosting untrusted code, you should also update Linux to a more recent version that implements kernel page-table isolation (KPTI) which separates the page tables used by the kernel from those belonging to user space. These mitigations require a Linux OS update and can be obtained from your distribution provider when available. Your OS provider can tell you whether protections are enabled or disabled by default.








## Next steps

To learn more, see [Securing Azure customers from CPU vulnerability](https://azure.microsoft.com/blog/securing-azure-customers-from-cpu-vulnerability/).