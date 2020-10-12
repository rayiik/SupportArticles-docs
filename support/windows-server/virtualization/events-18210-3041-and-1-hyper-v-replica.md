---
title: Events 18210 and 3041 when using Hyper-V Replica
description: Describes an issue that triggers events in the Application log when you have a Hyper-V host that's running Windows Server 2012. This issue involves the Hyper-V Replica feature. The events can be safely ignored.
ms.date: 09/14/2020
author: Deland-Han
ms.author: delhan
manager: dscontentpm
audience: itpro
ms.topic: troubleshooting
ms.prod: windows-server
localization_priority: medium
ms.reviewer: kaushika
ms.prod-support-area-path: Hyper-V Replica
ms.technology: HyperV
---
# Events 18210, 3041, and 1 are logged when Hyper-V replica is configured

This article describes an issue that triggers events in the Application log when you have a Hyper-V host and the Hyper-V Replica feature is configured.

_Original product version:_ &nbsp; Windows Server 2012 R2  
_Original KB number:_ &nbsp; 3050765

## Symptoms

Consider the following scenario:

- You have a Hyper-V host that's running Windows Server 2012 R2 or Windows Server 2012.
- SQL Server is running on the virtual machine.
- You configure the Hyper-V Replica feature for the virtual machine, and a VSS snapshot (application-consistent recovery point) is enabled.

In this scenario, the following events are periodically logged in the Application log on the virtual machine:

Log Name: Application
Source: MSSQLSERVER
Event ID: 18210
Level: Error
Message: BackupVirtualDeviceFile::SendFileInfoBegin: failure on backup device '{\<GUID>}\<ID>'. Operating system error 995(The I/O operation has been aborted because of either a thread exit or an application request.)

Log Name: Application
Source: MSSQLSERVER
Event ID: 3041
Level: Error
Message: BACKUP failed to complete the command BACKUP DATABASE \<DB Name>. Check the backup application log for detailed messages.

Log Name: Application
Source: SQLVDI
Event ID: 1
Level: Error
Message: SQLVDI: Loc=TriggerAbort. Desc=invoked. ErrorCode=(0). Process=\<PID>. Thread=\<TID>. Server. Instance=\<InstanceName>. VD=Global\{\<GUID>}5_SQLVDIMemoryName_0.

> [!NOTE]
> The same errors may also occur during a genuine backup failure. In that case, you should promptly address the issue. Those errors should not be ignored except after careful review of the error message pattern in the SQL error log.

## Cause

This issue occurs because the Hyper-V Replica feature always uses the non-autorecovery  flag to create an application-consistent recovery point. In this situation, the guest writers report the failure. However, when Hyper-V Replica creates an application-consistent recovery point, it also creates a VSS snapshot, and the events are logged after the VSS snapshot is created. Therefore, these events do not indicate any backup failures, and you can safely ignore them. 

## More information

For more information, see the following TechNet blog post:

[Application consistent recovery points with Windows Server 2008/2003 guest OS](https://blogs.technet.com/b/virtualization/archive/2014/05/20/application-consistent-recovery-points-with-an-older-guest-os.aspx)