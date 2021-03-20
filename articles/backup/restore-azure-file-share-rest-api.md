---
title: Återställa Azure-filresurser med REST API
description: Lär dig hur du använder REST API för att återställa Azure-filresurser eller vissa filer från en återställnings punkt som skapats av Azure Backup
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 60c73caa5db684e38b94b4d5786f2fd24aa65d08
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88761805"
---
# <a name="restore-azure-file-shares-using-rest-api"></a>Återställa Azure-filresurser med hjälp av REST API

I den här artikeln förklaras hur du återställer en hel fil resurs eller vissa filer från en återställnings punkt som skapats av [Azure Backup](./backup-overview.md) med hjälp av REST API.

I slutet av den här artikeln får du lära dig hur du utför följande åtgärder med hjälp av REST API:

* Visa återställnings punkter för en säkerhets kopie rad Azure-filresurs.
* Återställa en fullständig Azure-filresurs.
* Återställa enskilda filer eller mappar.

## <a name="prerequisites"></a>Förutsättningar

Vi förutsätter att du redan har en säkerhetskopierad fil resurs som du vill återställa. Om du inte gör det kontrollerar du [säkerhets kopiering av Azure-filresurs med REST API](backup-azure-file-share-rest-api.md) för att lära dig hur du skapar en.

I den här artikeln använder vi följande resurser:

* **RecoveryServicesVault**: *azurefilesvault*
* **Resurs grupp**: *migreringsåtgärden*
* **Lagrings konto**: *afsaccount*
* **Fil resurs**: *migreringsåtgärden*

## <a name="fetch-containername-and-protecteditemname"></a>Hämta ContainerName och ProtectedItemName

För de flesta av de återställning relaterade API-anropen måste du skicka värden för URI-parametrarna {containerName} och {protectedItemName}. Använd attributet ID i svars texten för [Get backupprotectableitems](/rest/api/backup/protecteditems/get) -åtgärden för att hämta värden för dessa parametrar. I vårt exempel är ID: t för den fil resurs som vi vill skydda:

`"/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;afsaccount/protectableItems/azurefileshare;azurefiles`

Det innebär att värdena översätts enligt följande:

* {ContainerName}- *storagecontainer; Storage; migreringsåtgärden; afsaccount*
* {protectedItemName}- *azurefileshare; migreringsåtgärden*

## <a name="fetch-recovery-points-for-backed-up-azure-file-share"></a>Hämta återställnings punkter för säkerhets kopiering av Azure-filresurs

Om du vill återställa en säkerhets kopie rad fil resurs eller filer väljer du först en återställnings punkt för att utföra återställnings åtgärden. Tillgängliga återställnings punkter för ett säkerhetskopierat objekt kan listas med hjälp av [listan över återställnings punkts](/rest/api/site-recovery/recoverypoints/listbyreplicationprotecteditems) REST API anrop. Det är en GET-åtgärd med alla relevanta värden.

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints?api-version=2019-05-13&$filter={$filter}
```

Ange URI-värden på följande sätt:

* {fabricName}: *Azure*
* {vaultName}: *azurefilesvault*
* {ContainerName}: *storagecontainer; Storage; migreringsåtgärden; afsaccount*
* {protectedItemName}: *azurefileshare; migreringsåtgärden*
* {ResourceGroupName}: *migreringsåtgärden*

Hämta URI har alla nödvändiga parametrar. Det behövs ingen ytterligare brödtext.

```http
GET https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare;azurefiles/recoveryPoints?api-version=2019-05-13
```

### <a name="example-response-for-fetch-recovery-points"></a>Exempel svar för hämtning av återställnings punkter

När Hämta URI har skickats returneras ett 200-svar:

```http
HTTP/1.1" 200 None
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Transfer-Encoding': 'chunked'
'Content-Type': 'application/json'
'Content-Encoding': 'gzip'
'Expires': '-1'
'Vary': 'Accept-Encoding'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': 'd68d7951-7d97-4c49-9a2d-7fbaab55233a'
'x-ms-client-request-id': '4edb5a58-47ea-11ea-a27a-0a580af41908, 4edb5a58-47ea-11ea-a27a-0a580af41908'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'Server': 'Microsoft-IIS/10.0'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-reads': '11998'
'x-ms-correlation-request-id': 'd68d7951-7d97-4c49-9a2d-7fbaab55233a'
'x-ms-routing-request-id': 'WESTEUROPE:20200205T073708Z:d68d7951-7d97-4c49-9a2d-7fbaab55233a'
'Date': 'Wed, 05 Feb 2020 07:37:08 GMT'
{
“value”:[
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare;azurefiles/recoveryPoints/932881138555802864",
    "location": null,
    "name": "932881138555802864",
    "properties": {
      "fileShareSnapshotUri": "https://afsaccount.file.core.windows.net/azurefiles?sharesnapshot=2020-02-04T08:01:35.0000000Z",
      "objectType": "AzureFileShareRecoveryPoint",
      "recoveryPointSizeInGb": 1,
      "recoveryPointTime": "2020-02-04T08:01:35+00:00",
      "recoveryPointType": "FileSystemConsistent"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints"
  },
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare;azurefiles/recoveryPoints/932878582606969225",
    "location": null,
    "name": "932878582606969225",
    "properties": {
      "fileShareSnapshotUri": "https://afsaccount.file.core.windows.net/azurefiles?sharesnapshot=2020-02-03T08:05:30.0000000Z",
      "objectType": "AzureFileShareRecoveryPoint",
      "recoveryPointSizeInGb": 1,
      "recoveryPointTime": "2020-02-03T08:05:30+00:00",
      "recoveryPointType": "FileSystemConsistent"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints"
  },
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare;azurefiles/recoveryPoints/932890167574511261",
    "location": null,
    "name": "932890167574511261",
    "properties": {
      "fileShareSnapshotUri": "https://afsaccount.file.core.windows.net/azurefiles?sharesnapshot=2020-02-02T08:03:50.0000000Z",
      "objectType": "AzureFileShareRecoveryPoint",
      "recoveryPointSizeInGb": 1,
      "recoveryPointTime": "2020-02-02T08:03:50+00:00",
      "recoveryPointType": "FileSystemConsistent"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints"
  },
```

Återställnings punkten identifieras med fältet {Name} i svaret ovan.

## <a name="full-share-recovery-using-rest-api"></a>Fullständig delnings återställning med REST API

Använd det här alternativet för återställning för att återställa hela fil resursen på den ursprungliga platsen eller en annan plats.
Aktivering av återställning är en POST-begäran och du kan utföra den här åtgärden med hjälp av [återställnings återställnings](/rest/api/backup/restores/trigger) REST API.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints/{recoveryPointId}/restore?api-version=2019-05-13
```

Värdena {containerName} och {protectedItemName} anges [här](#fetch-containername-and-protecteditemname) och recoveryPointID är {Name}-fältet för den återställnings punkt som anges ovan.

```http
POST https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare%3Bazurefiles/recoveryPoints/932886657837421071/restore?api-version=2019-05-13'
```

### <a name="create-request-body"></a>Skapa brödtext för begäran

För att utlösa en återställning för en Azure-filresurs är följande komponenter i begär ande texten:

Namn |  Typ   |   Beskrivning
--- | ---- | ----
Egenskaper | AzureFileShareRestoreRequest | Egenskaper för RestoreRequestResource

En fullständig lista över definitioner av begär ande texten och annan information finns i [Återställnings REST API dokumentet](/rest/api/backup/restores/trigger#request-body).

### <a name="restore-to-original-location"></a>Återställ till ursprunglig plats

#### <a name="request-body-example-for-restore-to-original-location"></a>Exempel på begäran om att återställa till den ursprungliga platsen

Följande begär ande text definierar egenskaper som krävs för att utlösa en Azure-fil resurs återställning:

```json
{
   "properties":{
      "objectType":"AzureFileShareRestoreRequest",
      "recoveryType":"OriginalLocation",
      "sourceResourceId":"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afsaccount",
      "copyOptions":"Overwrite",
      "restoreRequestType":"FullShareRestore"
}
}
```

### <a name="restore-to-alternate-location"></a>Återställ till en annan plats

Ange följande parametrar för alternativ plats återställning:

* **targetResourceId**: det lagrings konto som det säkerhetskopierade innehållet återställs till. Mål lagrings kontot måste finnas på samma plats som valvet.
* **namn**: fil resursen på mål lagrings kontot som det säkerhetskopierade innehållet återställs till.
* **targetFolderPath**: mappen under den fil resurs som data återställs till.

#### <a name="request-body-example-for-restore-to-alternate-location"></a>Exempel på begäran om att återställa till en annan plats

Följande begär ande brödtext återställer fil resursen *migreringsåtgärden* i *afsaccount* Storage-kontot till *azurefiles1* -filresursen i *afaccount1* -lagrings kontot.

```json
{
   "properties":{
      "objectType":"AzureFileShareRestoreRequest",
      "recoveryType":"AlternateLocation",
      "sourceResourceId":"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afsaccount",
      "copyOptions":"Overwrite",
      "restoreRequestType":"FullShareRestore",
      "restoreFileSpecs":[
         {
            "targetFolderPath":"restoredata"
}
],
      "targetDetails":{
         "name":"azurefiles1",
         "targetResourceId":"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afaccount1"
}
}
}
```

### <a name="response"></a>Svarsåtgärder

Utlösaren av en återställnings åtgärd är en [asynkron åtgärd](../azure-resource-manager/management/async-operations.md). Den här åtgärden skapar en annan åtgärd som måste spåras separat.
Den returnerar två svar: 202 (accepterad) när en annan åtgärd skapas och 200 (OK) när åtgärden har slutförts.

#### <a name="response-example"></a>Exempel på svar

När du har skickat *post* -URI: n för att utlösa en återställning är det första svaret 202 (accepteras) med ett plats huvud eller Azure-async-header.

```http
HTTP/1.1" 202
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Expires': '-1'
'Location': 'https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare;azurefiles/operationResults/68ccfbc1-a64f-4b29-b955-314b5790cfa9?api-version=2019-05-13'
'Retry-After': '60'
'Azure-AsyncOperation': 'https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare;azurefiles/operationsStatus/68ccfbc1-a64f-4b29-b955-314b5790cfa9?api-version=2019-05-13'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': '2426777d-c5ec-44b6-a324-384f8947460c'
'x-ms-client-request-id': '3c743096-47eb-11ea-ae90-0a580af41908, 3c743096-47eb-11ea-ae90-0a580af41908'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-writes': '1198'
'x-ms-correlation-request-id': '2426777d-c5ec-44b6-a324-384f8947460c'
'x-ms-routing-request-id': 'WESTEUROPE:20200205T074347Z:2426777d-c5ec-44b6-a324-384f8947460c'
'Date': 'Wed, 05 Feb 2020 07:43:47 GMT'
```

Spåra sedan den resulterande åtgärden med hjälp av plats rubriken eller Azure-AsyncOperation huvud med kommandot GET.

```http
GET https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupOperations/68ccfbc1-a64f-4b29-b955-314b5790cfa9?api-version=2016-12-01
```

När åtgärden har slutförts returneras 200 (OK) med ID: t för det resulterande återställnings jobbet i svars texten.

```http
HTTP/1.1" 200
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Transfer-Encoding': 'chunked'
'Content-Type': 'application/json'
'Content-Encoding': 'gzip'
'Expires': '-1'
'Vary': 'Accept-Encoding'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': '41ee89b2-3be4-40d8-8ff6-f5592c2571e3'
'x-ms-client-request-id': '3c743096-47eb-11ea-ae90-0a580af41908, 3c743096-47eb-11ea-ae90-0a580af41908'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'Server': 'Microsoft-IIS/10.0'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-reads': '11998'
'x-ms-correlation-request-id': '41ee89b2-3be4-40d8-8ff6-f5592c2571e3'
'x-ms-routing-request-id': 'WESTEUROPE:20200205T074348Z:41ee89b2-3be4-40d8-8ff6-f5592c2571e3'
'Date': 'Wed, 05 Feb 2020 07:43:47 GMT'
{
  "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/a7e97e42-4e54-4d4b-b449-26fcf946f42c",
  "location": null,
  "name": "a7e97e42-4e54-4d4b-b449-26fcf946f42c",
  "properties": {
    "actionsInfo": [
      "Cancellable"
    ],
    "activityId": "3c743096-47eb-11ea-ae90-0a580af41908",
    "backupManagementType": "AzureStorage",
    "duration": "0:00:01.863098",
    "endTime": null,
    "entityFriendlyName": "azurefiles",
    "errorDetails": null,
    "extendedInfo": {
      "dynamicErrorMessage": null,
      "propertyBag": {},
      "tasksList": []
    },
    "jobType": "AzureStorageJob",
    "operation": "Restore",
    "startTime": "2020-02-05T07:43:47.144961+00:00",
    "status": "InProgress",
    "storageAccountName": "afsaccount",
    "storageAccountVersion": "Storage"
  },
  "resourceGroup": "azurefiles",
  "tags": null,
  "type": "Microsoft.RecoveryServices/vaults/backupJobs"
}
```

För alternativ plats återställning ser svars texten ut så här:

```http
{
  "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/7e0ee41e-6e31-4728-a25c-98ff6b777641",
  "location": null,
  "name": "7e0ee41e-6e31-4728-a25c-98ff6b777641",
  "properties": {
    "actionsInfo": [
      "Cancellable"
    ],
    "activityId": "6077be6e-483a-11ea-a915-0a580af4ad72",
    "backupManagementType": "AzureStorage",
    "duration": "0:00:02.171965",
    "endTime": null,
    "entityFriendlyName": "azurefiles",
    "errorDetails": null,
    "extendedInfo": {
      "dynamicErrorMessage": null,
      "propertyBag": {
        "Data Transferred (in MB)": "0",
        "Job Type": "Recover to an alternate file share",
        "Number Of Failed Files": "0",
        "Number Of Restored Files": "0",
        "Number Of Skipped Files": "0",
        "RestoreDestination": "afaccount1/azurefiles1/restoredata",
        "Source File Share Name": "azurefiles",
        "Source Storage Account Name": "afsaccount",
        "Target File Share Name": "azurefiles1",
        "Target Storage Account Name": "afaccount1"
      },
      "tasksList": []
    },
    "jobType": "AzureStorageJob",
    "operation": "Restore",
    "startTime": "2020-02-05T17:10:18.106532+00:00",
    "status": "InProgress",
    "storageAccountName": "afsaccount",
    "storageAccountVersion": "ClassicCompute"
  },
  "resourceGroup": "azurefiles",
  "tags": null,
  "type": "Microsoft.RecoveryServices/vaults/backupJobs"
}
```

Eftersom säkerhets kopierings jobbet är en tids krävande åtgärd bör det spåras som förklaras i [övervaknings jobben med REST API-dokument](./backup-azure-arm-userestapi-managejobs.md#tracking-the-job).

## <a name="item-level-recovery-using-rest-api"></a>Återställning på objekt nivå med REST API

Du kan använda det här alternativet för återställning för att återställa enskilda filer eller mappar på den ursprungliga platsen eller en annan plats.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints/{recoveryPointId}/restore?api-version=2019-05-13
```

Värdena {containerName} och {protectedItemName} anges [här](#fetch-containername-and-protecteditemname) och recoveryPointID är {Name}-fältet för den återställnings punkt som anges ovan.

```http
POST https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare%3Bazurefiles/recoveryPoints/932886657837421071/restore?api-version=2019-05-13'
```

### <a name="create-request-body-for-item-level-recovery-using-rest-api"></a>Skapa begär ande text för återställning på objekt nivå med REST API

För att utlösa en återställning för en Azure-filresurs är följande komponenter i begär ande texten:

Namn |  Typ   |   Beskrivning
--- | ---- | ----
Egenskaper | AzureFileShareRestoreRequest | Egenskaper för RestoreRequestResource

En fullständig lista över definitioner av begär ande texten och annan information finns i [Återställnings REST API dokumentet](/rest/api/backup/restores/trigger#request-body).

### <a name="restore-to-original-location-for-item-level-recovery-using-rest-api"></a>Återställ till den ursprungliga platsen för återställning på objekt nivå med REST API

Följande brödtext i begäran är att återställa *Restoretest.txt* -filen i *migreringsåtgärden* -filresursen i *afsaccount* Storage-kontot.

Skapa brödtext för begäran

```json
{
   "properties":{
      "objectType":"AzureFileShareRestoreRequest",
      "copyOptions":"Overwrite",
      "recoveryType":"OriginalLocation",
      "restoreFileSpecs":[
         {
            "fileSpecType":"File",
            "path":"RestoreTest.txt",
            "targetFolderPath":null
}
],
      "restoreRequestType":"ItemLevelRestore",
      "sourceResourceId":"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.storage/storageAccounts/afsaccount",
      "targetDetails":null
}
}
```

### <a name="restore-to-alternate-location-for-item-level-recovery-using-rest-api"></a>Återställ till en alternativ plats för återställning på objekt nivå med REST API

Följande brödtext i begäran är att återställa *Restoretest.txt* -filen i *migreringsåtgärden* -filresursen i *afsaccount* -lagrings kontot till mappen *restoredata* i *azurefiles1* -filresursen i *afaccount1* lagrings konto.

Skapa brödtext för begäran

```json
{
   "properties":{
      "objectType":"AzureFileShareRestoreRequest",
      "recoveryType":"AlternateLocation",
      "sourceResourceId":"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afsaccount",
      "copyOptions":"Overwrite",
      "restoreRequestType":"ItemLevelRestore",
      "restoreFileSpecs":[
         {
            "path":"Restore/RestoreTest.txt",
            "fileSpecType":"File",
            "targetFolderPath":"restoredata"
}
],
      "targetDetails":{
         "name":"azurefiles1",
         "targetResourceId":"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afaccount1"
}
}
}
```

Svaret ska hanteras på samma sätt som förklaras ovan för [fullständiga delnings](#full-share-recovery-using-rest-api)återställningar.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [hanterar säkerhets kopiering av Azure-filresurser med hjälp av REST API](manage-azure-file-share-rest-api.md).
