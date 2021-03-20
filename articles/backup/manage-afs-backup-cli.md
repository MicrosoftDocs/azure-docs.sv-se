---
title: Hantera säkerhets kopior av Azure-filresurser med Azure CLI
description: Lär dig hur du använder Azure CLI för att hantera och övervaka Azure-filresurser som har säkerhetskopierats av Azure Backup.
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: 5a8a785016845b836a102663a959e4b2f28696b6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94566460"
---
# <a name="manage-azure-file-share-backups-with-the-azure-cli"></a>Hantera säkerhets kopior av Azure-filresurser med Azure CLI

Azure CLI innehåller en kommando rads upplevelse för att hantera Azure-resurser. Det är ett bra verktyg för att skapa anpassad automatisering för att använda Azure-resurser. Den här artikeln förklarar hur du utför uppgifter för att hantera och övervaka Azure-filresurser som säkerhets kopie ras av [Azure Backup](./backup-overview.md). Du kan också utföra de här stegen med [Azure Portal](https://portal.azure.com/).

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du redan har en Azure-filresurs som har säkerhetskopierats av [Azure Backup](./backup-overview.md). Om du inte har en sådan, se [säkerhetskopiera Azure-filresurser med CLI](backup-afs-cli.md) för att konfigurera säkerhets kopiering för dina fil resurser. I den här artikeln använder du följande resurser:
   -  **Resurs grupp**: *migreringsåtgärden*
   -  **RecoveryServicesVault**: *azurefilesvault*
   -  **Lagrings konto**: *afsaccount*
   -  **Fil resurs**: *migreringsåtgärden*
  
  [!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]
   - I den här självstudien krävs version 2.0.18 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="monitor-jobs"></a>Övervaka jobb

När du utlöser säkerhets kopierings-eller återställnings åtgärder skapar säkerhets kopierings tjänsten ett jobb för spårning. Om du vill övervaka slutförda eller pågående jobb använder du cmdleten [AZ backup Job List](/cli/azure/backup/job#az-backup-job-list) . Med CLI kan du också [pausa ett pågående jobb](/cli/azure/backup/job#az-backup-job-stop) eller [vänta tills ett jobb har slutförts](/cli/azure/backup/job#az-backup-job-wait).

I följande exempel visas status för säkerhets kopierings jobb för *azurefilesvault* Recovery Services-valvet:

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

Du kan ändra en säkerhets kopierings princip för att ändra säkerhets kopierings frekvens eller kvarhållningsintervall genom att använda [AZ säkerhets kopierings princip](/cli/azure/backup/item#az-backup-item-set-policy).

Definiera följande parametrar för att ändra principen:

* **--container-Name**: namnet på det lagrings konto som är värd för fil resursen. Om du vill hämta **namnet** eller det **egna namnet** på din behållare använder du kommandot [AZ backup container List](/cli/azure/backup/container#az-backup-container-list) .
* **--Name**: namnet på den fil resurs som du vill ändra principen för. Om du vill hämta **namnet** eller det **egna namnet** på det säkerhetskopierade objektet använder du kommandot [AZ backup item List](/cli/azure/backup/item#az-backup-item-list) .
* **--princip namn**: namnet på den säkerhets kopierings princip som du vill ange för din fil resurs. Du kan använda [AZ säkerhets kopierings princip lista](/cli/azure/backup/policy#az-backup-policy-list) för att visa alla principer för ditt valv.

I följande exempel anges *schedule2* säkerhets kopierings policy för *migreringsåtgärden* -filresursen i *afsaccount* Storage-kontot.

```azurecli-interactive
az backup item set-policy --policy-name schedule2 --name azurefiles --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --name "AzureFileShare;azurefiles" --backup-management-type azurestorage --out table
```

Du kan också köra föregående kommando genom att använda de egna namnen för behållaren och objektet genom att tillhandahålla följande två ytterligare parametrar:

* **--säkerhets kopiering-hantering-typ**: *azurestorage*
* **--arbets belastning-typ**: *azurefileshare*

```azurecli-interactive
az backup item set-policy --policy-name schedule2 --name azurefiles --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --name azurefiles --backup-management-type azurestorage --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
fec6f004-0e35-407f-9928-10a163f123e5  azurefiles
```

Namnattributet **i** utdata motsvarar namnet på jobbet som skapas av säkerhets kopierings tjänsten för din ändrings princip åtgärd. Om du vill spåra jobbets status använder du [AZ backup Job show](/cli/azure/backup/job#az-backup-job-show) cmdlet.

## <a name="stop-protection-on-a-file-share"></a>Avbryta skyddet av en filresurs

Det finns två sätt att sluta skydda Azure-filresurser:

* Stoppa alla framtida säkerhets kopierings jobb och *ta bort* alla återställnings punkter.
* Stoppa alla framtida säkerhets kopierings jobb men *lämna* återställnings punkterna.

Det kan finnas en kostnad som är kopplad till att lämna återställnings punkterna i lagringen, eftersom de underliggande ögonblicks bilderna som skapats av Azure Backup kommer att behållas. Fördelen med att lämna återställnings punkterna är möjligheten att återställa fil resursen senare, om du vill. Information om kostnaden för att lämna återställnings punkterna finns i [pris informationen](https://azure.microsoft.com/pricing/details/storage/files). Om du väljer att ta bort alla återställnings punkter kan du inte återställa fil resursen.

Definiera följande parametrar för att stoppa skyddet av fil resursen:

* **--container-Name**: namnet på det lagrings konto som är värd för fil resursen. Om du vill hämta **namnet** eller det **egna namnet** på din behållare använder du kommandot [AZ backup container List](/cli/azure/backup/container#az-backup-container-list) .
* **--objekt namn**: namnet på den fil resurs som du vill stoppa skyddet för. Om du vill hämta **namnet** eller det **egna namnet** på det säkerhetskopierade objektet använder du kommandot [AZ backup item List](/cli/azure/backup/item#az-backup-item-list) .

### <a name="stop-protection-and-retain-recovery-points"></a>Stoppa skyddet och behåll återställnings punkter

Om du vill stoppa skyddet och behålla data använder du [AZ backup Protection Disable](/cli/azure/backup/protection#az-backup-protection-disable) cmdlet.

I följande exempel stoppas skyddet för *migreringsåtgärden* -filresursen men alla återställnings punkter bevaras.

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name “AzureFileShare;azurefiles” --out table
```

Du kan också köra föregående kommando genom att använda det egna namnet för behållaren och objektet genom att tillhandahålla följande två ytterligare parametrar:

* **--säkerhets kopiering-hantering-typ**: *azurestorage*
* **--arbets belastning-typ**: *azurefileshare*

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
fec6f004-0e35-407f-9928-10a163f123e5  azurefiles
```

Namnattributet **i** utdata motsvarar namnet på jobbet som skapats av säkerhets kopierings tjänsten för åtgärden stoppa skydd. Om du vill spåra jobbets status använder du [AZ backup Job show](/cli/azure/backup/job#az-backup-job-show) cmdlet.

### <a name="stop-protection-without-retaining-recovery-points"></a>Stoppa skyddet utan att behålla återställnings punkter

Om du vill stoppa skyddet utan att behålla återställnings punkter använder du [säkerhets kopierings skydd inaktivera](/cli/azure/backup/protection#az-backup-protection-disable) cmdlet med alternativet **ta bort-säkerhets kopierings data** inställt på **Sant**.

I följande exempel stoppas skyddet av *migreringsåtgärden* -filresursen utan att återställnings punkterna bevaras.

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name “AzureFileShare;azurefiles” --delete-backup-data true --out table
```

Du kan också köra föregående kommando genom att använda det egna namnet för behållaren och objektet genom att tillhandahålla följande två ytterligare parametrar:

* **--säkerhets kopiering-hantering-typ**: *azurestorage*
* **--arbets belastning-typ**: *azurefileshare*

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --delete-backup-data true --out table
```

## <a name="resume-protection-on-a-file-share"></a>Återuppta skyddet av en filresurs

Om du har stoppat skyddet för en Azure-filresurs men behåller återställnings punkter kan du återuppta skyddet senare. Om du inte behåller återställnings punkterna kan du inte återuppta skyddet.

Definiera följande parametrar för att återuppta skyddet av fil resursen:

* **--container-Name**: namnet på det lagrings konto som är värd för fil resursen. Om du vill hämta **namnet** eller det **egna namnet** på din behållare använder du kommandot [AZ backup container List](/cli/azure/backup/container#az-backup-container-list) .
* **--objekt namn**: namnet på den fil resurs som du vill återuppta skyddet för. Om du vill hämta **namnet** eller det **egna namnet** på det säkerhetskopierade objektet använder du kommandot [AZ backup item List](/cli/azure/backup/item#az-backup-item-list) .
* **--princip namn**: namnet på den säkerhets kopierings princip för vilken du vill återuppta skyddet av fil resursen.

I följande exempel används cmdleten [AZ backup Protection Resume](/cli/azure/backup/protection#az-backup-protection-resume) för att återuppta skyddet av fil resursen *migreringsåtgärden* med hjälp av *schedule1* säkerhets kopierings princip.

```azurecli-interactive
az backup protection resume --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --policy-name schedule2 --out table
```

Du kan också köra föregående kommando genom att använda det egna namnet för behållaren och objektet genom att tillhandahålla följande två ytterligare parametrar:

* **--säkerhets kopiering-hantering-typ**: *azurestorage*
* **--arbets belastning-typ**: *azurefileshare*

```azurecli-interactive
az backup protection resume --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --policy-name schedule2 --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
75115ab0-43b0-4065-8698-55022a234b7f  azurefiles
```

Namnattributet **i** utdata motsvarar namnet på jobbet som skapats av säkerhets kopierings tjänsten för åtgärden återuppta skydd. Om du vill spåra jobbets status använder du [AZ backup Job show](/cli/azure/backup/job#az-backup-job-show) cmdlet.

## <a name="unregister-a-storage-account"></a>Avregistrera ett lagrings konto

Om du vill skydda dina fil resurser i ett visst lagrings konto med hjälp av ett annat Recovery Services-valv måste du först [sluta skydda alla fil resurser](#stop-protection-on-a-file-share) i det lagrings kontot. Avregistrera sedan kontot från Recovery Services-valvet som används för skydd.

Du måste ange ett behållar namn för att avregistrera lagrings kontot. Om du vill hämta **namnet** eller det **egna namnet** på din behållare använder du kommandot [AZ backup container List](/cli/azure/backup/container#az-backup-container-list) .

I följande exempel avregistreras lagrings kontot för *afsaccount* från *azurefilesvault* med hjälp av cmdleten [AZ backup deregister](/cli/azure/backup/container#az-backup-container-unregister) .

```azurecli-interactive
az backup container unregister --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --out table
```

Du kan också köra föregående cmdlet med hjälp av det egna namnet för behållaren genom att ange följande ytterligare parameter:

* **--säkerhets kopiering-hantering-typ**: *azurestorage*

```azurecli-interactive
az backup container unregister --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --backup-management-type azurestorage --out table
```

## <a name="next-steps"></a>Nästa steg

Mer information finns i [Felsöka säkerhets kopiering av Azure-filresurser](troubleshoot-azure-files.md).
