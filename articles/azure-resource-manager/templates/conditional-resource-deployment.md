---
title: Villkorlig distribution med mallar
description: Beskriver hur du villkorligt distribuerar en resurs i en Azure Resource Manager-mall (ARM-mall).
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 409d258d7dfe3ed186e5cf97cc0dbe6dc149b849
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101741182"
---
# <a name="conditional-deployment-in-arm-templates"></a>Villkorsstyrd distribution i ARM-mallar

Ibland behöver du eventuellt distribuera en resurs i en Azure Resource Manager-mall (ARM-mall) eller bicep-fil. För JSON-mallar använder du `condition` elementet för att ange om resursen har distribuerats. För bicep använder du `if` nyckelordet för att ange om resursen har distribuerats. Värdet för villkoret löses till true eller false. När värdet är true skapas resursen. När värdet är false skapas inte resursen. Värdet kan bara användas för hela resursen.

> [!NOTE]
> Villkorlig distribution överlappar inte [underordnade resurser](child-resource-name-type.md). Om du vill villkorligt distribuera en resurs och dess underordnade resurser måste du tillämpa samma villkor för varje resurs typ.

## <a name="deploy-condition"></a>Distribuera villkor

Du kan skicka ett parameter värde som anger om en resurs har distribuerats. I följande exempel används villkorligt för att distribuera en DNS-zon.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "deployZone": {
      "type": "bool"
    }
  },
  "functions": [],
  "resources": [
    {
      "condition": "[parameters('deployZone')]",
      "type": "Microsoft.Network/dnsZones",
      "apiVersion": "2018-05-01",
      "name": "myZone",
      "location": "global"
    }
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param deployZone bool

resource dnsZone 'Microsoft.Network/dnszones@2018-05-01' = if (deployZone) {
  name: 'myZone'
  location: 'global'
}
```

---

Ett mer avancerat exempel finns i [logisk Azure SQL-Server](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server).

## <a name="new-or-existing-resource"></a>Ny eller befintlig resurs

Du kan använda villkorlig distribution för att skapa en ny resurs eller använda en befintlig. I följande exempel visas hur du antingen distribuerar ett nytt lagrings konto eller använder ett befintligt lagrings konto.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountName": {
      "type": "string"
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    },
    "newOrExisting": {
      "type": "string",
      "defaultValue": "new",
      "allowedValues": [
        "new",
        "existing"
      ]
    }
  },
  "functions": [],
  "resources": [
    {
      "condition": "[equals(parameters('newOrExisting'), 'new')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "name": "[parameters('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
      },
      "kind": "StorageV2",
      "properties": {
        "accessTier": "Hot"
      }
    }
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param storageAccountName string
param location string = resourceGroup().location

@allowed([
  'new'
  'existing'
])
param newOrExisting string = 'new'

resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = if (newOrExisting == 'new') {
  name: storageAccountName
  location: location
  sku: {
    name: 'Standard_LRS'
    tier: 'Standard'
  }
  kind: 'StorageV2'
  properties: {
    accessTier: 'Hot'
  }
}
```

---

När parametern `newOrExisting` är inställd på **ny**, utvärderas villkoret som sant. Lagrings kontot har distribuerats. Men om `newOrExisting` är inställt på **befintlig**, utvärderas villkoret till falskt och lagrings kontot distribueras inte.

En fullständig exempel mall som använder `condition` -elementet finns i [VM med en ny eller befintlig Virtual Network, lagring och offentlig IP-adress](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-new-or-existing-conditions).

## <a name="runtime-functions"></a>Körnings funktioner

Om du använder en [referens](template-functions-resource.md#reference) -eller [list](template-functions-resource.md#list) funktion med en resurs som är villkorligt distribuerad utvärderas funktionen även om resursen inte har distribuerats. Du får ett fel meddelande om funktionen hänvisar till en resurs som inte finns.

Använd funktionen [IF](template-functions-logical.md#if) för att se till att funktionen bara utvärderas för villkor när resursen distribueras. Se [funktionen IF](template-functions-logical.md#if) för en exempel-mall som använder `if` och `reference` med en villkorligt distribuerad resurs.

Du anger en [resurs som beroende](define-resource-dependency.md) av en villkorlig resurs precis som med andra resurser. När en villkorlig resurs inte distribueras tar Azure Resource Manager automatiskt bort den från de nödvändiga beroendena.

## <a name="complete-mode"></a>Fullständigt läge

Om du distribuerar en mall med [slutfört läge](deployment-modes.md) och en resurs inte distribueras eftersom `condition` den utvärderas till false beror resultatet på vilken REST API version som du använder för att distribuera mallen. Om du använder en tidigare version än 2019-05-10 **tas inte resursen bort**. Med 2019-05-10 eller senare **tas resursen bort**. De senaste versionerna av Azure PowerShell och Azure CLI tar bort resursen när villkoret är falskt.

## <a name="next-steps"></a>Nästa steg

* En Microsoft Learn-modul som täcker villkorlig distribution finns i [hantera komplexa moln distributioner med hjälp av avancerade funktioner för ARM-mallar](/learn/modules/manage-deployments-advanced-arm-template-features/).
* Rekommendationer om hur du skapar mallar finns i [metod tips för ARM-mallar](template-best-practices.md).
* Om du vill skapa flera instanser av en resurs, se [resurs upprepning i arm-mallar](copy-resources.md).
