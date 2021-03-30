---
title: Säkerhetskopiera Azure-filresurser med REST API
description: Lär dig hur du använder REST API för att säkerhetskopiera Azure-filresurser i Recovery Services-valvet
ms.topic: conceptual
ms.date: 02/16/2020
ms.openlocfilehash: 8d2d8ed88da133986540a293185c8e37000ab87b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88824873"
---
# <a name="backup-azure-file-share-using-azure-backup-via-rest-api"></a>Säkerhetskopiera Azure-filresurs med Azure Backup via REST API

Den här artikeln beskriver hur du säkerhetskopierar en Azure-filresurs med hjälp av Azure Backup via REST API.

Den här artikeln förutsätter att du redan har skapat ett Recovery Services valv och en princip för att konfigurera säkerhets kopiering för din fil resurs. Om du inte har det kan du läsa avsnittet [skapa valv](./backup-azure-arm-userestapi-createorupdatevault.md) och [Skapa princip](./backup-azure-arm-userestapi-createorupdatepolicy.md) REST API själv studie kurser för att skapa nya valv och principer.

I den här artikeln använder vi följande resurser:

- **RecoveryServicesVault**: *azurefilesvault*

- **Princip:** *schedule1*

- **Resurs grupp**: *migreringsåtgärden*

- **Lagrings konto**: *testvault2*

- **Fil resurs**: *testshare*

## <a name="configure-backup-for-an-unprotected-azure-file-share-using-rest-api"></a>Konfigurera säkerhets kopiering för en oskyddad Azure-filresurs med hjälp av REST API

### <a name="discover-storage-accounts-with-unprotected-azure-file-shares"></a>Identifiera lagrings konton med oskyddade Azure-filresurser

Valvet måste identifiera alla Azure Storage-konton i prenumerationen med fil resurser som kan säkerhets kopie ras till Recovery Services valvet. Detta utlöses med hjälp av [uppdaterings åtgärden](/rest/api/backup/protectioncontainers/refresh). Det är en asynkron *post* -åtgärd som garanterar att valvet får den senaste listan över alla oskyddade Azure-filresurser i den aktuella prenumerationen och cachelagrar dem. När fil resursen är "cachelagrad" kan återställnings tjänsten komma åt fil resursen och skydda den.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupname}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/refreshContainers?api-version=2016-12-01&$filter={$filter}
```

POST-URI: n har `{subscriptionId}` parametrarna,, `{vaultName}` `{vaultresourceGroupName}` och `{fabricName}` . I vårt exempel blir värdet för de olika parametrarna följande:

- `{fabricName}` är *Azure*

- `{vaultName}` är *azurefilesvault*

- `{vaultresourceGroupName}` är *migreringsåtgärden*

- $filter = backupManagementType EQ ' AzureStorage '

Eftersom alla nödvändiga parametrar har angetts i URI: n behöver du inte ha någon separat brödtext för begäran.

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/refreshContainers?api-version=2016-12-01&$filter=backupManagementType eq 'AzureStorage'
```

#### <a name="responses-to-the-refresh-operation"></a>Svar på uppdaterings åtgärden

Åtgärden Uppdatera är en [asynkron åtgärd](../azure-resource-manager/management/async-operations.md). Det innebär att den här åtgärden skapar en annan åtgärd som måste spåras separat.

Den returnerar två svar: 202 (accepterad) när en annan åtgärd skapas och 200 (OK) när åtgärden har slutförts.

##### <a name="example-responses-to-the-refresh-operation"></a>Exempel svar på uppdaterings åtgärden

När *post* -begäran har skickats returneras ett 202-svar (accepterat).

```http
HTTP/1.1 202 Accepted
'Pragma': 'no-cache'
'Expires': '-1'
'Location': ‘https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/ResourceGroups
/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/operationResults/
cca47745-12d2-42f9-b3a4-75335f18fdf6?api-version=2016-12-01’
'Retry-After': '60'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': '6cc12ceb-90a2-430d-a1ec-9b6b6fdea92b'
'x-ms-client-request-id': ‘3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61’
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-reads': '11996'
'x-ms-correlation-request-id': '6cc12ceb-90a2-430d-a1ec-9b6b6fdea92b'
'x-ms-routing-request-id': CENTRALUSEUAP:20200203T091326Z:6cc12ceb-90a2-430d-a1ec-9b6b6fdea92b'
'Date': 'Mon, 03 Feb 2020 09:13:25 GMT'
```

Spåra den resulterande åtgärden med hjälp av rubriken "location" med ett enkelt *Get* -kommando

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/operationResults/cca47745-12d2-42f9-b3a4-75335f18fdf6?api-version=2016-12-01
```

När alla Azure Storage-konton identifieras returnerar kommandot GET ett 200-svar (inget innehåll). Valvet kan nu identifiera lagrings konto med fil resurser som kan säkerhets kopie ras i prenumerationen.

```http
HTTP/1.1 200 NoContent
Cache-Control  : no-cache
Pragma   : no-cache
X-Content-Type-Options  : nosniff
x-ms-request-id    : d9bdb266-8349-4dbd-9688-de52f07648b2
x-ms-client-request-id  : 3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61
Strict-Transport-Security  : max-age=31536000; includeSubDomains
X-Powered-By    : ASP.NET
x-ms-ratelimit-remaining-subscription-reads: 11933
x-ms-correlation-request-id   : d9bdb266-8349-4dbd-9688-de52f07648b2
x-ms-routing-request-id  : CENTRALUSEUAP:20200127T105304Z:d9bdb266-8349-4dbd-9688-de52f07648b2
Date   : Mon, 27 Jan 2020 10:53:04 GMT
```

### <a name="get-list-of-storage-accounts-with-file-shares-that-can-be-backed-up-with-recovery-services-vault"></a>Hämta en lista över lagrings konton med fil resurser som kan säkerhets kopie ras med Recovery Services Vault

För att bekräfta att "cachelagring" görs, visar alla lagrings konton i prenumerationen med fil resurser som kan säkerhets kopie ras med Recovery Services valvet. Leta sedan reda på det önskade lagrings kontot i svaret. Detta görs med hjälp av åtgärden [Get ProtectableContainers](/rest/api/backup/protectablecontainers/list) .

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectableContainers?api-version=2016-12-01&$filter=backupManagementType eq 'AzureStorage'
```

*Hämta* URI har alla nödvändiga parametrar. Ingen ytterligare begär ande text krävs.

Exempel på svars text:

```json
{

  "value": [

    {

      "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers
 /Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/

protectableContainers/StorageContainer;Storage;AzureFiles;testvault2",

      "name": "StorageContainer;Storage;AzureFiles;testvault2",

      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectableContainers",

      "properties": {

        "friendlyName": "testvault2",

        "backupManagementType": "AzureStorage",

        "protectableContainerType": "StorageContainer",

        "healthStatus": "Healthy",

        "containerId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/
 AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2"

      }

    }

  ]

}
```

Eftersom vi kan hitta *testvault2* lagrings konto i svars texten med det egna namnet, lyckades uppdaterings åtgärden som utfördes ovan. Recovery Services-valvet kan nu identifiera lagrings konton med oskyddade fil resurser i samma prenumeration.

### <a name="register-storage-account-with-recovery-services-vault"></a>Registrera lagrings konto med Recovery Services Vault

Det här steget behövs bara om du inte registrerade lagrings kontot med valvet tidigare. Du kan registrera valvet via [åtgärden ProtectionContainers-register](/rest/api/backup/protectioncontainers/register).

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}?api-version=2016-12-01
```

Ange variablerna för URI: n enligt följande:

- {resourceGroupName}- *migreringsåtgärden*
- {fabricName} – *Azure*
- {vaultName}- *azurefilesvault*
- {containerName} – det här är namnattributet i svars texten för GET ProtectableContainers-åtgärden.
   I vårt exempel är det *StorageContainer; Lagrings Migreringsåtgärden; testvault2*

>[!NOTE]
> Ta alltid namnattributet för svaret och fyll i det i den här begäran. Du behöver inte hårdkoda eller skapa behållar namns formatet. Om du skapar eller hårdkodar den kommer API-anropet att Miss lägeas om behållar namns formatet ändras i framtiden.

<br>

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2?api-version=2016-12-01
```

Texten för att skapa begäran är följande:

```json
{

 "properties": {


  "containerType": "StorageContainer",


  "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",


  "resourceGroup": "AzureFiles",


  "friendlyName": "testvault2",


  "backupManagementType": "AzureStorage"


 }
```

En fullständig lista över definitioner av begär ande texten och annan information finns i [ProtectionContainers-register](/rest/api/backup/protectioncontainers/register#azurestoragecontainer).

Detta är en asynkron åtgärd och returnerar två svar: "202 accepterad" när åtgärden godkänns och "200 OK" när åtgärden har slutförts.  Om du vill spåra åtgärdens status använder du plats rubriken för att hämta den senaste statusen för åtgärden.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/operationresults/1a3c8ee7-e0e5-43ed-b8b3-73cc992b6db9?api-version=2016-12-01
```

Exempel på svars text när åtgärden har slutförts:

```json
{
    "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/
protectionContainers/StorageContainer;Storage;AzureFiles;testvault2",
    "name": "StorageContainer;Storage;AzureFiles;testvault2",
    "properties": {
        "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
        "protectedItemCount": 0,
        "friendlyName": "testvault2",
        "backupManagementType": "AzureStorage",
        "registrationStatus": "Registered",
        "healthStatus": "Healthy",
        "containerType": "StorageContainer",
        "protectableObjectType": "StorageContainer"
    }
}
```

Du kan kontrol lera om registreringen lyckades från värdet för parametern *registrationstatus* i svars texten. I vårt fall visar den status som registrerad för *testvault2*, så Registreringen lyckades.

### <a name="inquire-all-unprotected-files-shares-under-a-storage-account"></a>Fråga alla oskyddade fil resurser under ett lagrings konto

Du kan fråga om skydds bara objekt i ett lagrings konto med hjälp av [skydds behållare – fråga](/rest/api/backup/protectioncontainers/inquire) åtgärd. Det är en asynkron åtgärd och resultaten bör spåras med hjälp av plats rubriken.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/inquire?api-version=2016-12-01
```

Ange variablerna för ovanstående URI enligt följande:

- {vaultName}- *azurefilesvault*
- {fabricName} – *Azure*
- {containerName} – referera till namnattributet i svars texten för åtgärden Hämta ProtectableContainers. I vårt exempel är det *StorageContainer; Lagrings Migreringsåtgärden; testvault2*

```http
https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/inquire?api-version=2016-12-01
```

När begäran har slutförts returneras status koden "OK"

```http
Cache-Control : no-cache
Pragma   : no-cache
X-Content-Type-Options: nosniff
x-ms-request-id  : 68727f1e-b8cf-4bf1-bf92-8e03a9d96c46
x-ms-client-request-id  : 3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61
Strict-Transport-Security: max-age=31536000; includeSubDomains
Server  : Microsoft-IIS/10.0
X-Powered-B : ASP.NET
x-ms-ratelimit-remaining-subscription-reads: 11932
x-ms-correlation-request-id  : 68727f1e-b8cf-4bf1-bf92-8e03a9d96c46
x-ms-routing-request-id   : CENTRALUSEUAP:20200127T105305Z:68727f1e-b8cf-4bf1-bf92-8e03a9d96c46
Date  : Mon, 27 Jan 2020 10:53:05 GMT
```

### <a name="select-the-file-share-you-want-to-back-up"></a>Välj den fil resurs som du vill säkerhetskopiera

Du kan visa en lista över alla objekt som kan skyddas under prenumerationen och hitta önskad fil resurs som ska säkerhets kopie ras med hjälp av åtgärden [Hämta backupprotectableItems](/rest/api/backup/backupprotectableitems/list) .

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupProtectableItems?api-version=2016-12-01&$filter={$filter}
```

Konstruera URI: n enligt följande:

- {vaultName}- *azurefilesvault*
- {$filter}- *backupManagementType EQ ' AzureStorage '*

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupProtectableItems?$filter=backupManagementType eq 'AzureStorage'&api-version=2016-12-01
```

Exempel svar:

```json
Status Code:200

{
    "value": [
        {
            "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;afaccount1/protectableItems/azurefileshare;azurefiles1",
            "name": "azurefileshare;azurefiles1",
            "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectableItems",
            "properties": {
                "parentContainerFabricId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afaccount1",
                "parentContainerFriendlyName": "afaccount1",
                "azureFileShareType": "XSMB",
                "backupManagementType": "AzureStorage",
                "workloadType": "AzureFileShare",
                "protectableItemType": "AzureFileShare",
                "friendlyName": "azurefiles1",
                "protectionState": "NotProtected"
            }
        },
        {
            "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;afsaccount/protectableItems/azurefileshare;afsresource",
            "name": "azurefileshare;afsresource",
            "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectableItems",
            "properties": {
                "parentContainerFabricId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afsaccount",
                "parentContainerFriendlyName": "afsaccount",
                "azureFileShareType": "XSMB",
                "backupManagementType": "AzureStorage",
                "workloadType": "AzureFileShare",
                "protectableItemType": "AzureFileShare",
                "friendlyName": "afsresource",
                "protectionState": "NotProtected"
            }
        },
        {
            "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;testvault2/protectableItems/azurefileshare;testshare",
            "name": "azurefileshare;testshare",
            "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectableItems",
            "properties": {
                "parentContainerFabricId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
                "parentContainerFriendlyName": "testvault2",
                "azureFileShareType": "XSMB",
                "backupManagementType": "AzureStorage",
                "workloadType": "AzureFileShare",
                "protectableItemType": "AzureFileShare",
                "friendlyName": "testshare",
                "protectionState": "NotProtected"
            }
        }
    ]
}
```

Svaret innehåller en lista över alla oskyddade fil resurser och innehåller all information som krävs av Azure Recovery Service för att konfigurera säkerhets kopieringen.

### <a name="enable-backup-for-the-file-share"></a>Aktivera säkerhets kopiering för fil resursen

När den relevanta fil resursen har identifierats med det egna namnet väljer du den princip som du vill skydda. Mer information om befintliga principer i valvet finns i [lista över princip-API](/rest/api/backup/backuppolicies/list). Välj sedan den [aktuella principen](/rest/api/backup/protectionpolicies/get) genom att referera till princip namnet. Information om hur du skapar principer finns i [själv studie kursen skapa princip](./backup-azure-arm-userestapi-createorupdatepolicy.md).

Aktivering av skydd är en *asynkron åtgärd* som skapar ett "skyddat objekt".

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2019-05-13
```

Ange variablerna **ContainerName** och **protecteditemname** med ID-attributet i svars texten för åtgärden get backupprotectableitems.

I vårt exempel är det ID för fil resurs som vi vill skydda:

```output
"/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;testvault2/protectableItems/azurefileshare;testshare
```

- {ContainerName}- *storagecontainer; Storage; migreringsåtgärden; testvault2*
- {protectedItemName}- *azurefileshare; testshare*

Eller så kan du referera till **namnattributet för** skydds behållaren och svar på objekt som kan skyddas.

>[!NOTE]
>Ta alltid namnattributet för svaret och fyll i det i den här begäran. Hårdkoda eller skapa inte formatet för behållar namn eller skyddat objekt namn. Om du skapar eller hårdkodar den kommer API-anropet att Miss lägeas om behållar formatet eller det skyddade objekt namnet ändras i framtiden.

<br>

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/protectedItems/azurefileshare;testshare?api-version=2016-12-01
```

Skapa en begär ande text:

Följande begär ande text definierar egenskaper som krävs för att skapa ett skyddat objekt.

```json
{
  "properties": {
    "protectedItemType": "AzureFileShareProtectedItem",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
    "policyId": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupPolicies/schedule1"
  }
}
```

**SourceResourceId** är **parentcontainerFabricID** som svar på Get backupprotectableItems.

Exempelsvar

Att skapa ett skyddat objekt är en asynkron åtgärd som skapar en annan åtgärd som måste spåras. Den returnerar två svar: 202 (accepterad) när en annan åtgärd skapas och 200 (OK) när åtgärden har slutförts.

När du skickar in *begäran om att skapa* eller uppdatera skyddade objekt är det första svaret 202 (accepteras) med ett plats huvud.

```http
HTTP/1.1 202 Accepted
Cache-Control  : no-cache
Pragma  : no-cache
Location : https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/protectedItems/azurefileshare;testshare/operationResults/c3a52d1d-0853-4211-8141-477c65740264?api-version=2016-12-01
Retry-Afte  : 60
Azure-AsyncOperation  : https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/protectedItems/azurefileshare;testshare/operationResults/c3a52d1d-0853-4211-8141-477c65740264?api-version=2016-12-01
X-Content-Type-Options : nosniff
x-ms-request-id : b55527fa-f473-4f09-b169-9cc3a7a39065
x-ms-client-request-id: 3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61
Strict-Transport-Security : max-age=31536000; includeSubDomains
X-Powered-By  : ASP.NET
x-ms-ratelimit-remaining-subscription-writes: 1198
x-ms-correlation-request-id : b55527fa-f473-4f09-b169-9cc3a7a39065
x-ms-routing-request-id  : CENTRALUSEUAP:20200127T105412Z:b55527fa-f473-4f09-b169-9cc3a7a39065
Date : Mon, 27 Jan 2020 10:54:12 GMT
```

Spåra sedan den resulterande åtgärden med hjälp av plats rubriken eller Azure-AsyncOperation huvud med kommandot  *Get* .

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupOperations/c3a52d1d-0853-4211-8141-477c65740264?api-version=2016-12-01
```

När åtgärden har slutförts returneras 200 (OK) med det skyddade objekt innehållet i svars texten.

Exempel på svars text:

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

Detta bekräftar att skyddet har Aktiver ATS för fil resursen och att den första säkerhets kopieringen kommer att utlösas enligt princip schemat.

## <a name="trigger-an-on-demand-backup-for-file-share"></a>Utlös en säkerhets kopiering på begäran för fil resurs

När en Azure-filresurs har kon figurer ATS för säkerhets kopiering körs säkerhets kopieringar enligt princip schemat. Du kan vänta på den första schemalagda säkerhets kopieringen eller utlösa en säkerhets kopiering på begäran när som helst.

Att utlösa en säkerhets kopiering på begäran är en POST-åtgärd.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/backup?api-version=2016-12-01
```

{containerName} och {protectedItemName} har skapats ovan samtidigt som säkerhets kopieringen aktive ras. I vårt exempel översätts detta till:

```http
POST https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/backup?api-version=2017-07-01
```

### <a name="create-request-body"></a>Skapa brödtext för begäran

Om du vill utlösa en säkerhets kopiering på begäran, följer du komponenterna i begär ande texten.

| Namn       | Typ                       | Beskrivning                       |
| ---------- | -------------------------- | --------------------------------- |
| Egenskaper | AzurefilesharebackupReques | Egenskaper för BackupRequestResource |

En fullständig lista över definitioner av begär ande texten och annan information finns i [Utlös säkerhets kopiering för skyddade objekt REST API dokument](/rest/api/backup/backups/trigger#request-body).

Exempel på begär ande text

```json
{

  "properties":{

   "objectType":"AzureFileShareBackupRequest",
    "recoveryPointExpiryTimeInUTC":"2020-03-07T18:29:59.000Z"
}

}
```

### <a name="responses-to-the-on-demand-backup-operation"></a>Svar på säkerhets kopierings åtgärden på begäran

Att utlösa en säkerhets kopiering på begäran är en [asynkron åtgärd](../azure-resource-manager/management/async-operations.md). Det innebär att den här åtgärden skapar en annan åtgärd som måste spåras separat.

Den returnerar två svar: 202 (accepterad) när en annan åtgärd skapas och 200 (OK) när åtgärden har slutförts.

### <a name="example-responses-to-the-on-demand-backup-operation"></a>Exempel svar på säkerhets kopierings åtgärden på begäran

När du har skickat in *post* -begäran för en säkerhets kopiering på begäran är det första svaret 202 (accepteras) med ett plats huvud eller Azure-async-header.

```http
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Expires': '-1'
'Location': https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/operationResults/dc62d524-427a-4093-968d-e951c0a0726e?api-version=2017-07-01
'Retry-After': '60'
'Azure-AsyncOperation': https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/operationsStatus/dc62d524-427a-4093-968d-e951c0a0726e?api-version=2017-07-01
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': '2e03b8d4-66b1-48cf-8094-aa8bff57e8fb'
'x-ms-client-request-id': 'a644712a-4895-11ea-ba57-0a580af42708, a644712a-4895-11ea-ba57-0a580af42708'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-writes': '1199'
'x-ms-correlation-request-id': '2e03b8d4-66b1-48cf-8094-aa8bff57e8fb'
'x-ms-routing-request-id': 'WESTEUROPE:20200206T040339Z:2e03b8d4-66b1-48cf-8094-aa8bff57e8fb'
'Date': 'Thu, 06 Feb 2020 04:03:38 GMT'
'Content-Length': '0'
```

Spåra sedan den resulterande åtgärden med hjälp av plats rubriken eller Azure-AsyncOperation huvud med kommandot  *Get* .

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupOperations/dc62d524-427a-4093-968d-e951c0a0726e?api-version=2016-12-01
```

När åtgärden har slutförts returneras 200 (OK) med ID: t för det resulterande säkerhets kopierings jobbet i svars texten.

#### <a name="sample-response-body"></a>Exempel på svars text

```json
{
    "id": "dc62d524-427a-4093-968d-e951c0a0726e",
    "name": "dc62d524-427a-4093-968d-e951c0a0726e",
    "status": "Succeeded",
    "startTime": "2020-02-06T11:06:02.1327954Z",
    "endTime": "2020-02-06T11:06:02.1327954Z",
    "properties": {
        "objectType": "OperationStatusJobExtendedInfo",
        "jobId": "39282261-cb52-43f5-9dd0-ffaf66beeaef"
    }
}
```

Eftersom säkerhets kopierings jobbet är en tids krävande åtgärd måste det spåras enligt beskrivningen i [övervaknings jobben med REST API-dokument](./backup-azure-arm-userestapi-managejobs.md#tracking-the-job).

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [återställer Azure-filresurser med hjälp av REST API](restore-azure-file-share-rest-api.md).
