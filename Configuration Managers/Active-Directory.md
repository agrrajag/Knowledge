---
layout: default
title: Active Directory
parent: Configuration Managers
---


# Active Directory
{: .no_toc }

Microsoft Windows Domains
{: .fs-6 .fw-300 }

## Table of contents
{: .no_toc .text-delta }

* TOC
{:toc}

---
# Domain Controllers

## Adding a Domain Controller to an Existing Domain

### Install Active Directory services
* Open Server Manager
* Select Manage
* Select Add Roles and Features
* Select Next, Next, Next
* Select Active Directory Domain Services
* Select Add Features on the box that pops up
* Select Next
* Select Next
* Continue through the deployment process, promotion will be after service installation

### Promote to Domain Controller

* Select Add a domain controller to an existing domain
* Next to domain, select Select…
* Select your domain
* Select Change next to credentials and add a domain admin
* Select next
* Select the desired site name
* Generate a strong password for DSRM. This is unique to the DC
* Select Next
* Select Next past DNS options
* Select Next on replications
* Select the desired paths for NTDS/SYSVOL
* Select Next
* Select Next
* Select Install

## Demoting a Domain Controller

[Microsoft Guide on Demoting a Domain Controller](https://docs.microsoft.com/en-us/windows-server/identity/ad-ds/deploy/demoting-domain-controllers-and-domains--level-200-) 

* Ensure services are not needing that domain controller
* Log in to the DC
* Open Server Manager
* Select manage
* Select Remove Roles and Features
* Select Next
* Select Next
* Deselect Active Directory Domain Services (and only that checkbox, a new dialog will open)
* Deselect Remove Management Tools
* Select Remove Features
* A new validation dialog will open suggesting you can demote the domain controller
* Select the words demote this domain controller
* Do not select force unless there is no network connection
  * If you do force, you will need to immediately [clean up metadata](https://docs.microsoft.com/en-us/windows-server/identity/ad-ds/deploy/ad-ds-metadata-cleanup)
* Select next
* Select Proceed with Removal

## FSMO Roles
### Roles and Descriptions
Credit to [https://www.varonis.com/blog/fsmo-roles/](https://www.varonis.com/blog/fsmo-roles/) 

#### Schema Master
The Schema Master role manages the read-write copy of your Active Directory schema. The AD Schema defines all the attributes – things like employee ID, phone number, email address, and login name – that you can apply to an object in your AD database.

#### Domain Naming Master
The Domain Naming Master makes sure that you don’t create a second domain in the same forest with the same name as another. It is the master of your domain names. Creating new domains isn’t something that happens often, so of all the roles, this one is most likely to live on the same DC with another role.

#### RID Master
The Relative ID Master assigns blocks of Security Identifiers (SID) to different DCs they can use for newly created objects. Each object in AD has an SID, and the last few digits of the SID are the Relative portion. In order to keep multiple objects from having the same SID, the RID Master grants each DC the privilege of assigning certain SIDs.

#### PDC Emulator
 The DC with the Primary Domain Controller Emulator role is the authoritative DC in the domain. The PDC Emulator responds to authentication requests, changes passwords, and manages Group Policy Objects. And the PDC Emulator tells everyone else what time it is! It’s good to be the PDC.

#### Infrastructure Master
The Infrastructure Master role translates Globally Unique Identifiers (GUID), SIDs, and Distinguished Names (DN) between domains. If you have multiple domains in your forest, the Infrastructure Master is the Babelfish that lives between them. If the Infrastructure Master doesn’t do its job correctly you will see SIDs in place of resolved names in your Access Control Lists (ACL).

![ ](https://blogvaronis2.wpengine.com/wp-content/uploads/2018/04/fsmo-roles-960x768.png)

### FSMO Placement
Multiple best-practice guides recommend leaving all FSMO roles on the first domain controller in the forest of a single-domain forest.

#### RID, PDC, IM
* Open Active Directory Users and Computers
* Right click your domain
* Select Change Domain Controller
* Select the domain controller you would like to move roles to
* Select OK
* That window will close
* Right click your domain
* Select Operations Masters
* Assign the permissions for RID, PDC, and IM to your desired DC
* If you change the PDC, you will need to update time hierarchy as well

#### Schema Master
* Open CMD as an administrator
* Run mmc.exe
* Add the Active Directory Schema module
* Right click the forest and change domain controllers to the one you want to move the operation master to
* Right click the forest and select Operations Masters
* Select Change to apply the schema master to that domain controller

#### Domain Naming Master
* Open Active Directory Domains and Trusts
* Right click Active Directory Domains and Trusts
* Select Change Domain Controller
* Change the domain controller to the one you want to move the role to
* Right click Active Directory Domains and Trusts
* Select Operations Master
* Select Change to apply the master to the new DC

## Troubleshooting

### DCDiag - userAccountControl 0x82020
This occurs when the server that you are promoting to a DC is already joined to the domain

* Open ADSI Edit
* Navigate to DC=example.com/OU=Domain Controllers and right click the DC that is showing the error
* Select Properties
* Scroll down to and double click the userAccountControl field
* Change from 532512 to 532480
* Select OK
* Select OK
