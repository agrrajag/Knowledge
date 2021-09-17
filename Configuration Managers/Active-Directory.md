---
layout: default
title: Active Directory
parent: Configuration Managers
---


# Active Directory
{: .no_toc }

Microsoft Domains
{: .fs-6 .fw-300 }

## Table of contents
{: .no_toc .text-delta }

* TOC
{:toc}

---

## FSMO Roles
### Roles and Descriptions
Taken from https://www.varonis.com/blog/fsmo-roles/ 
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
