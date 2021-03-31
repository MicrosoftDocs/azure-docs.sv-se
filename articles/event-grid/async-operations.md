---
title: Status för Event Grid asynkrona åtgärder
description: Beskriver hur du spårar Event Grid asynkrona åtgärder i Azure. Det visar de värden som du använder för att hämta status för en tids krävande åtgärd.
ms.topic: conceptual
ms.date: 07/07/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: baae7b097a0b696d405c0e7ea3d3bdeb326f23b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "89011692"
---
# <a name="track-event-grid-asynchronous-azure-operations"></a>Spåra Event Grid asynkrona Azure-åtgärder
Vissa Azure REST-åtgärder körs asynkront eftersom åtgärden inte kan slutföras snabbt. Den här artikeln beskriver hur du spårar statusen för asynkrona åtgärder genom värden som returneras i svaret.  

## <a name="status-codes-for-asynchronous-operations"></a>Status koder för asynkrona åtgärder
En asynkron åtgärd returnerar inlednings vis HTTP-status kod för antingen:

* 201 (skapat)
* 202 (accepterad) 

När åtgärden har slutförts returneras antingen:

* 200 (OK)
* 204 (inget innehåll) 

Läs REST API- [dokumentationen](/rest/api/) om du vill se svaren för den åtgärd som du kör.

## <a name="monitor-status-of-operation"></a>Övervaka status för åtgärd
De asynkrona REST-åtgärderna returnerar huvud värden som du kan använda för att fastställa status för åtgärden. Det finns potentiellt tre huvud värden att undersöka:

* `Azure-AsyncOperation` -URL för att kontrol lera den pågående statusen för åtgärden. Om åtgärden returnerar detta värde ska du alltid använda det (i stället för plats) för att spåra status för åtgärden.
* `Location` -URL för att avgöra när en åtgärd har slutförts. Använd endast det här värdet när Azure-AsyncOperation inte returneras.
* `Retry-After` -Antalet sekunder som ska förflyta innan den asynkrona åtgärdens status kontrol leras.

Men alla asynkrona åtgärder returnerar alla dessa värden. Du kan till exempel behöva utvärdera Azure-AsyncOperation huvud värde för en åtgärd och värdet för plats huvudet för en annan åtgärd. 

Du hämtar rubrik värden på samma sätt som du hämtar alla huvud värden för en begäran. I C# hämtar du till exempel huvudet från ett `HttpWebResponse` objekt med namnet `response` med följande kod:

```cs
response.Headers.GetValues("Azure-AsyncOperation").GetValue(0)
```

## <a name="azure-asyncoperation-request-and-response"></a>Azure-AsyncOperation begäran och svar

Om du vill hämta statusen för den asynkrona åtgärden skickar du en GET-begäran till URL: en i Azure-AsyncOperation huvud värde.

Bröd texten i svaret från den här åtgärden innehåller information om åtgärden. I följande exempel visas möjliga värden som returneras från åtgärden:

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

Endast `status` returneras för alla svar. Objektet Error returneras när status är misslyckad eller avbruten. Alla andra värden är valfria. det svar du får ser därför annorlunda ut än exemplet.

## <a name="provisioningstate-values"></a>provisioningState-värden

Åtgärder för att skapa, uppdatera eller ta bort (skicka, korrigera, ta bort) en resurs returnerar vanligt vis ett `provisioningState` värde. När en åtgärd har slutförts returneras något av följande tre värden: 

* Lyckades
* Misslyckad
* Avbrutna

Alla andra värden anger att åtgärden fortfarande körs. Resurs leverantören kan returnera ett anpassat värde som anger dess tillstånd. Du kan till exempel ta emot **godkännande** när begäran tas emot och körs.


## <a name="example-requests-and-responses"></a>Exempel begär Anden och svar

### <a name="start-virtual-machine-202-with-azure-asyncoperation"></a>Starta den virtuella datorn (202 med Azure-AsyncOperation)
Det här exemplet visar hur du fastställer statusen för **Start** åtgärden för virtuella datorer. Den första begäran har följande format:

```HTTP
POST 
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Compute/virtualMachines/{vm-name}/start?api-version=2016-03-30
```

Den returnerar status koden 202. Mellan rubrik värden visas:

```HTTP
Azure-AsyncOperation : https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/locations/{region}/operations/{operation-id}?api-version=2016-03-30
```

Om du vill kontrol lera statusen för den asynkrona åtgärden skickar du en annan begäran till den URL: en.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/locations/{region}/operations/{operation-id}?api-version=2016-03-30
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

Det här exemplet visar hur du fastställer statusen för **distributioner** för distribution av resurser till Azure. Den första begäran har följande format:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/microsoft.resources/deployments/{deployment-name}?api-version=2016-09-01
```

Den returnerar status koden 201. Bröd texten i svaret innehåller:

```json
"provisioningState":"Accepted",
```

Mellan rubrik värden visas:

```HTTP
Azure-AsyncOperation: https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Resources/deployments/{deployment-name}/operationStatuses/{operation-id}?api-version=2016-09-01
```

Om du vill kontrol lera statusen för den asynkrona åtgärden skickar du en annan begäran till den URL: en.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Resources/deployments/{deployment-name}/operationStatuses/{operation-id}?api-version=2016-09-01
```

Svars texten innehåller status för åtgärden:

```json
{"status":"Running"}
```

När distributionen är färdig innehåller svaret:

```json
{"status":"Succeeded"}
```

### <a name="create-storage-account-202-with-location-and-retry-after"></a>Skapa lagrings konto (202 med plats och försök igen efter)

Det här exemplet visar hur du fastställer status för **skapa** -åtgärden för lagrings konton. Den första begäran har följande format:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}?api-version=2016-01-01
```

Och begär ande texten innehåller egenskaper för lagrings kontot:

```json
{ "location": "South Central US", "properties": {}, "sku": { "name": "Standard_LRS" }, "kind": "Storage" }
```

Den returnerar status koden 202. Mellan rubrik värden visas följande två värden:

```HTTP
Location: https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Storage/operations/{operation-id}?monitor=true&api-version=2016-01-01
Retry-After: 17
```

Efter att ha väntat i antal sekunder som anges i försök igen, kontrollerar du statusen för den asynkrona åtgärden genom att skicka en annan begäran till den URL: en.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Storage/operations/{operation-id}?monitor=true&api-version=2016-01-01
```

Om begäran fortfarande körs får du en status kod 202. Om begäran har slutförts får du en status kod 200 och bröd texten i svaret innehåller egenskaperna för det lagrings konto som har skapats.

## <a name="next-steps"></a>Nästa steg

* Dokumentation om varje REST-åtgärd finns i [REST API-dokumentationen](/rest/api/).
* Information om hur du distribuerar mallar via Resource Manager REST API finns i [distribuera resurser med Resource Manager-mallar och Resource manager REST API](../azure-resource-manager/templates/deploy-rest.md).