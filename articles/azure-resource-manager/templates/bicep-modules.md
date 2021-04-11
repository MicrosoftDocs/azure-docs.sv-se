---
title: Bicep-moduler
description: Beskriver hur du definierar och använder en modul och hur du använder modul omfattningar.
ms.topic: conceptual
ms.date: 03/30/2021
ms.openlocfilehash: 6c325bbbe265e13241119761373985ca4552b158
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105967888"
---
# <a name="use-bicep-modules-preview"></a>Använda bicep-moduler (förhands granskning)

Med bicep kan du dela upp en komplex lösning i moduler. En bicep-modul är en uppsättning av en eller flera resurser som ska distribueras tillsammans. Moduler sammanfattar komplexa detaljer om rå resurs deklarationen, vilket kan öka läsbarheten. Du kan återanvända de här modulerna och dela dem med andra personer. Kombinerat med [specifikationer för mallar](./template-specs.md), skapar det ett sätt för modulär användning och kod åter användning. Bicep-moduler kapslas i en enda ARM-mall med [kapslade mallar](./linked-templates.md#nested-template) för distribution. I bicep hanteras [_dependsOn_](./template-syntax.md#resources) automatiskt.

En själv studie kurs finns i [Självstudier: Lägg till bicep-moduler](./bicep-tutorial-add-modules.md).

## <a name="define-modules"></a>Definiera moduler

Varje bicep-fil kan användas som en modul. En modul visar bara parametrar och utdata som kontrakt till andra bicep-filer. Både parametrar och utdata är valfria.

Följande bicep-fil kan distribueras direkt för att skapa ett lagrings konto eller användas som en modul.  I nästa avsnitt får du se hur du använder moduler:

```bicep
@minLength(3)
@maxLength(11)
param storagePrefix string

@allowed([
  'Standard_LRS'
  'Standard_GRS'
  'Standard_RAGRS'
  'Standard_ZRS'
  'Premium_LRS'
  'Premium_ZRS'
  'Standard_GZRS'
  'Standard_RAGZRS'
])
param storageSKU string = 'Standard_LRS'
param location string

var uniqueStorageName = '${storagePrefix}${uniqueString(resourceGroup().id)}'

resource stg 'Microsoft.Storage/storageAccounts@2019-04-01' = {
  name: uniqueStorageName
  location: location
  sku: {
    name: storageSKU
  }
  kind: 'StorageV2'
  properties: {
    supportsHttpsTrafficOnly: true
  }
}

output storageEndpoint object = stg.properties.primaryEndpoints
```

Utdata används för att skicka värden till de överordnade bicep-filerna.

## <a name="consume-modules"></a>Använda moduler

Använd _modulens_ nyckelord för att använda en modul. Följande bicep-fil distribuerar den resurs som definierats i den modultyp som refereras:

```bicep
@minLength(3)
@maxLength(11)
param namePrefix string
param location string = resourceGroup().location

module stgModule './storageAccount.bicep' = {
  name: 'storageDeploy'
  params: {
    storagePrefix: namePrefix
    location: location
  }
}

output storageEndpoint object = stgModule.outputs.storageEndpoint
```

- **modul**: nyckelord.
- **symboliskt namn** (stgModule): identifierare för modulen.
- **modul fil**: sökvägen till modulen i det här exemplet anges med en relativ sökväg (./storageAccount.bicep). Alla sökvägar i bicep måste anges med hjälp av katalog avgränsaren snedstreck (/) för att säkerställa konsekvent kompilering mellan plattformar. Windows-omvänt snedstreck ( \\ ) stöds inte.
- **_Namn_** egenskapen (storageDeploy) krävs när en modul konsumeras. När bicep genererar mallen IL används det här fältet som namnet på den kapslade distributions resursen, som genereras för modulen:

    ```json
    ...
    ...
    "resources": [
      {
        "type": "Microsoft.Resources/deployments",
        "apiVersion": "2020-10-01",
        "name": "storageDeploy",
        "properties": {
          ...
        }
      }
    ]
    ...
    ```

Hämta ett utdata-värde från en modul genom att hämta egenskap svärdet med syntax som: `stgModule.outputs.storageEndpoint` där `stgModule` är identifieraren för modulen.

## <a name="configure-module-scopes"></a>Konfigurera scope för modulen

När du deklarerar en modul kan du ange en _omfattnings_ egenskap för att ange det omfång som modulen ska distribueras till:

```bicep
module stgModule './storageAccount.bicep' = {
  name: 'storageDeploy'
  scope: resourceGroup('someOtherRg') // pass in a scope to a different resourceGroup
  params: {
    storagePrefix: namePrefix
    location: location
  }
}
```

Egenskapen _omfattning_ kan utelämnas när modulens mål omfång och den överordnade mål omfånget är desamma. När omfattnings egenskapen inte anges distribueras modulen i den överordnade mål omfånget.

Följande bicep-fil visar hur du skapar en resurs grupp och distribuerar en modul till resurs gruppen:

```bicep
// set the target scope for this file
targetScope = 'subscription'

@minLength(3)
@maxLength(11)
param namePrefix string

param location string = deployment().location

var resourceGroupName = '${namePrefix}rg'
resource myResourceGroup 'Microsoft.Resources/resourceGroups@2020-01-01' = {
  name: resourceGroupName
  location: location
  scope: subscription()
}

module stgModule './storageAccount.bicep' = {
  name: 'storageDeploy'
  scope: myResourceGroup
  params: {
    storagePrefix: namePrefix
    location: location
  }
}

output storageEndpoint object = stgModule.outputs.storageEndpoint
```

## <a name="next-steps"></a>Nästa steg

- Information om hur du går igenom självstudierna finns i [Självstudier: Lägg till bicep-moduler](./bicep-tutorial-add-modules.md).
