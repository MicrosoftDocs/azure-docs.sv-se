---
title: Status för asynkrona åtgärder
description: Beskriver hur du spårar asynkrona åtgärder i Azure. Det visar de värden som du använder för att hämta status för en tids krävande åtgärd.
ms.topic: conceptual
ms.date: 08/21/2020
ms.custom: seodec18
ms.openlocfilehash: e2c5ba137d5277466cf1b382d2b0b1bc02259f00
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88723460"
---
# <a name="track-asynchronous-azure-operations"></a>Spåra asynkrona Azure-åtgärder

Vissa Azure REST-åtgärder körs asynkront eftersom åtgärden inte kan slutföras snabbt. Den här artikeln beskriver hur du spårar statusen för asynkrona åtgärder genom värden som returneras i svaret.  

## <a name="status-codes-for-asynchronous-operations"></a>Status koder för asynkrona åtgärder

En asynkron åtgärd returnerar inlednings vis HTTP-status kod för antingen:

* 201 (skapat)
* 202 (accepterad)

När åtgärden har slutförts returneras antingen:

* 200 (OK)
* 204 (inget innehåll)

Läs REST API- [dokumentationen](/rest/api/azure/) om du vill se svaren för den åtgärd som du kör.

När du har hämtat 201-eller 202-svars koden är du redo att övervaka status för åtgärden.

## <a name="url-to-monitor-status"></a>URL för att övervaka status

Det finns två olika sätt att övervaka statusen för den asynkrona åtgärden. Du fastställer rätt metod genom att undersöka de huvud värden som returneras från din ursprungliga begäran. Börja med att leta efter:

* `Azure-AsyncOperation` -URL för att kontrol lera den pågående statusen för åtgärden. Om åtgärden returnerar det här värdet använder du det för att spåra status för åtgärden.
* `Retry-After` -Antalet sekunder som ska förflyta innan den asynkrona åtgärdens status kontrol leras.

Om `Azure-AsyncOperation` inte är ett av rubrik värden söker du efter:

* `Location` -URL för att avgöra när en åtgärd har slutförts. Använd endast det här värdet när Azure-AsyncOperation inte returneras.
* `Retry-After` -Antalet sekunder som ska förflyta innan den asynkrona åtgärdens status kontrol leras.

## <a name="azure-asyncoperation-request-and-response"></a>Azure-AsyncOperation begäran och svar

Om du har en URL från `Azure-AsyncOperation` Head-värdet skickar du en get-begäran till den URL: en. Använd värdet från `Retry-After` för att schemalägga hur ofta du vill kontrol lera statusen. Du får ett svars objekt som visar status för åtgärden. Ett annat svar returneras när du kontrollerar status för åtgärden med `Location` URL: en. Mer information om svaret från en plats-URL finns i [skapa lagrings konto (202 med plats och försök igen)](#create-storage-account-202-with-location-and-retry-after).

Svars egenskaperna kan variera, men innehåller alltid status för den asynkrona åtgärden.

```json
{
    "status": "{status-value}"
}
```

I följande exempel visas andra värden som kan returneras från åtgärden:

```json
{
    "id": "{resource path from GET operation}",
    "name": "{operation-id}",
    "status" : "Succeeded | Failed | Canceled | {resource provider values}",
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
    "percentComplete": {double between 0 and 100 },
    "properties": {
        /* Specific resource provider values for successful operations */
    },
    "error" : {
        "code": "{error code}",  
        "message": "{error description}"
    }
}
```

Objektet Error returneras när status är misslyckad eller avbruten. Alla andra värden är valfria. Svaret du får ser annorlunda ut än exemplet.

## <a name="provisioningstate-values"></a>provisioningState-värden

Åtgärder för att skapa, uppdatera eller ta bort (skicka, korrigera, ta bort) en resurs returnerar vanligt vis ett `provisioningState` värde. När en åtgärd har slutförts returneras något av följande tre värden:

* Lyckades
* Misslyckad
* Avbrutna

Alla andra värden anger att åtgärden fortfarande körs. Resurs leverantören kan returnera ett anpassat värde som anger dess tillstånd. Du kan till exempel ta emot **godkännande** när begäran tas emot och körs.

## <a name="example-requests-and-responses"></a>Exempel begär Anden och svar

### <a name="start-virtual-machine-202-with-azure-asyncoperation"></a>Starta den virtuella datorn (202 med Azure-AsyncOperation)

Det här exemplet visar hur du fastställer statusen för [Start åtgärden för virtuella datorer](/rest/api/compute/virtualmachines/start). Den första begäran har följande format:

```HTTP
POST 
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Compute/virtualMachines/{vm-name}/start?api-version=2019-12-01
```

Den returnerar status koden 202. Mellan rubrik värden visas:

```HTTP
Azure-AsyncOperation : https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/locations/{region}/operations/{operation-id}?api-version=2019-12-01
```

Om du vill kontrol lera statusen för den asynkrona åtgärden skickar du en annan begäran till den URL: en.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/locations/{region}/operations/{operation-id}?api-version=2019-12-01
```

Svars texten innehåller status för åtgärden:

```json
{
  "startTime": "2017-01-06T18:58:24.7596323+00:00",
  "status": "InProgress",
  "name": "9a062a88-e463-4697-bef2-fe039df73a02"
}
```

### <a name="deploy-resources-201-with-azure-asyncoperation"></a>Distribuera resurser (201 med Azure-AsyncOperation)

Det här exemplet visar hur du fastställer statusen för [distributioner för](/rest/api/resources/deployments/createorupdate) distribution av resurser till Azure. Den första begäran har följande format:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/microsoft.resources/deployments/{deployment-name}?api-version=2020-06-01
```

Den returnerar status koden 201. Bröd texten i svaret innehåller:

```json
"provisioningState":"Accepted",
```

Mellan rubrik värden visas:

```HTTP
Azure-AsyncOperation: https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Resources/deployments/{deployment-name}/operationStatuses/{operation-id}?api-version=2020-06-01
```

Om du vill kontrol lera statusen för den asynkrona åtgärden skickar du en annan begäran till den URL: en.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Resources/deployments/{deployment-name}/operationStatuses/{operation-id}?api-version=2020-06-01
```

Svars texten innehåller status för åtgärden:

```json
{
    "status": "Running"
}
```

När distributionen är färdig innehåller svaret:

```json
{
    "status": "Succeeded"
}
```

### <a name="create-storage-account-202-with-location-and-retry-after"></a>Skapa lagrings konto (202 med plats och försök igen efter)

Det här exemplet visar hur du fastställer status för [skapa-åtgärden för lagrings konton](/rest/api/storagerp/storageaccounts/create). Den första begäran har följande format:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}?api-version=2019-06-01
```

Och begär ande texten innehåller egenskaper för lagrings kontot:

```json
{
    "location": "South Central US",
    "properties": {},
    "sku": {
        "name": "Standard_LRS"
    },
    "kind": "Storage"
}
```

Den returnerar status koden 202. Mellan rubrik värden visas följande två värden:

```HTTP
Location: https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Storage/operations/{operation-id}?monitor=true&api-version=2019-06-01
Retry-After: 17
```

Efter att ha väntat i antal sekunder som anges i försök igen, kontrollerar du statusen för den asynkrona åtgärden genom att skicka en annan begäran till den URL: en.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Storage/operations/{operation-id}?monitor=true&api-version=2019-06-01
```

Om begäran fortfarande körs får du en status kod 202. Om begäran har slutförts får du en status kod 200 och bröd texten i svaret innehåller egenskaperna för det lagrings konto som har skapats.

## <a name="next-steps"></a>Nästa steg

* Dokumentation om varje REST-åtgärd finns i [REST API-dokumentationen](/rest/api/azure/).
* Information om hur du distribuerar mallar via Resource Manager REST API finns i [distribuera resurser med Resource Manager-mallar och Resource manager REST API](../templates/deploy-rest.md).