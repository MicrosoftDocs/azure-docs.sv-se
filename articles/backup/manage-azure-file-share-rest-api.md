---
title: Hantera säkerhets kopiering av Azure-filresurs med REST API
description: Lär dig hur du använder REST API för att hantera och övervaka Azure-filresurser som säkerhets kopie ras av Azure Backup.
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 5e2823472c6a7bdd6b3f9819db3079d7efa78c4e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88892855"
---
# <a name="manage-azure-file-share-backup-with-rest-api"></a>Hantera säkerhets kopiering av Azure-filresurs med REST API

Den här artikeln förklarar hur du utför uppgifter för att hantera och övervaka Azure-filresurser som säkerhets kopie ras av [Azure Backup](./backup-overview.md).

## <a name="monitor-jobs"></a>Övervaka jobb

Azure Backups tjänsten utlöser jobb som körs i bakgrunden. Detta inkluderar scenarier som utlöser säkerhets kopiering, återställnings åtgärder och inaktiverar säkerhets kopiering. Dessa jobb kan spåras med hjälp av deras ID.

### <a name="fetch-job-information-from-operations"></a>Hämta jobb information från åtgärder

En åtgärd som utlöser säkerhets kopiering returnerar alltid en jobID i svaret.

Till exempel är det slutliga svaret på en [säkerhets kopierings REST API](backup-azure-file-share-rest-api.md#trigger-an-on-demand-backup-for-file-share) åtgärd följande:

```json
{
    "id": "c3a52d1d-0853-4211-8141-477c65740264",
    "name": "c3a52d1d-0853-4211-8141-477c65740264",
    "status": "Succeeded",
    "startTime": "2020-02-03T18:10:48.296012Z",
    "endTime": "2020-02-03T18:10:48.296012Z",
    "properties": {
        "objectType": "OperationStatusJobExtendedInfo",
        "jobId": "e2ca2cf4-2eb9-4d4b-b16a-8e592d2a658b"
    }
}
```

Säkerhets kopierings jobbet för Azure-filresursen identifieras av **jobId** -fältet och kan spåras på det sätt som anges [här](/rest/api/backup/jobdetails/) med en get-begäran.

### <a name="tracking-the-job"></a>Spåra jobbet

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupJobs/{jobName}?api-version=2019-05-13
```

{JobName} är "jobId" som nämnts ovan. Svaret är alltid "200 OK" med fältet **status** och indikerar jobbets status. När det är "slutfört" eller "CompletedWithWarnings" visar avsnittet **extendedInfo** mer information om jobbet.

```http
GET https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/e2ca2cf4-2eb9-4d4b-b16a-8e592d2a658b?api-version=2019-05-13'
```

#### <a name="response"></a>Svarsåtgärder

Namn  | Typ  |  Beskrivning
--- | --- | ----
200 OK |  JobResource  | OK

#### <a name="response-example"></a>Exempel på svar

När *Hämta* URI har skickats returneras ett 200-svar.

```http
HTTP/1.1" 200
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Transfer-Encoding': 'chunked'
'Content-Type': 'application/json'
'Content-Encoding': 'gzip'
'Expires': '-1'
'Vary': 'Accept-Encoding'
'Server': 'Microsoft-IIS/10.0, Microsoft-IIS/10.0'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': 'dba43f00-5cdb-43b1-a9ec-23e419db67c5'
'x-ms-client-request-id': 'a644712a-4895-11ea-ba57-0a580af42708, a644712a-4895-11ea-ba57-0a580af42708'
'X-Powered-By': 'ASP.NET'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'x-ms-ratelimit-remaining-subscription-reads': '11999'
'x-ms-correlation-request-id': 'dba43f00-5cdb-43b1-a9ec-23e419db67c5'
'x-ms-routing-request-id': 'WESTEUROPE:20200206T040341Z:dba43f00-5cdb-43b1-a9ec-23e419db67c5'
'Date': 'Thu, 06 Feb 2020 04:03:40 GMT'
{
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/e2ca2cf4-2eb9-4d4b-b16a-8e592d2a658b",
    "name": "e2ca2cf4-2eb9-4d4b-b16a-8e592d2a658b",
    "type": "Microsoft.RecoveryServices/vaults/backupJobs",
    "properties": {
        "jobType": "AzureStorageJob",
        "duration": "00:00:43.1809140",
        "storageAccountName": "testvault2",
        "storageAccountVersion": "Storage",
        "extendedInfo": {
            "tasksList": [],
            "propertyBag": {
                "File Share Name": "testshare",
                "Storage Account Name": "testvault2",
                "Policy Name": "schedule1"
            }
        },
        "entityFriendlyName": "testshare",
        "backupManagementType": "AzureStorage",
        "operation": "ConfigureBackup",
        "status": "Completed",
        "startTime": "2020-02-03T18:10:48.296012Z",
        "endTime": "2020-02-03T18:11:31.476926Z",
        "activityId": "3677cec0-942d-4eac-921f-8f3c873140d7"
    }
}
```

## <a name="modify-policy"></a>Ändra princip

Om du vill ändra principen som fil resursen är skyddad med kan du använda samma format som att aktivera skydd. Ange bara det nya princip-ID: t i begär ande principen och skicka begäran.

Exempel: om du vill ändra skydds principen för *testshare* från *schedule1* till *schedule2* anger du *schedule2* -ID i begär ande texten.

```json
{
  "properties": {
    "protectedItemType": "AzureFileShareProtectedItem",
    "sourceResourceId": "/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
    "policyId": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupPolicies/schedule2"
  }
}
```

## <a name="stop-protection-but-retain-existing-data"></a>Stoppa skyddet men behåll befintliga data

Du kan ta bort skyddet för en skyddad fil resurs men Behåll de data som redan har säkerhetskopierats. Det gör du genom att ta bort principen i begär ande texten som du använde för att[Aktivera säkerhets kopiering](backup-azure-file-share-rest-api.md#enable-backup-for-the-file-share) och skicka begäran. När kopplingen med principen tas bort utlöses inte säkerhets kopieringarna och inga nya återställnings punkter skapas.

```json
{
  "properties": {
    "protectedItemType": "AzureFileShareProtectedItem",
    "sourceResourceId": "/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
    "policyId": “" ,
“protectionState”:”ProtectionStopped”
  }
}
```

### <a name="sample-response"></a>Exempelsvar

Att stoppa skyddet av en fil resurs är en asynkron åtgärd. Åtgärden skapar en annan åtgärd som måste spåras. Den returnerar två svar: 202 (accepterad) när en annan åtgärd skapas och 200 när åtgärden har slutförts.

Svars huvud när åtgärden har accepterats:

```http
HTTP/1.1" 202
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Expires': '-1'
'Location': 'https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/operationResults/b300922a-ad9c-4181-b4cd-d42ea780ad77?api-version=2019-05-13'
'Retry-After': '60'
msrest.http_logger :     'Azure-AsyncOperation': 'https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/operationsStatus/b300922a-ad9c-4181-b4cd-d42ea780ad77?api-version=2019-05-13'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': '3895e8a1-e4b9-4da5-bec7-2cf0266405f8'
'x-ms-client-request-id': 'd331c15e-48ab-11ea-84c0-0a580af46a50, d331c15e-48ab-11ea-84c0-0a580af46a50'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-writes': '1199'
'x-ms-correlation-request-id': '3895e8a1-e4b9-4da5-bec7-2cf0266405f8'
'x-ms-routing-request-id': 'WESTEUROPE:20200206T064224Z:3895e8a1-e4b9-4da5-bec7-2cf0266405f8'
'Date': 'Thu, 06 Feb 2020 06:42:24 GMT'
'Content-Length': '0'
```

Spåra sedan den resulterande åtgärden med hjälp av plats rubriken eller Azure-AsyncOperation huvud med ett GET-kommando:

```http
GET https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupoperations/b300922a-ad9c-4181-b4cd-d42ea780ad77?api-version=2016-12-01
```

### <a name="response-body"></a>Själva svaret

```json
{
    "id": "b300922a-ad9c-4181-b4cd-d42ea780ad77",
    "name": "b300922a-ad9c-4181-b4cd-d42ea780ad77",
    "status": "Succeeded",
    "startTime": "2020-02-06T06:42:24.4001299Z",
    "endTime": "2020-02-06T06:42:24.4001299Z",
    "properties": {
        "objectType": "OperationStatusJobExtendedInfo",
        "jobId": "7816fca8-d5be-4c41-b911-1bbd922e5826"
    }
}
```

## <a name="stop-protection-and-delete-data"></a>Stoppa skyddet och ta bort data

Ta bort skyddet på en skyddad fil resurs och ta bort säkerhetskopierade data också genom att utföra en borttagnings åtgärd som beskrivs [här](/rest/api/backup/protecteditems/delete).

```http
DELETE https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2019-05-13
```

Parametrarna {containerName} och {protectedItemName} anges [här](restore-azure-file-share-rest-api.md#fetch-containername-and-protecteditemname).

I följande exempel utlöses en åtgärd för att stoppa skyddet av *testshare* -filresursen som skyddas med *azurefilesvault*.

```http
DELETE https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/protectedItems/azurefileshare;testshare?api-version=2016-12-01
```

### <a name="responses"></a>Svar

Borttagning av skydd är en asynkron åtgärd. Åtgärden skapar en annan åtgärd som måste spåras separat.
Den returnerar två svar: 202 (accepterad) när en annan åtgärd skapas och 204 (inget innehåll) när åtgärden har slutförts.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [felsöker problem när du konfigurerar säkerhets kopiering för Azure-filresurser](troubleshoot-azure-files.md).
