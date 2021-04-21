---
title: Hantera säkerhetskopior av Azure-filresurs med Azure CLI
description: Lär dig hur du använder Azure CLI för att hantera och övervaka Azure-filresurser som säkerhetskopieras av Azure Backup.
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: e389f5cde12734ef4bf0be4ecfba69ba33f5e030
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773611"
---
# <a name="manage-azure-file-share-backups-with-the-azure-cli"></a>Hantera säkerhetskopior av Azure-filresurs med Azure CLI

Azure CLI tillhandahåller en kommandoradsupplevelse för hantering av Azure-resurser. Det är ett bra verktyg för att skapa anpassad automatisering för användning av Azure-resurser. Den här artikeln förklarar hur du utför uppgifter för att hantera och övervaka Azure-filresurser som säkerhetskopieras av [Azure Backup](./backup-overview.md). Du kan också utföra de här stegen med [hjälp Azure Portal](https://portal.azure.com/).

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du redan har en Azure-filresurs som säkerhetskopierats [av Azure Backup](./backup-overview.md). Om du inte har någon kan du gå till [Säkerhetskopiera Azure-filresurser med CLI för](backup-afs-cli.md) att konfigurera säkerhetskopiering för dina filresurser. I den här artikeln använder du följande resurser:
   -  **Resursgrupp:** *azurefiles*
   -  **RecoveryServicesVault:** *azurefilesvault*
   -  **Lagringskonto:** *afsaccount*
   -  **Filresurs:** *azurefiles*
  
  [!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]
   - Den här självstudien kräver version 2.0.18 eller senare av Azure CLI. Om du Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="monitor-jobs"></a>Övervaka jobb

När du utlöser säkerhetskopierings- eller återställningsåtgärder skapar säkerhetskopieringstjänsten ett jobb för spårning. Om du vill övervaka slutförda eller jobb som körs för närvarande använder [du cmdleten az backup job list.](/cli/azure/backup/job#az_backup_job_list) Med CLI kan du också pausa [ett jobb som körs eller](/cli/azure/backup/job#az_backup_job_stop) vänta [tills ett jobb har avslutats.](/cli/azure/backup/job#az_backup_job_wait)

I följande exempel visas status för säkerhetskopieringsjobb för *Recovery Services-valvet azurefilesvault:*

```azurecli-interactive
az backup job list --resource-group azurefiles --vault-name azurefilesvault
```

```output
[
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/d477dfb6-b292-4f24-bb43-6b14e9d06ab5",
    "location": null,
    "name": "d477dfb6-b292-4f24-bb43-6b14e9d06ab5",
    "properties": {
      "actionsInfo": null,
      "activityId": "3cef43ed-0af4-43e2-b9cb-1322c496ccb4",
      "backupManagementType": "AzureStorage",
      "duration": "0:00:29.718011",
      "endTime": "2020-01-13T08:05:29.180606+00:00",
      "entityFriendlyName": "azurefiles",
      "errorDetails": null,
      "extendedInfo": null,
      "jobType": "AzureStorageJob",
      "operation": "Backup",
      "startTime": "2020-01-13T08:04:59.462595+00:00",
      "status": "Completed",
      "storageAccountName": "afsaccount",
      "storageAccountVersion": "MicrosoftStorage"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupJobs"
  },
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/1b9399bf-c23c-4caa-933a-5fc2bf884519",
    "location": null,
    "name": "1b9399bf-c23c-4caa-933a-5fc2bf884519",
    "properties": {
      "actionsInfo": null,
      "activityId": "2663449c-94f1-4735-aaf9-5bb991e7e00c",
      "backupManagementType": "AzureStorage",
      "duration": "0:00:28.145216",
      "endTime": "2020-01-13T08:05:27.519826+00:00",
      "entityFriendlyName": "azurefilesresource",
      "errorDetails": null,
      "extendedInfo": null,
      "jobType": "AzureStorageJob",
      "operation": "Backup",
      "startTime": "2020-01-13T08:04:59.374610+00:00",
      "status": "Completed",
      "storageAccountName": "afsaccount",
      "storageAccountVersion": "MicrosoftStorage"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupJobs"
  }
]
```

## <a name="modify-policy"></a>Ändra princip

Du kan ändra en säkerhetskopieringspolicy för att ändra säkerhetskopieringsfrekvensen eller kvarhållningsintervallet med [hjälp av az backup item set-policy](/cli/azure/backup/item#az_backup_item_set_policy).

Definiera följande parametrar för att ändra principen:

* **--container-name:** Namnet på det lagringskonto som är värd för filresursen. Om du vill **hämta namnet** eller det **egna namnet** på din container använder du kommandot az backup [container list.](/cli/azure/backup/container#az_backup_container_list)
* **--name:** Namnet på den filresurs som du vill ändra principen för. Om du vill **hämta namnet** eller det **egna namnet** på det säkerhetskopierade objektet använder du kommandot az backup [item list.](/cli/azure/backup/item#az_backup_item_list)
* **--policy-name:** Namnet på den säkerhetskopieringspolicy som du vill ange för filresursen. Du kan använda [az backup policy list för](/cli/azure/backup/policy#az_backup_policy_list) att visa alla principer för valvet.

I följande exempel anges *säkerhetskopieringsprincipen schedule2* för *filresursen azurefiles* som finns i *afsaccount-lagringskontot.*

```azurecli-interactive
az backup item set-policy --policy-name schedule2 --name azurefiles --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --name "AzureFileShare;azurefiles" --backup-management-type azurestorage --out table
```

Du kan också köra föregående kommando med hjälp av de egna namnen för containern och objektet genom att ange följande två ytterligare parametrar:

* **--backup-management-type**: *azurestorage*
* **--workload-type**: *azurefileshare*

```azurecli-interactive
az backup item set-policy --policy-name schedule2 --name azurefiles --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --name azurefiles --backup-management-type azurestorage --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
fec6f004-0e35-407f-9928-10a163f123e5  azurefiles
```

Attributet **Namn** i utdata motsvarar namnet på jobbet som skapas av säkerhetskopieringstjänsten för din ändringsprincipåtgärd. Om du vill spåra jobbets status använder du [cmdleten az backup job show.](/cli/azure/backup/job#az_backup_job_show)

## <a name="stop-protection-on-a-file-share"></a>Avbryta skyddet av en filresurs

Det finns två sätt att sluta skydda Azure-filresurser:

* Stoppa alla framtida säkerhetskopieringsjobb och *ta bort* alla återställningspunkter.
* Stoppa alla framtida säkerhetskopieringsjobb *men lämna* kvar återställningspunkterna.

Det kan finnas en kostnad kopplad till att lämna återställningspunkterna i lagringen, eftersom de underliggande ögonblicksbilderna som skapas Azure Backup kommer att behållas. Fördelen med att lämna återställningspunkterna är att du kan återställa filresursen senare om du vill. Information om kostnaden för att lämna återställningspunkterna finns i [prisinformationen.](https://azure.microsoft.com/pricing/details/storage/files) Om du väljer att ta bort alla återställningspunkter kan du inte återställa filresursen.

Om du vill stoppa skyddet för filresursen definierar du följande parametrar:

* **--container-name:** Namnet på det lagringskonto som är värd för filresursen. Om du vill **hämta namnet** eller det **egna namnet** på din container använder du kommandot az backup [container list.](/cli/azure/backup/container#az_backup_container_list)
* **--item-name:** Namnet på den filresurs som du vill stoppa skyddet för. Om du vill **hämta namnet** eller det **egna namnet** på det säkerhetskopierade objektet använder du kommandot az backup [item list.](/cli/azure/backup/item#az_backup_item_list)

### <a name="stop-protection-and-retain-recovery-points"></a>Stoppa skyddet och behåll återställningspunkter

Om du vill stoppa skyddet samtidigt som du behåller data använder du [cmdleten az backup protection disable.](/cli/azure/backup/protection#az_backup_protection_disable)

I följande exempel stoppas skyddet för *azurefiles-filresursen* men alla återställningspunkter behålls.

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name “AzureFileShare;azurefiles” --out table
```

Du kan också köra föregående kommando med hjälp av det egna namnet för containern och objektet genom att ange följande två ytterligare parametrar:

* **--backup-management-type**: *azurestorage*
* **--workload-type**: *azurefileshare*

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
fec6f004-0e35-407f-9928-10a163f123e5  azurefiles
```

**Name-attributet** i utdata motsvarar namnet på jobbet som skapas av säkerhetskopieringstjänsten för din stoppskyddsåtgärd. Om du vill spåra jobbets status använder du [cmdleten az backup job show.](/cli/azure/backup/job#az_backup_job_show)

### <a name="stop-protection-without-retaining-recovery-points"></a>Stoppa skyddet utan att behålla återställningspunkter

Om du vill stoppa skyddet utan att behålla återställningspunkter använder du cmdleten [az backup protection disable](/cli/azure/backup/protection#az_backup_protection_disable) med alternativet **delete-backup-data** inställt på **true**.

I följande exempel stoppas skyddet för *azurefiles-filresursen* utan att återställningspunkter behålls.

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name “AzureFileShare;azurefiles” --delete-backup-data true --out table
```

Du kan också köra föregående kommando med hjälp av det egna namnet för containern och objektet genom att ange följande två ytterligare parametrar:

* **--backup-management-type**: *azurestorage*
* **--workload-type**: *azurefileshare*

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --delete-backup-data true --out table
```

## <a name="resume-protection-on-a-file-share"></a>Återuppta skyddet av en filresurs

Om du har stoppat skyddet för en Azure-filresurs men behållit återställningspunkter kan du återuppta skyddet senare. Om du inte behåller återställningspunkterna kan du inte återuppta skyddet.

Om du vill återuppta skyddet av filresursen definierar du följande parametrar:

* **--container-name:** Namnet på det lagringskonto som är värd för filresursen. Om du vill **hämta namnet** eller det **egna namnet** på din container använder du kommandot az backup [container list.](/cli/azure/backup/container#az_backup_container_list)
* **--item-name:** Namnet på den filresurs som du vill återuppta skyddet för. Om du vill **hämta namnet** eller det **egna namnet** på det säkerhetskopierade objektet använder du kommandot az backup [item list.](/cli/azure/backup/item#az_backup_item_list)
* **--policy-name:** Namnet på den säkerhetskopieringspolicy som du vill återuppta skyddet av filresursen för.

I följande exempel används [cmdleten az backup protection resume](/cli/azure/backup/protection#az_backup_protection_resume) för att återuppta skyddet för *azurefiles-filresursen* med hjälp av *säkerhetskopieringspolicyn schedule1.*

```azurecli-interactive
az backup protection resume --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --policy-name schedule2 --out table
```

Du kan också köra föregående kommando med hjälp av det egna namnet för containern och objektet genom att ange följande två ytterligare parametrar:

* **--backup-management-type:** *azurestorage*
* **--workload-type:** *azurefileshare*

```azurecli-interactive
az backup protection resume --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --policy-name schedule2 --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
75115ab0-43b0-4065-8698-55022a234b7f  azurefiles
```

Attributet **Namn** i utdata motsvarar namnet på jobbet som har skapats av säkerhetskopieringstjänsten för åtgärden för att återuppta skyddet. Om du vill spåra jobbets status använder du [cmdleten az backup job show.](/cli/azure/backup/job#az_backup_job_show)

## <a name="unregister-a-storage-account"></a>Avregistrera ett lagringskonto

Om du vill skydda dina filresurser i ett visst lagringskonto med hjälp av ett annat Recovery Services-valv måste du först stoppa skyddet för alla [filresurser](#stop-protection-on-a-file-share) i det lagringskontot. Avregistrera sedan kontot från Recovery Services-valvet som för närvarande används för skydd.

Du måste ange ett containernamn för att avregistrera lagringskontot. Om du vill **hämta namnet** eller det egna **namnet på** din container använder du kommandot az backup [container list.](/cli/azure/backup/container#az_backup_container_list)

I följande exempel avregistreras *afsaccount-lagringskontot* från *azurefilesvault* med hjälp av cmdleten [az backup container unregister.](/cli/azure/backup/container#az_backup_container_unregister)

```azurecli-interactive
az backup container unregister --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --out table
```

Du kan också köra den tidigare cmdleten med hjälp av det egna namnet för containern genom att ange följande ytterligare parameter:

* **--backup-management-type**: *azurestorage*

```azurecli-interactive
az backup container unregister --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --backup-management-type azurestorage --out table
```

## <a name="next-steps"></a>Nästa steg

Mer information finns i Felsöka [säkerhetskopiering av Azure-filresurser.](troubleshoot-azure-files.md)
