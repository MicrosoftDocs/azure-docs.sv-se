---
title: Azure Backup för SQL Server körs på en virtuell Azure-dator
description: I den här artikeln får du lära dig hur du registrerar Azure Backup i SQL Server körs på en virtuell Azure-dator.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: v-amallick
ms.author: v-amallick
ms.collection: windows
ms.date: 07/05/2019
ms.openlocfilehash: c10be941206dd60887c9d82025506d1ea15c51a2
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517257"
---
# <a name="azure-backup-for-sql-server-running-in-azure-vm"></a>Azure Backup för SQL Server körs på en virtuell Azure-dator

Azure Backup bland andra erbjudanden ger stöd för att arbetsbelastningsbacka arbetsbelastningar, till exempel SQL Server körs på virtuella Azure-datorer. Eftersom SQL-programmet körs på en virtuell Azure-dator behöver säkerhetskopieringstjänsten behörighet att komma åt programmet och hämta nödvändig information.
För att göra det Azure Backup installerar **tillägget AzureBackupWindowsWorkload** på den virtuella dator där SQL Server körs under den registreringsprocess som utlöses av användaren.

## <a name="prerequisites"></a>Förutsättningar

En lista över scenarier som stöds finns i [supportmatrisen som](../../backup/sql-support-matrix.md#scenario-support) stöds av Azure Backup.

## <a name="network-connectivity"></a>Nätverksanslutningar

Azure Backup stöd för NSG-taggar, distribution av en proxyserver eller angivna IP-intervall. Mer information om var och en av metoderna finns i den här [artikeln.](../../backup/backup-sql-server-database-azure-vms.md#establish-network-connectivity)

## <a name="extension-schema"></a>Tilläggsschema

Tilläggsschemat och egenskapsvärdena är de konfigurationsvärden (körningsinställningar) som tjänsten vidarebefordrar till CRP-API:et. Dessa konfigurationsvärden används under registreringen och uppgraderingen. **AzureBackupWindowsWorkload-tillägget** använder också det här schemat. Schemat är förinställt. en ny parameter kan läggas till i fältet objectStr

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

### <a name="property-values"></a>Egenskapsvärden

Name | Värde/exempel | Datatyp
 --- | --- | ---
locale | sv-se  |  sträng
taskId | "1c0ae461-9d3b-418c-a505-bb31dfe2095d"  | sträng
objectStr <br/> (publicSettings)  | "eyJjb250YWluZXJQcm9wZXJ0aWVzIjp7IkNvbnRhaW5lcklEIjoiMzVjMjQxYTItOGRjNy00ZGE5LWI4NTMtMjdjYTJhNDM2ZkIiwiSWRNZ210Q29udGFpbmVySWQiOjM0NTY3ODg5LCJSZXNvdXJjZUlkIjoiMDU5NWIwOGEtYzI4Zi00ZmFlLWE5ODItOTkwOWMyMGVjNjVhIiwiU3Vic2NyaXB0aW9uSWQiOiSymbolNGEzOTliNy1iYjAyLTQ2MWMtODdmYS1jNTM5LIO3ZTgzNTQiLCJVbmlxdWVDb250YWluZXJOYW1lIjoiODM4MDZjODUtNTQ4OS00NmNhLWEyZTctNWMzNzNhYjg3OTcyIn0sInN0YW1wTGlzdCI6W3siU2VydmljZU5hbWUiOjUsIlNlcnZpY2VTdGFtcFVybCI6Imh0dHA6XC9cL015V0xGYWJTdmMuY29tIn1dfQ==" | sträng
commandStartTimeUTCTicks | "636967192566036845"  | sträng
vmType  | "microsoft.compute/virtualmachines"  | sträng
objectStr <br/> (protectedSettings) | "eyJjb250YWluZXJQcm9wZXJ0aWVzIjp7IkNvbnRhaW5lcklEIjoiMzVjMjQxYTItOGRjNy00ZGE5LWI4NTMtMjdjYTJhNDM2ZkIiwiSWRNZ210Q29udGFpbmVySWQiOjM0NTY3ODg5LCJSZXNvdXJjZUlkIjoiMDU5NWIwOGEtYzI4Zi00ZmFlLWE5ODItOTkwOWMyMGVjNjVhIiwiU3Vic2NyaXB0aW9uSWQiOiJjNGEzOTliNy1iYjAyLTQ2MWMtODdmYS1jNTM5LIO3ZTgzNTQiLCJVbmlxdWVDb250YWluZXJOYW1lIjoiODM4MDZjODUtNTQ4OS00NmNhLWEyZTctNWMzNzNhYjg3OTcyIn0sInN0YW1wTGlzdCI6W3siU2VydmljZU5hbWUiOjUsIlNlcnZpY2VTdGFtcFVybCI6Imh0dHA6XC9cL015V0xGYWJTdmMuY29tIn1dfQ==" | sträng
logsBlobUri | <https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Logs.txt?sv=2014-02-14&sr=b&sig=DbwYhwfeAC5YJzISgxoKk%2FEWQq2AO1vS1E0rDW%2FlsBw%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw> | sträng
statusBlobUri | <https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Status.txt?sv=2014-02-14&sr=b&sig=96RZBpTKCjmV7QFeXm5IduB%2FILktwGbLwbWg6Ih96Ao%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw> | sträng

## <a name="template-deployment"></a>Malldistribution

Vi rekommenderar att du lägger till tillägget AzureBackupWindowsWorkload på en virtuell dator genom att aktivera SQL Server säkerhetskopiering på den virtuella datorn. Detta kan uppnås med hjälp av [Resource Manager som utformats](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vm-workload-backup) för att automatisera säkerhetskopiering på en SQL Server VM.

## <a name="powershell-deployment"></a>PowerShell-distribution

Du måste "registrera" den virtuella Azure-dator som innehåller SQL-programmet med ett Recovery Services-valv. Under registreringen installeras tillägget AzureBackupWindowsWorkload på den virtuella datorn. Använd [cmdleten Register-AzRecoveryServicesBackupContainerPS](/powershell/module/az.recoveryservices/register-azrecoveryservicesbackupcontainer) för att registrera den virtuella datorn.

```powershell
$myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
```

Kommandot returnerar en **säkerhetskopieringscontainer** för den här resursen och **statusen** registreras.

## <a name="next-steps"></a>Nästa steg

- [Läs mer om](../../backup/backup-sql-server-azure-troubleshoot.md) felsökningsriktlinjerna för Azure SQL Server VM-säkerhetskopiering
- [Vanliga frågor](../../backup/faq-backup-sql-server.yml) om att SQL Server databaser som körs på virtuella Azure-datorer (VM) och som använder Azure Backup tjänsten.
