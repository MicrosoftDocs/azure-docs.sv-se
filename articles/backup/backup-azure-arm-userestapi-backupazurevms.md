---
title: Säkerhetskopiera virtuella Azure-datorer med REST API
description: I den här artikeln får du lära dig hur du konfigurerar, initierar och hanterar säkerhets kopierings åtgärder för virtuella Azure-datorer med hjälp av REST API.
ms.topic: conceptual
ms.date: 08/03/2018
ms.assetid: b80b3a41-87bf-49ca-8ef2-68e43c04c1a3
ms.openlocfilehash: 9ba22c51c7a6c26a232ed20aec21fc83d2c54b37
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92171464"
---
# <a name="back-up-an-azure-vm-using-azure-backup-via-rest-api"></a>Säkerhetskopiera en virtuell Azure-dator med hjälp av Azure Backup via REST API

Den här artikeln beskriver hur du hanterar säkerhets kopior för en virtuell Azure-dator med hjälp av Azure Backup via REST API. Konfigurera skydd för första gången för en tidigare oskyddad virtuell Azure-dator, Utlös en säkerhets kopiering på begäran för en skyddad virtuell Azure-dator och ändra säkerhets kopierings egenskaperna för en säkerhets kopie rad virtuell dator via REST API enligt beskrivningen här.

Se [skapa valv](backup-azure-arm-userestapi-createorupdatevault.md) och [skapa principer](backup-azure-arm-userestapi-createorupdatepolicy.md) REST API själv studie kurser för att skapa nya valv och principer.

Anta att du vill skydda en virtuell dator "testVM" under en resurs grupp "testRG" till ett Recovery Services valv "testVault", som finns i resurs gruppen "testVaultRG", med standard principen (med namnet "DefaultPolicy").

## <a name="configure-backup-for-an-unprotected-azure-vm-using-rest-api"></a>Konfigurera säkerhets kopiering för en oskyddad virtuell Azure-dator med hjälp av REST API

### <a name="discover-unprotected-azure-vms"></a>Identifiera oskyddade virtuella Azure-datorer

Först ska valvet kunna identifiera den virtuella Azure-datorn. Detta utlöses med hjälp av [uppdaterings åtgärden](/rest/api/backup/protectioncontainers/refresh). Det är en asynkron *post*  -åtgärd som ser till att valvet får den senaste listan över alla oskyddade virtuella datorer i den aktuella prenumerationen och cachelagrar dem. När den virtuella datorn är "cachelagrad" kommer återställnings tjänster att kunna komma åt den virtuella datorn och skydda den.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupname}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/refreshContainers?api-version=2016-12-01
```

POST-URI: n har `{subscriptionId}` , `{vaultName}` , `{vaultresourceGroupName}` , `{fabricName}` parametrar. `{fabricName}`Är "Azure". Enligt vårt exempel `{vaultName}` är "testVault" och `{vaultresourceGroupName}` är "testVaultRG". Eftersom alla obligatoriska parametrar anges i URI: n behöver du inte ha någon separat brödtext för begäran.

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/refreshContainers?api-version=2016-12-01
```

#### <a name="responses-to-refresh-operation"></a>Svar på uppdaterings åtgärden

Åtgärden Uppdatera är en [asynkron åtgärd](../azure-resource-manager/management/async-operations.md). Det innebär att den här åtgärden skapar en annan åtgärd som måste spåras separat.

Den returnerar två svar: 202 (accepterad) när en annan åtgärd skapas och sedan 200 (OK) när åtgärden har slutförts.

|Namn  |Typ  |Beskrivning  |
|---------|---------|---------|
|204 inget innehåll     |         |  OK utan innehåll som returneras      |
|202 accepterad     |         |     Har godkänts    |

##### <a name="example-responses-to-refresh-operation"></a>Exempel svar för uppdaterings åtgärd

När *post* -begäran har skickats returneras ett 202-svar (accepterat).

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
X-Content-Type-Options: nosniff
x-ms-request-id: 43cf550d-e463-421c-8922-37e4766db27d
x-ms-client-request-id: 4910609f-bb9b-4c23-8527-eb6fa2d3253f; 4910609f-bb9b-4c23-8527-eb6fa2d3253f
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 43cf550d-e463-421c-8922-37e4766db27d
x-ms-routing-request-id: SOUTHINDIA:20180521T105701Z:43cf550d-e463-421c-8922-37e4766db27d
Cache-Control: no-cache
Date: Mon, 21 May 2018 10:57:00 GMT
Location: https://management.azure.com/subscriptions//00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/operationResults/aad204aa-a5cf-4be2-a7db-a224819e5890?api-version=2019-05-13
X-Powered-By: ASP.NET
```

Spåra den resulterande åtgärden med hjälp av rubriken "location" med ett enkelt *Get* -kommando

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/operationResults/aad204aa-a5cf-4be2-a7db-a224819e5890?api-version=2019-05-13
```

När alla virtuella Azure-datorer har identifierats returnerar GET-kommandot ett 204-svar (inget innehåll). Valvet kan nu identifiera vilken virtuell dator som helst i prenumerationen.

```http
HTTP/1.1 204 NoContent
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: cf6cd73b-9189-4942-a61d-878fcf76b1c1
x-ms-client-request-id: 25bb6345-f9fc-4406-be1a-dc6db0eefafe; 25bb6345-f9fc-4406-be1a-dc6db0eefafe
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14997
x-ms-correlation-request-id: cf6cd73b-9189-4942-a61d-878fcf76b1c1
x-ms-routing-request-id: SOUTHINDIA:20180521T105825Z:cf6cd73b-9189-4942-a61d-878fcf76b1c1
Cache-Control: no-cache
Date: Mon, 21 May 2018 10:58:25 GMT
X-Powered-By: ASP.NET
```

### <a name="selecting-the-relevant-azure-vm"></a>Välja relevant virtuell Azure-dator

 Du kan bekräfta att "cachelagring" görs genom att [lista alla objekt](/rest/api/backup/backupprotectableitems/list) som kan skyddas under prenumerationen och hitta önskad virtuell dator i svaret. [Svaret på den här åtgärden](#example-responses-to-get-operation) ger också information om hur Recovery Services identifierar en virtuell dator.  När du känner till mönstret kan du hoppa över det här steget och fortsätta att [aktivera skyddet](#enabling-protection-for-the-azure-vm)direkt.

Den här åtgärden är en *Get* -åtgärd.

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupProtectableItems?api-version=2016-12-01&$filter=backupManagementType eq 'AzureIaasVM'
```

*Hämta* URI har alla nödvändiga parametrar. Ingen ytterligare begär ande text krävs.

#### <a name="responses-to-get-operation"></a>Svar för att hämta åtgärd

|Namn  |Typ  |Beskrivning  |
|---------|---------|---------|
|200 OK     | [WorkloadProtectableItemResourceList](/rest/api/backup/backupprotectableitems/list#workloadprotectableitemresourcelist)        |       OK |

#### <a name="example-responses-to-get-operation"></a>Exempel svar för att hämta åtgärd

När *Get* -begäran har skickats returneras ett 200-svar (OK).

```http
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: 7c2cf56a-e6be-4345-96df-c27ed849fe36
x-ms-client-request-id: 40c8601a-c217-4c68-87da-01db8dac93dd; 40c8601a-c217-4c68-87da-01db8dac93dd
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14979
x-ms-correlation-request-id: 7c2cf56a-e6be-4345-96df-c27ed849fe36
x-ms-routing-request-id: SOUTHINDIA:20180521T071408Z:7c2cf56a-e6be-4345-96df-c27ed849fe36
Cache-Control: no-cache
Date: Mon, 21 May 2018 07:14:08 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainerv2;testRG;testVM/protectableItems/vm;iaasvmcontainerv2;testRG;testVM",
      "name": "iaasvmcontainerv2;testRG;testVM",
      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectableItems",
      "properties": {
        "virtualMachineId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
        "virtualMachineVersion": "Compute",
        "resourceGroup": "testRG",
        "backupManagementType": "AzureIaasVM",
        "protectableItemType": "Microsoft.Compute/virtualMachines",
        "friendlyName": "testVM",
        "protectionState": "NotProtected"
      }
    },……………..

```

> [!TIP]
> Antalet värden i *Get* -svaret är begränsat till 200 för en sida. Använd fältet ' nextLink ' för att hämta URL: en för nästa uppsättning svar.

Svaret innehåller en lista över alla oskyddade virtuella Azure-datorer och varje `{value}` innehåller all information som krävs av Azure Recovery Service för att konfigurera säkerhets kopiering. Om du vill konfigurera säkerhets kopiering noterar du `{name}` fältet och `{virtualMachineId}` fältet i `{properties}` avsnittet. Konstruera två variabler från dessa fält värden enligt vad som anges nedan.

- containerName = "iaasvmcontainer;" +`{name}`
- protectedItemName = "VM;" + `{name}`
- `{virtualMachineId}` används senare i [begär ande texten](#example-request-body)

I exemplet översätts ovanstående värden till:

- containerName = "iaasvmcontainer; iaasvmcontainerv2; testRG; testVM"
- protectedItemName = "VM; iaasvmcontainerv2; testRG; testVM"

### <a name="enabling-protection-for-the-azure-vm"></a>Aktiverar skydd för den virtuella Azure-datorn

När den aktuella virtuella datorn är "cachelagrad" och "identifierad" väljer du den princip som du vill skydda. Om du vill veta mer om befintliga principer i valvet, se [lista över princip-API](/rest/api/backup/backuppolicies/list). Välj sedan den [aktuella principen](/rest/api/backup/protectionpolicies/get) genom att referera till princip namnet. Information om hur du skapar principer finns i [själv studie kursen skapa princip](backup-azure-arm-userestapi-createorupdatepolicy.md). "DefaultPolicy" är markerat i exemplet nedan.

Aktivering av skydd är en asynkron *placerings* åtgärd som skapar ett skyddat objekt.

```http
https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2019-05-13
```

`{containerName}`Och `{protectedItemName}` är som konstruerade ovan. `{fabricName}`Är "Azure". I vårt exempel översätts detta till:

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM?api-version=2019-05-13
```

#### <a name="create-the-request-body"></a>Skapa begär ande texten

Om du vill skapa ett skyddat objekt följer du komponenterna i begär ande texten.

|Namn  |Typ  |Beskrivning  |
|---------|---------|---------|
|properties     | AzureIaaSVMProtectedItem        |Egenskaper för ProtectedItem-resurs         |

En fullständig lista över definitioner av begär ande texten och annan information finns i [skapa skyddat objekt REST API dokument](/rest/api/backup/protecteditems/createorupdate#request-body).

##### <a name="example-request-body"></a>Exempel på begär ande text

Följande begär ande text definierar egenskaper som krävs för att skapa ett skyddat objekt.

```json
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/DefaultPolicy"
  }
}
```

`{sourceResourceId}`Är det `{virtualMachineId}` som nämns ovan från [svar på listan över skrivskyddade objekt](#example-responses-to-get-operation).

#### <a name="responses-to-create-protected-item-operation"></a>Svar för att skapa skyddad objekt åtgärd

Att skapa ett skyddat objekt är en [asynkron åtgärd](../azure-resource-manager/management/async-operations.md). Det innebär att den här åtgärden skapar en annan åtgärd som måste spåras separat.

Den returnerar två svar: 202 (accepterad) när en annan åtgärd skapas och sedan 200 (OK) när åtgärden har slutförts.

|Namn  |Typ  |Beskrivning  |
|---------|---------|---------|
|200 OK     |    [ProtectedItemResource](/rest/api/backup/protecteditemoperationresults/get#protecteditemresource)     |  OK       |
|202 accepterad     |         |     Har godkänts    |

##### <a name="example-responses-to-create-protected-item-operation"></a>Exempel svar för att skapa skyddad objekt åtgärd

När du skickar in *begäran om att skapa* eller uppdatera skyddade objekt är det första svaret 202 (accepteras) med ett plats huvud eller Azure-async-header.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2019-05-13
X-Content-Type-Options: nosniff
x-ms-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-client-request-id: e1f94eef-9b2d-45c4-85b8-151e12b07d03; e1f94eef-9b2d-45c4-85b8-151e12b07d03
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-routing-request-id: SOUTHINDIA:20180521T073907Z:db785be0-bb20-4598-bc9f-70c9428b170b
Cache-Control: no-cache
Date: Mon, 21 May 2018 07:39:06 GMT
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationResults/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2019-05-13
X-Powered-By: ASP.NET
```

Spåra sedan den resulterande åtgärden med hjälp av plats rubriken eller Azure-AsyncOperation huvud med ett enkelt *Get* -kommando.

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2019-05-13
```

När åtgärden har slutförts returneras 200 (OK) med det skyddade objekt innehållet i svars texten.

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM",
  "name": "VM;testRG;testVM",
  "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems",
  "properties": {
    "friendlyName": "testVM",
    "virtualMachineId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "protectionStatus": "Healthy",
    "protectionState": "IRPending",
    "healthStatus": "Passed",
    "lastBackupStatus": "",
    "lastBackupTime": "2001-01-01T00:00:00Z",
    "protectedItemDataId": "17592691116891",
    "extendedInfo": {
      "recoveryPointCount": 0,
      "policyInconsistent": false
    },
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "backupManagementType": "AzureIaasVM",
    "workloadType": "VM",
    "containerName": "iaasvmcontainerv2;testRG;testVM",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/DefaultPolicy",
    "policyName": "DefaultPolicy"
  }
}
```

Detta bekräftar att skyddet har Aktiver ATS för den virtuella datorn och att den första säkerhets kopieringen kommer att utlösas enligt princip schemat.

### <a name="excluding-disks-in-azure-vm-backup"></a>Exkludera diskar i virtuell Azure-säkerhetskopiering

Azure Backup är också ett sätt att selektivt säkerhetskopiera en delmängd av diskar i virtuella Azure-datorer. Mer information finns [här](selective-disk-backup-restore.md). Om du vill selektivt säkerhetskopiera några diskar när du aktiverar skydd bör följande kodfragment vara [begär ande texten när skyddet aktive ras](#example-request-body).

```json
{
"properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/DefaultPolicy",
    "extendedProperties":  {
      "diskExclusionProperties":{
          "diskLunList":[0,1],
          "isInclusionList":true
        }
    }
}
}
```

I begär ande texten ovan finns listan över diskar som ska säkerhets kopie ras i avsnittet utökade egenskaper.

|Egenskap  |Värde  |
|---------|---------|
|diskLunList     | Listan disk-LUN är en lista över *LUN för data diskar*. **OS-disken säkerhets kopie ras alltid och behöver inte anges**.        |
|IsInclusionList     | Bör vara **Sant** för de LUN som ska tas med under säkerhets kopieringen. Om det är **falskt** kommer de ovan nämnda LUN att undantas.         |

Så om kravet bara är att säkerhetskopiera OS-disken ska _alla_ data diskar undantas. Ett enklare sätt är att säga att inga data diskar ska tas med. Diskens LUN-lista är tom och **IsInclusionList** är **sann**. På samma sätt kan du tänka på vad som är det enklaste sättet att välja en delmängd: ett fåtal diskar bör alltid undantas eller så bör ett fåtal diskar alltid inkluderas. Välj LUN-listan och värdet för den booleska variabeln.

## <a name="trigger-an-on-demand-backup-for-a-protected-azure-vm"></a>Utlös en säkerhets kopiering på begäran för en skyddad virtuell Azure-dator

När en virtuell Azure-dator har kon figurer ATS för säkerhets kopiering sker säkerhets kopieringar enligt princip schemat. Du kan vänta på den första schemalagda säkerhets kopieringen eller utlösa en säkerhets kopiering på begäran när som helst. Kvarhållning för säkerhets kopiering på begäran är separat från säkerhets kopierings principens kvarhållning och kan anges till en viss datum tid. Om inget anges antas det vara 30 dagar från dagen för utlösaren av säkerhets kopiering på begäran.

Att utlösa en säkerhets kopiering på begäran är en *post* -åtgärd.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/backup?api-version=2016-12-01
```

`{containerName}`Och `{protectedItemName}` är som konstruerade [ovan](#responses-to-get-operation). `{fabricName}`Är "Azure". I vårt exempel översätts detta till:

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM/backup?api-version=2016-12-01
```

### <a name="create-the-request-body-for-on-demand-backup"></a>Skapa begär ande texten för säkerhets kopiering på begäran

Om du vill utlösa en säkerhets kopiering på begäran, följer du komponenterna i begär ande texten.

|Namn  |Typ  |Beskrivning  |
|---------|---------|---------|
|properties     | [IaaSVMBackupRequest](/rest/api/backup/backups/trigger#iaasvmbackuprequest)        |Egenskaper för BackupRequestResource         |

En fullständig lista över definitioner av begär ande texten och annan information finns i [Utlös säkerhets kopiering för skyddade objekt REST API dokument](/rest/api/backup/backups/trigger#request-body).

#### <a name="example-request-body-for-on-demand-backup"></a>Exempel på begäran om säkerhets kopiering på begäran

Följande begär ande text definierar egenskaper som krävs för att utlösa en säkerhets kopia för ett skyddat objekt. Om kvarhållning inte anges kommer den att behållas i 30 dagar från det att säkerhets kopierings jobbet utlöses.

```json
{
   "properties": {
    "objectType": "IaasVMBackupRequest",
    "recoveryPointExpiryTimeInUTC": "2018-12-01T02:16:20.3156909Z"
  }
}
```

### <a name="responses-for-on-demand-backup"></a>Svar för säkerhets kopiering på begäran

Att utlösa en säkerhets kopiering på begäran är en [asynkron åtgärd](../azure-resource-manager/management/async-operations.md). Det innebär att den här åtgärden skapar en annan åtgärd som måste spåras separat.

Den returnerar två svar: 202 (accepterad) när en annan åtgärd skapas och sedan 200 (OK) när åtgärden har slutförts.

|Namn  |Typ  |Beskrivning  |
|---------|---------|---------|
|202 accepterad     |         |     Har godkänts    |

#### <a name="example-responses-for-on-demand-backup"></a>Exempel svar för säkerhets kopiering på begäran

När du har skickat in *post* -begäran för en säkerhets kopiering på begäran är det första svaret 202 (accepteras) med ett plats huvud eller Azure-async-header.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testVaultRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/b8daecaa-f8f5-44ed-9f18-491a9e9ba01f?api-version=2019-05-13
X-Content-Type-Options: nosniff
x-ms-request-id: 7885ca75-c7c6-43fb-a38c-c0cc437d8810
x-ms-client-request-id: 7df8e874-1d66-4f81-8e91-da2fe054811d; 7df8e874-1d66-4f81-8e91-da2fe054811d
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 7885ca75-c7c6-43fb-a38c-c0cc437d8810
x-ms-routing-request-id: SOUTHINDIA:20180521T083541Z:7885ca75-c7c6-43fb-a38c-c0cc437d8810
Cache-Control: no-cache
Date: Mon, 21 May 2018 08:35:41 GMT
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testVaultRG;testVM/protectedItems/vm;testRG;testVM/operationResults/b8daecaa-f8f5-44ed-9f18-491a9e9ba01f?api-version=2019-05-13
X-Powered-By: ASP.NET
```

Spåra sedan den resulterande åtgärden med hjälp av plats rubriken eller Azure-AsyncOperation huvud med ett enkelt *Get* -kommando.

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2019-05-13
```

När åtgärden har slutförts returneras 200 (OK) med ID: t för det resulterande säkerhets kopierings jobbet i svars texten.

```json
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: a8b13524-2c95-445f-b107-920806f385c1
x-ms-client-request-id: 5a63209d-3708-4e69-a75f-9499f4c8977c; 5a63209d-3708-4e69-a75f-9499f4c8977c
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14995
x-ms-correlation-request-id: a8b13524-2c95-445f-b107-920806f385c1
x-ms-routing-request-id: SOUTHINDIA:20180521T083723Z:a8b13524-2c95-445f-b107-920806f385c1
Cache-Control: no-cache
Date: Mon, 21 May 2018 08:37:22 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "00000000-0000-0000-0000-000000000000",
  "status": "Succeeded",
  "startTime": "2018-05-21T08:35:40.9488967Z",
  "endTime": "2018-05-21T08:35:40.9488967Z",
  "properties": {
    "objectType": "OperationStatusJobExtendedInfo",
    "jobId": "7ddead57-bcb9-4269-ac31-6a1b57588700"
  }
}
```

Eftersom säkerhets kopierings jobbet är en tids krävande åtgärd måste det spåras enligt beskrivningen i [övervaknings jobben med REST API-dokument](backup-azure-arm-userestapi-managejobs.md#tracking-the-job).

## <a name="modify-the-backup-configuration-for-a-protected-azure-vm"></a>Ändra säkerhets kopierings konfigurationen för en skyddad virtuell Azure-dator

### <a name="changing-the-policy-of-protection"></a>Ändra principen för skydd

Om du vill ändra principen som den virtuella datorn är skyddad med kan du använda samma format som att [Aktivera skydd](#enabling-protection-for-the-azure-vm). Ange bara det nya princip-ID: t i [begär ande texten](#example-request-body) och skicka begäran. Exempel: om du vill ändra principen för testVM från "DefaultPolicy" till "ProdPolicy" anger du "ProdPolicy"-ID: t i begär ande texten.

```json
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/ProdPolicy"
  }
}
```

Svaret följer samma format som det som nämnts [för att aktivera skydd](#responses-to-create-protected-item-operation)

#### <a name="excluding-disks-during-azure-vm-protection"></a>Exkludera diskar under Azure VM-skydd

Om den virtuella Azure-datorn redan har säkerhetskopierats kan du ange en lista över diskar som ska säkerhets kopie ras eller undantas genom att ändra skydds principen. Du behöver bara förbereda begäran i samma format som när du [aktiverar skydd](#excluding-disks-in-azure-vm-backup)

> [!IMPORTANT]
> Begär ande texten ovan är alltid den sista kopian av data diskar som ska undantas eller tas med. Detta *läggs* inte till i den tidigare konfigurationen. Exempel: om du först uppdaterar skyddet som "exkludera data disk 1" och sedan upprepar med "exkludera data disk 2", *undantas endast data disk 2* i efterföljande säkerhets kopieringar och data disk 1 tas med. Detta är alltid den slutgiltiga listan som kommer att inkluderas/undantas i efterföljande säkerhets kopieringar.

Hämta den information om det skyddade objektet som anges [här](/rest/api/backup/protecteditems/get)om du vill hämta den aktuella listan över diskar som undantas eller tas med. Svaret innehåller en lista över LUN för datadisk och indikerar om de tas med eller undantas.

### <a name="stop-protection-but-retain-existing-data"></a>Stoppa skyddet men behåll befintliga data

Ta bort skyddet för en skyddad virtuell dator men Behåll de data som redan har säkerhetskopierats genom att ta bort principen i begär ande texten och skicka begäran. När associationen med principen tas bort utlöses inte säkerhets kopieringarna och inga nya återställnings punkter skapas.

```http
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": ""
  }
}
```

Svaret följer samma format som det som nämnts [för att utlösa en säkerhets kopiering på begäran](#example-responses-for-on-demand-backup). Det resulterande jobbet bör spåras enligt beskrivningen i [övervaknings jobben med REST API-dokument](backup-azure-arm-userestapi-managejobs.md#tracking-the-job).

### <a name="stop-protection-and-delete-data"></a>Stoppa skyddet och ta bort data

Ta bort skyddet på en skyddad virtuell dator och ta bort säkerhetskopierade data också genom att utföra en borttagnings åtgärd som beskrivs [här](/rest/api/backup/protecteditems/delete).

Att stoppa skyddet och ta bort data är en *borttagnings* åtgärd.

```http
DELETE https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2019-05-13
```

`{containerName}`Och `{protectedItemName}` är som konstruerade [ovan](#responses-to-get-operation). `{fabricName}` är "Azure". I vårt exempel översätts detta till:

```http
DELETE https://management.azure.com//Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM?api-version=2019-05-13
```

#### <a name="responses-for-delete-protection"></a>Svar för borttagnings skydd

*Borttagning* av skydd är en [asynkron åtgärd](../azure-resource-manager/management/async-operations.md). Det innebär att den här åtgärden skapar en annan åtgärd som måste spåras separat.

Den returnerar två svar: 202 (accepterad) när en annan åtgärd skapas och sedan 204 (nocontent) när åtgärden har slutförts.

|Namn  |Typ  |Beskrivning  |
|---------|---------|---------|
|204 noåll     |         |  Inget innehåll       |
|202 accepterad     |         |     Har godkänts    |

> [!IMPORTANT]
> För att skydda mot oavsiktliga borttagnings scenarier finns det en [funktion för mjuk borttagning som är tillgänglig](use-restapi-update-vault-properties.md#soft-delete-state) för Recovery Services valv. Om det mjuka borttagnings läget för valvet är inställt på aktive rad tas inte data bort direkt i borttagnings åtgärden. Den sparas i 14 dagar och rensas sedan permanent. Du debiteras inte för lagring under den här perioden på 14 dagar. Om du vill ångra borttagnings åtgärden läser du [avsnittet ångra-ta bort](#undo-the-deletion).

### <a name="undo-the-deletion"></a>Ångra borttagningen

Att ångra borttagningen av misstag liknar att skapa säkerhets kopierings objekt. När borttagningen har ångrats behålls objektet men inga framtida säkerhets kopieringar utlöses.

Ångra borttagning *är en åtgärd* som liknar att [ändra principen](#changing-the-policy-of-protection) och/eller [aktivera skyddet](#enabling-protection-for-the-azure-vm). Ange bara avsikten att ångra borttagningen med variabeln *isRehydrate*  i [begär ande texten](#example-request-body) och skicka begäran. Exempel: om du vill ångra borttagningen av testVM, ska följande begär ande text användas.

```http
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "protectionState": "ProtectionStopped",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "isRehydrate": true
  }
}
```

Svaret följer samma format som det som nämnts [för att utlösa en säkerhets kopiering på begäran](#example-responses-for-on-demand-backup). Det resulterande jobbet bör spåras enligt beskrivningen i [övervaknings jobben med REST API-dokument](backup-azure-arm-userestapi-managejobs.md#tracking-the-job).

## <a name="next-steps"></a>Nästa steg

[Återställa data från en säkerhets kopia av en virtuell Azure-dator](backup-azure-arm-userestapi-restoreazurevms.md).

Mer information om Azure Backup REST-API: er finns i följande dokument:

- [Azure Recovery Services-Provider REST API](/rest/api/recoveryservices/)
- [Komma igång med Azure REST API](/rest/api/azure/)