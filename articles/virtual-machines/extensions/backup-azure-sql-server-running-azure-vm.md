---
title: Azure Backup för SQL Server som körs i Azure VM
description: I den här artikeln får du lära dig hur du registrerar Azure Backup i SQL Server som körs i en virtuell Azure-dator.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: dcurwin
ms.author: dacurwin
ms.collection: windows
ms.date: 07/05/2019
ms.openlocfilehash: 46cc4b23ca7dd70da3f917a3399406480dc9fdbd
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102557105"
---
# <a name="azure-backup-for-sql-server-running-in-azure-vm"></a>Azure Backup för SQL Server som körs i Azure VM

Azure Backup, bland annat erbjudanden, ger stöd för säkerhets kopiering av arbets belastningar som SQL Server som körs i virtuella Azure-datorer. Eftersom SQL-programmet körs i en virtuell Azure-dator måste säkerhets kopierings tjänsten ha behörighet att komma åt programmet och hämta nödvändig information.
För att göra det, Azure Backup installerar tillägget **AzureBackupWindowsWorkload** på den virtuella datorn, där SQL Server körs, under registrerings processen som utlöses av användaren.

## <a name="prerequisites"></a>Förutsättningar

En lista över scenarier som stöds finns i [support mat ris](../../backup/sql-support-matrix.md#scenario-support) som stöds av Azure Backup.

## <a name="network-connectivity"></a>Nätverksanslutningar

Azure Backup stöder NSG-taggar, distribution av en proxyserver eller listade IP-intervall; Mer information om var och en av metoderna finns i den här [artikeln](../../backup/backup-sql-server-database-azure-vms.md#establish-network-connectivity).

## <a name="extension-schema"></a>Tilläggsschema

Tilläggs schemats och egenskaps värden är konfigurations värden (körnings inställningar) som tjänsten skickar till CRP-API: et. Dessa konfigurations värden används vid registrering och uppgradering. **AzureBackupWindowsWorkload** -tillägget använder även det här schemat. Schemat är redan konfigurerat. Du kan lägga till en ny parameter i fältet objectStr

  ```json
      "runtimeSettings": [{
      "handlerSettings": {
      "protectedSettingsCertThumbprint": "",
      "protectedSettings": {
      "objectStr": "",
      "logsBlobUri": "",
      "statusBlobUri": ""
      }
      },
      "publicSettings": {
      "locale": "en-us",
      "taskId": "1c0ae461-9d3b-418c-a505-bb31dfe2095d",
      "objectStr": "",
      "commandStartTimeUTCTicks": "636295005824665976",
      "vmType": "vmType"
      }
      }]
      }
  ```

Följande JSON visar schemat för WorkloadBackup-tillägget.  

  ```json
  {
    "type": "extensions",
    "name": "WorkloadBackup",
    "location":"<myLocation>",
    "properties": {
      "publisher": "Microsoft.RecoveryServices",
      "type": "AzureBackupWindowsWorkload",
      "typeHandlerVersion": "1.1",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "locale":"<location>",
        "taskId":"<TaskId used by Azure Backup service to communicate with extension>",

        "objectStr": "<The configuration passed by Azure Backup service to extension>",

        "commandStartTimeUTCTicks": "<Scheduled start time of registration or upgrade task>",
        "vmType": "<Type of VM where registration got triggered Eg. Compute or ClassicCompute>"
      },
      "protectedSettings": {
        "objectStr": "<The sensitive configuration passed by Azure Backup service to extension>",
        "logsBlobUri": "<blob uri where logs of command execution by extension are written to>",
        "statusBlobUri": "<blob uri where status of the command executed by extension is written>"
      }
    }
  }
  ```

### <a name="property-values"></a>Egenskaps värden

Name | Värde/exempel | Datatyp
 --- | --- | ---
locale | sv-se  |  sträng
taskId | "1c0ae461-9d3b-418c-a505-bb31dfe2095d"  | sträng
objectStr <br/> (publicSettings)  | "eyJjb250YWluZXJQcm9wZXJ0aWVzIjp7IkNvbnRhaW5lcklEIjoiMzVjMjQxYTItOGRjNy00ZGE5LWI4NTMtMjdjYTJhNDZlM2ZkIiwiSWRNZ210Q29udGFpbmVySWQiOjM0NTY3ODg5LCJSZXNvdXJjZUlkIjoiMDU5NWIwOGEtYzI4Zi00ZmFlLWE5ODItOTkwOWMyMGVjNjVhIiwiU3Vic2NyaXB0aW9uSWQiOiJkNGEzOTliNy1iYjAyLTQ2MWMtODdmYS1jNTM5ODI3ZTgzNTQiLCJVbmlxdWVDb250 YWluZXJOYW1lIjoiODM4MDZjODUtNTQ4OS00NmNhLWEyZTctNWMzNzNhYjg3OTcyIn0sInN0YW1wTGlzdCI6W3siU2VydmljZU5hbWUiOjUsIlNlcnZpY2VTdGFtcFVybCI6Imh0dHA6XC9cL015V0xGYWJTdmMuY29tIn1dfQ = =" | sträng
commandStartTimeUTCTicks | "636967192566036845"  | sträng
vmType  | "Microsoft. Compute/virtualmachines"  | sträng
objectStr <br/> (protectedSettings) | "eyJjb250YWluZXJQcm9wZXJ0aWVzIjp7IkNvbnRhaW5lcklEIjoiMzVjMjQxYTItOGRjNy00ZGE5LWI4NTMtMjdjYTJhNDZlM2ZkIiwiSWRNZ210Q29udGFpbmVySWQiOjM0NTY3ODg5LCJSZXNvdXJjZUlkIjoiMDU5NWIwOGEtYzI4Zi00ZmFlLWE5ODItOTkwOWMyMGVjNjVhIiwiU3Vic2NyaXB0aW9uSWQiOiJkNGEzOTliNy1iYjAyLTQ2MWMtODdmYS1jNTM5ODI3ZTgzNTQiLCJVbmlxdWVDb250 YWluZXJOYW1lIjoiODM4MDZjODUtNTQ4OS00NmNhLWEyZTctNWMzNzNhYjg3OTcyIn0sInN0YW1wTGlzdCI6W3siU2VydmljZU5hbWUiOjUsIlNlcnZpY2VTdGFtcFVybCI6Imh0dHA6XC9cL015V0xGYWJTdmMuY29tIn1dfQ = =" | sträng
logsBlobUri | <https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Logs.txt?sv=2014-02-14&sr=b&sig=DbwYhwfeAC5YJzISgxoKk%2FEWQq2AO1vS1E0rDW%2FlsBw%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw> | sträng
statusBlobUri | <https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Status.txt?sv=2014-02-14&sr=b&sig=96RZBpTKCjmV7QFeXm5IduB%2FILktwGbLwbWg6Ih96Ao%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw> | sträng

## <a name="template-deployment"></a>Malldistribution

Vi rekommenderar att du lägger till AzureBackupWindowsWorkload-tillägg i en virtuell dator genom att aktivera SQL Server säkerhets kopiering på den virtuella datorn. Detta kan uppnås via [Resource Manager-mallen](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vm-workload-backup) som är utformad för att automatisera säkerhets kopieringen på en SQL Server VM.

## <a name="powershell-deployment"></a>PowerShell-distribution

Du måste registrera den virtuella Azure-datorn som innehåller SQL-programmet med ett Recovery Services-valv. Under registreringen installeras AzureBackupWindowsWorkload-tillägget på den virtuella datorn. Använd [register-AzRecoveryServicesBackupContainerPS-](/powershell/module/az.recoveryservices/register-azrecoveryservicesbackupcontainer) cmdlet för att registrera den virtuella datorn.

```powershell
$myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
```

Kommandot returnerar en **säkerhets kopia** av den här resursen och statusen kommer att **registreras**.

## <a name="next-steps"></a>Nästa steg

- [Lär dig mer](../../backup/backup-sql-server-azure-troubleshoot.md) om fel söknings rikt linjer för Azure SQL Server VM backup
- [Vanliga frågor](../../backup/faq-backup-sql-server.md) om säkerhets kopiering av SQL Server databaser som körs på virtuella Azure-datorer (VM: ar) och som använder tjänsten Azure Backup.
