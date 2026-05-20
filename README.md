# sysmon-config-ot | An Sysmon configuration file for use in OT #

This is a Microsoft Sysinternals Sysmon configuration file template with default high-quality event tracing for use in Operational Technology (OT) environments. 
The configuration file is forked from the legendary [SwiftOnSecurity's](https://github.com/Swiftonsecurity/sysmon-config) original Sysmon Config, updated with [Olaf Hatong's](https://github.com/cyb3rpeace/sysmon-modular-olafhartong-) Sysmon config modules, and then tuned for use in Operational Technology environments.

What is Operational Technology? It's a lot of things, but can be classed as Industrial Control Systems (ICS), safety systems, control systems, or any technology that directly controls or impacts the physical world, and their supporting systems. 
Operational Technology cybersecurity is focused more on Availability and Integrity of systems, with less value placed on Confidentiality. Importantly, because OT/ICS control or interact with the real world, systems need to be highly available, reactive, and have minimal downtime.
It also means the threats to OT/ICS systems can be different to traditional IT systems.
The prevalence of IT equipment (referred to as Commercial Off the Shelf equipment) means that we often need a combination of IT-type defenses with OT-specific defences. Thus, this config was created.

This config file takes the aims of the original file, and improves it for use in OT deployments. Some OT-specific sections have been added, and some exclusions have been tweaked to give better visibility.
A great example is - typically, Microsoft Office products are not installed in OT systems. This means we want to detect if it does become installed and in use.

Where possible, I have used the sysmon-modular components to extend the new Sysmon capabilities available in v15. Note this file will not work with earlier versions, but v15 and higher are stable and highly recommended for use.

When used in Windows Domain environments, I highly recommend using a PowerShell script run as a scheduled task to deploy in a Group Policy Object to automate deployment and also ensure it stays running. Ansible can also be used for this.

This configuration file is used in high density, secure Operational Technology environments today and can be used as is. HOWEVER! Ensure you test this in your environment, understand what it is doing and change as you need. 
This file can and should be used as part of a logging plan to capture events on device and be managed by a SIEM and SOC. Not every event generated should cause alarm, but can be used for correlation during investigation.

It is recommended to increase the size of your Windows Event Viewer log for Sysmon to retain events. Guidance on sizing is plentiful (for example, the Australian Cyber Security Centre recommend 2GB for security logs), however guidance on how to do this automatically for the Sysmon event channel is minimal. Thankfully, I've worked that out for you.

The Sysmon logging channel in Windows is created on installation. Best practice is to increase the size of this log in order to preserve local events, however this is not well documented. A 2GB maximum log size can be configured as follows:
  
Key path: HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\WINEVT\Channels\Microsoft-Windows-Sysmon/Operational
Value (DWORD): MaxSize
Value data: HEX: 0x80000000, Decimal: 2147483648
  
For Active Directory environments, use Group Policy to set this across the environment where possible. Adjust the decimal value (bytes) as required.

TO DO:

- Validate the Sysmon configuration works in Linux environments.
- Publish an example PowerShell script for deployment

SwiftOnSecurity's original notes:

The file should function as a great starting point for system change monitoring in a self-contained and accessible package. This configuration and results should give you a good idea of what's possible for Sysmon. Note that this does not track things like authentication and other Windows events that are also vital for incident investigation.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**[sysmonconfig-export.xml](https://github.com/SwiftOnSecurity/sysmon-config/blob/master/sysmonconfig-export.xml)**

Because virtually every line is commented and sections are marked with explanations, it should also function as a tutorial for Sysmon and a guide to critical monitoring areas in Windows systems.

- For a far more exhaustive and detailed approach to Sysmon configuration from a different approach, see also **[sysmon-modular](https://github.com/olafhartong/sysmon-modular)** by [@olafhartong](https://twitter.com/olafhartong), which can act as a superset of sysmon-config.

- Sysmon is a compliment to native Windows logging abilities, not a replacement for it. For valuable advice on these configurations, see **[MalwareArchaeology Logging Cheat Sheets](https://www.malwarearchaeology.com/cheat-sheets)** by [@HackerHurricane](https://twitter.com/hackerhurricane).

Note: Exact syntax and filtering choices in the configuration are highly deliberate in what they target, and to have as little performance impact as possible. Sysmon's filtering abilities are different than the built-in Windows auditing features, so often a different approach is taken than the normal static listing of paths. 

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**[See other forks of this configuration](https://github.com/SwiftOnSecurity/sysmon-config/network)**

## Use ##
### Install ###
Run with administrator rights
~~~~
sysmon.exe -accepteula -i sysmonconfig-export.xml
~~~~

### Update existing configuration ###
Run with administrator rights
~~~~
sysmon.exe -c sysmonconfig-export.xml
~~~~

### Uninstall ###
Run with administrator rights
~~~~
sysmon.exe -u
~~~~

## Required actions ##

### Prerequisites ###
Highly recommend using [Notepad++](https://notepad-plus-plus.org/) to edit this configuration. It understands UNIX newline format and does XML syntax highlighting, which makes this very understandable. I do not recommend using the built-in Notepad.exe.

### Customization ###
You will need to install and observe the results of the configuration in your own environment before deploying it widely. For example, you will need to exclude actions of your antivirus, which will otherwise likely fill up your logs with useless information.

The configuration is highly commented and designed to be self-explanatory to assist you in this customization to your environment.

### Design notes ###
This configuration expects software to be installed system-wide and NOT in the C:\Users folder. Various pieces of software install themselves in User directories, which are subject to extra monitoring. Where possible, you should install the system-wide version of these pieces of software, like Chrome. See the configuration file for more instructions.
