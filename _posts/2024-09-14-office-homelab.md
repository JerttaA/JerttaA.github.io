---
title: The Office Homelab
date: 2024-09-14 21:00:00 +0300
categories: [Homelab, Active Directory]
tags: [windows server, gpo, group policy management, active directory, homelab]     # TAG names should always be lowercase.
description: Write-up of My Active Directory Homelab Practice.
---

I am interested in learning about networking and system administration. I decided to build this homelab to deepen my understanding of Active Directory and its use in a corporate environment.

**Project Goals:** My goal is to learn the basic setup of Active Directory, manage users and groups, and create and manage group policies.

**Tools and Software Used:** In my homelab, I am using Windows Server 2022 as the domain controller and Windows 10 Enterprise as the client machine. Additionally, I am utilizing VMware Workstation Pro for managing virtual machines.

**Environment Description:** My virtual environment consists of one domain controller and one client machine, both connected to the same virtual network. The domain controller is responsible for managing users and resources, while the client machine serves as a test platform.


## First steps
After installing Active Directory with Server Manager on the Windows Server machine and promoting it to a domain controller, I created a root domain named `TestiDomain.local` for my homelab project. I then began structuring the organization in Active Directory Users and Computers under `TestiDomain.local.` I created organizational units for the USA office, Computers, Service Accounts, and Users.

![img-description](/assets/img/Screenshot-2024-09-14-093055.png)
_Active Directory Users and Computers: TestiDomain.local created USA office_

When I had created all my users, I made security and distribution groups for different departments. I added a `#` at the beginning of the group names to make it easier to search for the groups.

![img-description](/assets/img/Screenshot-2024-09-14-095044.png)
_Creating a new group: All employees mailing list_

![img-description](/assets/img/Screenshot-2024-09-14-100326.png)
_Adding all department groups to the mailin list group_

## Joining the Client PC to the Domain
To join the client PC to the domain, I first needed to configure the network settings of the server. I set the server’s IP address to static and configure its DNS to loopback to Google’s DNS. I configured the server's DNS to use Google's public DNS (8.8.8.8) for external name resolution, ensuring reliable DNS resolution for internet-based services. 

Then, on the client PC, I changed its DNS settings to point to the domain controller’s IP address and joined the computer to the domain. I kept the client’s IP address set to automatic.

![img-description](/assets/img/Screenshot-2024-09-14-101930.png)
_Setting domain controller a static IP_

![img-description](/assets/img/Screenshot-2024-09-14-102459.png)
_Pointing client to the domain controller_

Next, I tested the connection between the client and the server. I pinged the server from the client and use `nslookup` to verify the existence of testidomain.local.

![img-description](/assets/img/Screenshot-2024-09-14-102730.png)
_Client Command Prompt: ping and nslookup_

Next, I renamed the client machine to `COMP01` and added it to the `testidomain.local` domain. Then, I tested the domain addition by logging into the client machine with the IT user `ja` account.

![img-description](/assets/img/Screenshot-2024-09-14-102946.png)
_Adding COMP01 to the domain_

![img-description](/assets/img/Screenshot-2024-09-14-104840.png)
_Signing in to the domain_

## Configuring Group Policy Objects (GPO)
Now I will configure some Group Policy Objects (GPO) and test how they work.

First, I created a policy to map a shared folder as a network drive for members of the Sales department group. This involved setting up a shared folder on the server and then using Group Policy Management to create a GPO that maps this shared folder as a network drive for all users in the Sales group.

![img-description](/assets/img/Screenshot-2024-09-14-112029.png)
_Creating SHAREFOLDER and configuring it's security permissions_
![img-description](/assets/img/Screenshot-2024-09-14-114033.png)
_Mapped drives policy for the sales department_

I logged in as the `jhalpert` user (member of the sales department) and ran the `gpupdate` command to refresh the Group Policy settings. The `gpupdate` command forces an immediate update of Group Policy settings on the client machine. After running the command, I verified that the shared folder was correctly mapped as a network drive.

![img-description](/assets/img/Screenshot-2024-09-14-114339.png)
_running gpupdate command_
![img-description](/assets/img/Screenshot-2024-09-14-114432.png)
_Verified mapped network drive_

Next, I created a policy to restrict user access to the Control Panel. This involved creating a new GPO that disables access to the Control Panel and applying it to the relevant organizational units. Of course, I also tested that the `jhalpert` user couldn’t access the Control Panel.

![img-description](/assets/img/Screenshot-2024-09-14-163057.png)
_Prohibit access to Control PAnel and PC settings -Policy settings_
![img-description](/assets/img/Screenshot-2024-09-14-164236.png)
_Policy added to the wanted user groups_

Finally, I checked the `jhalpert` user’s account by running the `gpresult /r` command. This command displays the Resultant Set of Policy (RSoP) information for the user and computer. It shows which Group Policy Objects (GPOs) are applied to the user and computer, as well as the user’s group memberships. I verified that the policies I created were listed and correctly applied.

![img-description](/assets/img/Screenshot-2024-09-14-164606.png)
_Running gpresult /r command_

## Conclusion
This homelab project has been an invaluable learning experience. By setting up a virtual environment with a Windows Server 2022 domain controller and a Windows 10 Enterprise client, I have gained practical knowledge of Active Directory. I successfully created a root domain, structured organizational units, and managed users and groups.

Configuring Group Policy Objects (GPOs) allowed me to enforce specific settings across the network, such as mapping a shared folder for the Sales group and restricting access to the Control Panel. Testing these policies by logging in as different users and using commands like `gpupdate` and `gpresult /r` confirmed that the policies were correctly applied.

Overall, this project has enhanced my understanding of network management and system administration, providing a solid foundation for future endeavors in IT.

