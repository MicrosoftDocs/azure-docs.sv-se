---
title: Återställa virtuella Azure-datorer med REST API
description: I den här artikeln lär du dig att hantera återställnings åtgärder för säkerhets kopiering av virtuella Azure-datorer med hjälp av REST API.
ms.topic: conceptual
ms.date: 09/12/2018
ms.assetid: b8487516-7ac5-4435-9680-674d9ecf5642
ms.openlocfilehash: da6b4cd6134f0cd1fd3d6e04e814bbf8aec9b07d
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2021
ms.locfileid: "102452160"
---
# <a name="restore-azure-virtual-machines-using-rest-api"></a>Återställa virtuella Azure-datorer med hjälp av REST API

När säkerhets kopieringen av en virtuell Azure-dator med Azure Backup har slutförts kan en återställa hela virtuella Azure-datorer eller diskar eller filer från samma säkerhets kopia. I den här artikeln beskrivs hur du återställer en virtuell Azure-dator eller diskar med REST API.

För alla återställnings åtgärder måste en av dem identifiera den relevanta återställnings punkten först.

## <a name="select-recovery-point"></a>Välj återställnings punkt

Tillgängliga återställnings punkter för ett säkerhets kopierings objekt kan listas med hjälp av [listan återställnings punkt REST API](/rest/api/backup/recoverypoints/list). Det är en enkel åtgärd för att *Hämta* alla relevanta värden.

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints?api-version=2019-05-13
```

`{containerName}`Och `{protectedItemName}` är som konstruerade [här](backup-azure-arm-userestapi-backupazurevms.md#example-responses-to-get-operation). `{fabricName}` är "Azure".

*Hämta* URI har alla nödvändiga parametrar. Det behövs ingen ytterligare brödtext.

### <a name="responses"></a>Svar

|Namn  |Typ  |Beskrivning  |
|---------|---------|---------|
|200 OK     |   [RecoveryPointResourceList](/rest/api/backup/recoverypoints/list#recoverypointresourcelist)      |       OK  |

#### <a name="example-response"></a>Exempelsvar

När *hämtnings* -URI: n har skickats returneras ett 200-svar (OK).

```http
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: 03453538-2f8d-46de-8374-143ccdf60f40
x-ms-client-request-id: c48f4436-ce3f-42da-b537-12710d4d1a24; c48f4436-ce3f-42da-b537-12710d4d1a24
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14998
x-ms-correlation-request-id: 03453538-2f8d-46de-8374-143ccdf60f40
x-ms-routing-request-id: SOUTHINDIA:20180604T071851Z:03453538-2f8d-46de-8374-143ccdf60f40
Cache-Control: no-cache
Date: Mon, 04 Jun 2018 07:18:51 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/VM;testRG;testVM/recoveryPoints/20982486783671",
      "name": "20982486783671",
      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints",
      "properties": {
        "objectType": "IaasVMRecoveryPoint",
        "recoveryPointType": "AppConsistent",
        "recoveryPointTime": "2018-06-04T06:06:00.5121087Z",
        "recoveryPointAdditionalInfo": "",
        "sourceVMStorageType": "NormalStorage",
        "isSourceVMEncrypted": false,
        "isInstantIlrSessionActive": false,
        "recoveryPointTierDetails": [
          {
            "type": 1,
            "status": 1
          },
          {
            "type": 2,
            "status": 1
          }
        ],
        "isManagedVirtualMachine": true,
        "virtualMachineSize": "Standard_A1_v2",
        "originalStorageAccountOption": false
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/VM;testRG;testVM/recoveryPoints/23358112038108",
      "name": "23358112038108",
      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints",
      "properties": {
        "objectType": "IaasVMRecoveryPoint",
        "recoveryPointType": "CrashConsistent",
        "recoveryPointTime": "2018-06-03T06:20:56.3043878Z",
        "recoveryPointAdditionalInfo": "",
        "sourceVMStorageType": "NormalStorage",
        "isSourceVMEncrypted": false,
        "isInstantIlrSessionActive": false,
        "recoveryPointTierDetails": [
          {
            "type": 1,
            "status": 1
          },
          {
            "type": 2,
            "status": 1
          }
        ],
        "isManagedVirtualMachine": true,
        "virtualMachineSize": "Standard_A1_v2",
        "originalStorageAccountOption": false
      }
    },
......
```

Återställnings punkten identifieras med `{name}` fältet i ovanstående svar.

## <a name="restore-operations"></a>Återställnings åtgärder

När du har valt [relevant återställnings punkt](#select-recovery-point)fortsätter du med att utlösa återställnings åtgärden.

***Alla återställnings åtgärder på säkerhets kopierings objekt utförs med samma *post* -API. Endast begär ande texten ändras med återställnings scenarier.***

> [!IMPORTANT]
> All information om olika återställnings alternativ och deras beroenden anges [här](./backup-azure-arm-restore-vms.md#restore-options). Granska innan du fortsätter att utlösa de här åtgärderna.

Att utlösa återställnings åtgärder är en *post* -begäran. Mer information om API: t finns i [REST API "trigger Restore"](/rest/api/backup/restores/trigger).

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints/{recoveryPointId}/restore?api-version=2019-05-13
```

`{containerName}`Och `{protectedItemName}` är som konstruerade [här](backup-azure-arm-userestapi-backupazurevms.md#example-responses-to-get-operation). `{fabricName}` är "Azure" och `{recoveryPointId}` är `{name}` fältet för den återställnings punkt som anges [ovan](#example-response).

När återställnings punkten har hämtats måste vi konstruera begär ande texten för det relevanta återställnings scenariot. I följande avsnitt beskrivs förfrågnings texten för varje scenario.

- [Återställa diskar](#restore-disks)
- [Ersätt diskar](#replace-disks-in-a-backed-up-virtual-machine)
- [Återställ som ny virtuell dator](#restore-as-another-virtual-machine)

### <a name="restore-response"></a>Återställ svar

Aktivering av eventuella återställnings åtgärder är en [asynkron åtgärd](../azure-resource-manager/management/async-operations.md). Det innebär att den här åtgärden skapar en annan åtgärd som måste spåras separat.

Den returnerar två svar: 202 (accepterad) när en annan åtgärd skapas och sedan 200 (OK) när åtgärden har slutförts.

|Namn  |Typ  |Beskrivning  |
|---------|---------|---------|
|202 accepterad     |         |     Har godkänts    |

#### <a name="example-responses"></a>Exempel svar

När du har skickat *post* -URI: n för att utlösa återställnings diskar är det första svaret 202 (accepteras) med ett plats huvud eller Azure-async-header.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/781a0f18-e250-4d73-b059-5e9ffed4069e?api-version=2019-05-13
X-Content-Type-Options: nosniff
x-ms-request-id: 893fe372-8d6c-4c56-b589-45a95eeef95f
x-ms-client-request-id: a15ce064-25bd-4ac6-87e5-e3bc6ec65c0b; a15ce064-25bd-4ac6-87e5-e3bc6ec65c0b
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1198
x-ms-correlation-request-id: 893fe372-8d6c-4c56-b589-45a95eeef95f
x-ms-routing-request-id: SOUTHINDIA:20180604T130003Z:893fe372-8d6c-4c56-b589-45a95eeef95f
Cache-Control: no-cache
Date: Mon, 04 Jun 2018 13:00:03 GMT
Location: https://management.azure.com/subscriptions//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationResults/781a0f18-e250-4d73-b059-5e9ffed4069e?api-version=2019-05-13
X-Powered-By: ASP.NET
```

Spåra sedan den resulterande åtgärden med hjälp av plats rubriken eller Azure-AsyncOperation huvud med ett enkelt *Get* -kommando.

```http
GET https://management.azure.com/subscriptions//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationResults/781a0f18-e250-4d73-b059-5e9ffed4069e?api-version=2019-05-13
```

När åtgärden har slutförts returneras 200 (OK) med ID: t för det resulterande återställnings jobbet i svars texten.

```http
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: ea2a8011-eb83-4a4b-9ed2-e694070a966a
x-ms-client-request-id: a7f3a144-ed80-41ee-bffe-ae6a90c35a2f; a7f3a144-ed80-41ee-bffe-ae6a90c35a2f
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14973
x-ms-correlation-request-id: ea2a8011-eb83-4a4b-9ed2-e694070a966a
x-ms-routing-request-id: SOUTHINDIA:20180604T130917Z:ea2a8011-eb83-4a4b-9ed2-e694070a966a
Cache-Control: no-cache
Date: Mon, 04 Jun 2018 13:09:17 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "id": "781a0f18-e250-4d73-b059-5e9ffed4069e",
  "name": "781a0f18-e250-4d73-b059-5e9ffed4069e",
  "status": "Succeeded",
  "startTime": "2018-06-04T13:00:03.8068176Z",
  "endTime": "2018-06-04T13:00:03.8068176Z",
  "properties": {
    "objectType": "OperationStatusJobExtendedInfo",
    "jobId": "3021262a-fb3a-4538-9b37-e3e97a386093"
  }
}
```

Eftersom återställnings jobbet är en tids krävande åtgärd bör det spåras som förklaras i [övervaknings jobben med REST API-dokument](backup-azure-arm-userestapi-managejobs.md#tracking-the-job).

### <a name="restore-disks"></a>Återställa diskar

Om du behöver anpassa skapandet av en virtuell dator från säkerhets kopierings data kan du bara återställa diskarna till ett valt lagrings konto och skapa en virtuell dator från dessa diskar enligt deras krav. Lagrings kontot ska finnas i samma region som Recovery Services valvet och bör inte vara zoner-redundant. Både diskarna och konfigurationen av den säkerhetskopierade virtuella datorn ("vmconfig.jspå") lagras i det aktuella lagrings kontot. Som förklaras [ovan](#restore-operations)anges relevant begär ande text för återställnings diskar nedan.

#### <a name="create-request-body"></a>Skapa brödtext för begäran

Om du vill utlösa en disk återställning från en säkerhets kopia av en virtuell Azure-dator, följer du komponenterna i begär ande texten.

|Namn  |Typ  |Beskrivning  |
|---------|---------|---------|
|properties     | [IaaSVMRestoreRequest](/rest/api/backup/restores/trigger#iaasvmrestorerequest)        |    RestoreRequestResourceProperties     |

En fullständig lista över definitioner av begär ande texten och annan information finns i [utlös REST API-dokument](/rest/api/backup/restores/trigger#request-body).

##### <a name="example-request"></a>Exempelbegäran

Följande begär ande text definierar egenskaper som krävs för att utlösa en disk återställning.

```json
{
  "properties": {
    "objectType": "IaasVMRestoreRequest",
    "recoveryPointId": "20982486783671",
    "recoveryType": "RestoreDisks",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "storageAccountId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Storage/storageAccounts/testAccount",
    "region": "westus",
    "createNewCloudService": false,
    "originalStorageAccountOption": false,
    "encryptionDetails": {
      "encryptionEnabled": false
    }
  }
}
```

### <a name="restore-disks-selectively"></a>Återställ diskar selektivt

Om du [selektivt säkerhetskopierar diskar](backup-azure-arm-userestapi-backupazurevms.md#excluding-disks-in-azure-vm-backup)finns listan över aktuella säkerhets kopior i [återställnings punkt sammanfattning](#select-recovery-point) och ett [detaljerat svar](/rest/api/backup/recoverypoints/get). Du kan också selektivt återställa diskar och mer information finns [här](selective-disk-backup-restore.md#selective-disk-restore). Om du vill återställa en disk i listan över säkerhetskopierade diskar selektivt, letar du reda på LUN-enheten från återställnings punktens svar och lägger till egenskapen **restoreDiskLunList** i [begär ande texten ovan](#example-request) som visas nedan.

```json
{
    "properties": {
        "objectType": "IaasVMRestoreRequest",
        "recoveryPointId": "20982486783671",
        "recoveryType": "RestoreDisks",
        "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
        "storageAccountId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Storage/storageAccounts/testAccount",
        "region": "westus",
        "createNewCloudService": false,
        "originalStorageAccountOption": false,
        "encryptionDetails": {
          "encryptionEnabled": false
        },
        "restoreDiskLunList" : [0]
    }
}

```

När du har spårat svaret enligt beskrivningen [ovan](#responses)och det långvariga jobbet är slutfört, finns diskarna och konfigurationen för den säkerhetskopierade virtuella datorn ("VMConfig.jspå") på det angivna lagrings kontot.

### <a name="replace-disks-in-a-backed-up-virtual-machine"></a>Ersätt diskar i en säkerhets kopie rad virtuell dator

När disken återställs skapas diskar från återställnings punkten ersätter diskarna de aktuella diskarna i den säkerhetskopierade virtuella datorn med diskarna från återställnings punkten. Som det beskrivs [ovan](#restore-operations)anges den relevanta begär ande texten för att ersätta diskar nedan.

#### <a name="create-request-body"></a>Skapa brödtext för begäran

Om du vill utlösa en disk ersättning från en virtuell Azure-säkerhetskopiering, följer du komponenterna i begär ande texten.

|Namn  |Typ  |Beskrivning  |
|---------|---------|---------|
|properties     | [IaaSVMRestoreRequest](/rest/api/backup/restores/trigger#iaasvmrestorerequest)        |    RestoreRequestResourceProperties     |

En fullständig lista över definitioner av begär ande texten och annan information finns i [utlös REST API-dokument](/rest/api/backup/restores/trigger#request-body).

#### <a name="example-request"></a>Exempelbegäran

Följande begär ande text definierar egenskaper som krävs för att utlösa en disk återställning.

```json
{
    "properties": {
        "objectType": "IaasVMRestoreRequest",
        "recoveryPointId": "20982486783671",
        "recoveryType": "OriginalLocation",
        "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
        "storageAccountId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Storage/storageAccounts/testAccount",  
        "region": "westus",
        "createNewCloudService": false,
        "originalStorageAccountOption": false,
        "affinityGroup": "",
        "diskEncryptionSetId": null,
        "subnetId": null,
        "targetDomainNameId": null,
        "targetResourceGroupId": null,
        "targetVirtualMachineId": null,
        "virtualNetworkId": null
     }
}

```

### <a name="restore-as-another-virtual-machine"></a>Återställ som en annan virtuell dator

Enligt beskrivningen [ovan](#restore-operations)definierar följande begär ande text egenskaper som krävs för att utlösa en återställning av en virtuell dator.

```json
{
  "properties": {
          "objectType":  "IaasVMRestoreRequest",
          "recoveryPointId": "348916168024334",
          "recoveryType": "AlternateLocation",
          "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
          "targetVirtualMachineId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG/providers/Microsoft.Compute/virtualmachines/targetVM",
          "targetResourceGroupId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG",
          "storageAccountId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Storage/storageAccounts/testingAccount",
          "virtualNetworkId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG/providers/Microsoft.Network/virtualNetworks/testNet",
          "subnetId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG/providers/Microsoft.Network/virtualNetworks/testNet/subnets/default",
          "region": "westus",
          "createNewCloudService": false,
          "originalStorageAccountOption": false,
          "encryptionDetails": {
            "encryptionEnabled": false
     }
 }
```

Svaret ska hanteras på samma sätt som [förklaras ovan för att återställa diskar](#responses).

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Backup REST-API: er finns i följande dokument:

- [Azure Recovery Services-Provider REST API](/rest/api/recoveryservices/)
- [Komma igång med Azure REST API](/rest/api/azure/)
