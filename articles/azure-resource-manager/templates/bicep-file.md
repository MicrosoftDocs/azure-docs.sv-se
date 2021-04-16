---
title: Bicep-filstruktur och syntax
description: Beskriver strukturen och egenskaperna för en Bicep-fil med hjälp av deklarativ syntax.
ms.topic: conceptual
ms.date: 03/31/2021
ms.openlocfilehash: 1b8eddd388878be8f653f963ef967cf2c0af685f
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537863"
---
# <a name="understand-the-structure-and-syntax-of-bicep-files"></a>Förstå strukturen och syntaxen för Bicep-filer

I den här artikeln beskrivs strukturen för en Bicep-fil. Den visar de olika avsnitten i filen och de egenskaper som är tillgängliga i dessa avsnitt.

Den här artikeln är avsedd för användare som har viss kunskap om Bicep-filer. Den innehåller detaljerad information om mallens struktur. En stegvis självstudiekurs som vägleder dig genom processen för att skapa en Bicep-fil finns i Självstudie: Skapa och distribuera första [Azure Resource Manager Bicep-filen](bicep-tutorial-create-first-bicep.md).

## <a name="template-format"></a>Mallformat

En Bicep-fil har följande element. Elementen kan visas i valfri ordning.

```bicep
targetScope = '<scope>'

@<decorator>(<argument>)
param <parameter-name> <parameter-data-type> = <default-value>

var <variable-name> = <variable-value>

resource <resource-symbolic-name> '<resource-type>@<api-version>' = {
  <resource-properties>
}

// conditional deployment
resource <resource-symbolic-name> '<resource-type>@<api-version>' = if (<condition-to-deploy>) {
  <resource-properties>
}

// iterative deployment
@<decorator>(<argument>)
resource <resource-symbolic-name> '<resource-type>@<api-version>' = [for <item> in <collection>: {
  <resource-properties>
}]

module <module-symbolic-name> '<path-to-file>' = {
  name: '<linked-deployment-name>'
  params: {
    <parameter-names-and-values>
  }
}

// conditional deployment
module <module-symbolic-name> '<path-to-file>' = if (<condition-to-deploy>) {
  name: '<linked-deployment-name>'
  params: {
    <parameter-names-and-values>
  }
}

// iterative deployment
module <module-symbolic-name> '<path-to-file>' = [for <item> in <collection>: {
  name: '<linked-deployment-name>'
  params: {
    <parameter-names-and-values>
  }
}]

output <output-name> <output-data-type> = <output-value>
```

I följande exempel visas en implementering av dessa element.

```bicep
@minLength(3)
@maxLength(11)
param storagePrefix string

param storageSKU string = 'Standard_LRS'
param location string = resourceGroup().location

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

module webModule './webApp.bicep' = {
  name: 'webDeploy'
  params: {
    skuName: 'S1'
    location: location
  }
}

output storageEndpoint object = stg.properties.primaryEndpoints
```

## <a name="target-scope"></a>Målomfång

Som standard är målomfånget inställt på `resourceGroup` . Om du distribuerar på resursgruppsnivå behöver du inte ange målomfånget i bicep-filen.

Tillåtna värden är:

* **resourceGroup** – standardvärde som används för [resursgruppsdistributioner](deploy-to-resource-group.md).
* **subscription** – används för [prenumerationsdistributioner](deploy-to-subscription.md).
* **managementGroup** – används för [distribution av hanteringsgrupper.](deploy-to-management-group.md)
* **tenant** – används för [klientdistributioner](deploy-to-tenant.md).

## <a name="parameters"></a>Parametrar

Använd parametrar för värden som behöver variera för olika distributioner. Du kan definiera ett standardvärde för parametern som används om inget värde anges under distributionen.

Du kan till exempel lägga till en SKU-parameter för att ange olika storlekar för en resurs. Du kan använda mallfunktioner för att skapa standardvärdet, till exempel hämta resursgruppens plats.

```bicep
param storageSKU string = 'Standard_LRS'
param location string = resourceGroup().location
```

Information om tillgängliga datatyper finns i [Datatyper i mallar.](data-types.md)

Mer information finns i [Parametrar i mallar.](template-parameters.md)

## <a name="parameter-decorators"></a>Parameterdekoratorer

Du kan lägga till en eller flera decorators för varje parameter. Dessa decorators definierar de värden som tillåts för parametern. I följande exempel anges de SKU:er som kan distribueras via Bicep-filen.

```bicep
@allowed([
  'Standard_LRS'
  'Standard_GRS'
  'Standard_ZRS'
  'Premium_LRS'
])
param storageSKU string = 'Standard_LRS'
```

I följande tabell beskrivs tillgängliga decorators och hur du använder dem.

| Dekoratör | Tillämpa på | Argument | Description |
| --------- | ---- | ----------- | ------- |
| Tillåtet | all | matris | Tillåtna värden för parametern . Använd den här decorator för att se till att användaren anger rätt värden. |
| beskrivning | all | sträng | Text som förklarar hur du använder parametern . Beskrivningen visas för användarna via portalen. |
| Maxlength | matris, sträng | int | Maximal längd för sträng- och matrisparametrar. Värdet är inkluderande. |
| Maxvalue | int | int | Maxvärdet för heltalsparametern. Det här värdet är inkluderande. |
| metadata | all | objekt | Anpassade egenskaper som ska tillämpas på parametern . Kan innehålla en beskrivningsegenskap som motsvarar beskrivningsdekoratorn. |
| Minlength | matris, sträng | int | Minsta längd för sträng- och matrisparametrar. Värdet är inkluderande. |
| minValue | int | int | Minimivärdet för heltalsparametern. Det här värdet är inkluderande. |
| Säker | sträng, objekt | inget | Markerar parametern som säker. Värdet för en säker parameter sparas inte i distributionshistoriken och loggas inte. Mer information finns i [Skydda strängar och objekt.](data-types.md#secure-strings-and-objects) |

## <a name="variables"></a>Variabler

Använd variabler för komplexa uttryck som upprepas i en Bicep-fil. Du kan till exempel lägga till en variabel för ett resursnamn som skapas genom att sammanfoga flera värden.

```bicep
var uniqueStorageName = '${storagePrefix}${uniqueString(resourceGroup().id)}'
```

Du anger inte någon [datatyp för](data-types.md) en variabel. I stället här härledas datatypen från värdet .

Mer information finns i [Variabler i mallar.](template-variables.md)

## <a name="resource"></a>Resurs

Använd `resource` nyckelordet för att definiera en resurs som ska distribueras. Resursdeklarationen innehåller ett symboliskt namn för resursen. Du använder det här symboliska namnet i andra delar av Bicep-filen om du behöver hämta ett värde från resursen.

Resursdeklarationen innehåller även resurstypen och API-versionen.

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
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
```

I resursdeklarationen inkluderar du egenskaper för resurstypen. De här egenskaperna är unika för varje resurstyp.

Mer information finns i [Resursdeklaration i mallar](resource-declaration.md).

Om [du vill distribuera en resurs villkorligt](conditional-resource-deployment.md)lägger du till ett `if` uttryck.

```bicep
resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = if (newOrExisting == 'new') {
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
```

Om [du vill distribuera fler än en instans](https://github.com/Azure/bicep/blob/main/docs/spec/loops.md) av en resurstyp lägger du till ett `for` uttryck. Uttrycket kan iterera över medlemmar i en matris.

```bicep
resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = [for storageName in storageAccounts: {
  name: storageName
  location: location
  sku: {
    name: storageSKU
  }
  kind: 'StorageV2'
  properties: {
    supportsHttpsTrafficOnly: true
  }
}]
```

## <a name="modules"></a>Moduler

Använd moduler för att länka till andra Bicep-filer som innehåller kod som du vill återanvända. Modulen innehåller en eller flera resurser att distribuera. Dessa resurser distribueras tillsammans med andra resurser i din Bicep-fil.

```bicep
module webModule './webApp.bicep' = {
  name: 'webDeploy'
  params: {
    skuName: 'S1'
    location: location
  }
}
```

Med det symboliska namnet kan du referera till modulen från någon annan plats i filen. Du kan till exempel hämta ett utdatavärde från en modul med hjälp av det symboliska namnet och namnet på utdatavärdet.

Precis som med resurser kan du villkorligt eller iterativt distribuera en modul. Syntaxen är samma för moduler som resurser.

Mer information finns i Använda [Bicep-moduler.](bicep-modules.md)

## <a name="resource-and-module-decorators"></a>Resurs- och moduldekoratorer

Du kan lägga till en decorator till en resurs- eller moduldefinition. Den enda decorator som stöds är `batchSize(int)` . Du kan bara tillämpa den på en resurs- eller moduldefinition som använder ett `for` uttryck.

Som standard distribueras resurser parallellt. Du vet inte i vilken ordning de slutförs. När du lägger `batchSize` till encorator distribuerar du instanserna seriellt. Använd heltalsargumentet för att ange antalet instanser som ska distribueras parallellt.

```bicep
@batchSize(3)
resource storageAccountResources 'Microsoft.Storage/storageAccounts@2019-06-01' = [for storageName in storageAccounts: {
  ...
}]
```

Mer information finns i [Seriell eller Parallell](copy-resources.md#serial-or-parallel).

## <a name="outputs"></a>Utdata

Använd utdata för att returnera värdet från distributionen. Normalt returnerar du ett värde från en distribuerad resurs när du behöver återanvända värdet för en annan åtgärd.

```bicep
output storageEndpoint object = stg.properties.primaryEndpoints
```

Ange en [datatyp](data-types.md) för utdatavärdet.

Mer information finns i [Utdata i mallar.](template-outputs.md)

## <a name="comments"></a>Kommentarer

Använd `//` för enkelradskommentarer `/* ... */` eller för flerradskommentarer

I följande exempel visas en kommentar med en rad.

```bicep
// This is your primary NIC.
resource nic1 'Microsoft.Network/networkInterfaces@2020-06-01' = {
   ...
}
```

I följande exempel visas en flerradskommentar.

```bicep
/*
  This template assumes the key vault already exists and
  is in same subscription and resource group as the deployment.
*/
param existingKeyVaultName string
```

## <a name="multi-line-strings"></a>Flerradssträngar

Du kan dela upp en sträng i flera rader. Använd tre enkla citattecken `'''` för att starta och avsluta flerradssträngen.

Tecken i flerradssträngen hanteras som de är. Escape-tecken är onödiga. Du kan inte inkludera `'''` i flerradssträngen. Stränginterpolering stöds inte för närvarande.

Du kan antingen starta strängen direkt efter öppningen `'''` eller inkludera en ny rad. I båda fallen innehåller inte den resulterande strängen någon ny rad. Beroende på radsluten i Bicep-filen tolkas nya rader som `\r\n` eller `\n` .

I följande exempel visas en flerradssträng.

```bicep
var stringVar = '''
this is multi-line
  string with formatting
  preserved.
'''
```

Föregående exempel motsvarar följande JSON.

```json
"variables": {
  "stringVar": "this is multi-line\r\n  string with formatting\r\n  preserved.\r\n"
}
```

## <a name="next-steps"></a>Nästa steg

En introduktion till Bicep finns i [Vad är Bicep?](bicep-overview.md).
