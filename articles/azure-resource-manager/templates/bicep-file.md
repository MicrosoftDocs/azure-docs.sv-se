---
title: Bicep fil struktur och syntax
description: Beskriver struktur och egenskaper för en bicep-fil med deklarativ syntax.
ms.topic: conceptual
ms.date: 03/31/2021
ms.openlocfilehash: 09993ae9c08f53144de8e94e6555ad93bec681f6
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2021
ms.locfileid: "106168696"
---
# <a name="understand-the-structure-and-syntax-of-bicep-files"></a>Förstå strukturen och syntaxen för bicep-filer

I den här artikeln beskrivs strukturen för en bicep-fil. Den visar de olika avsnitten i filen och de egenskaper som är tillgängliga i dessa avsnitt.

Den här artikeln är avsedd för användare som har en viss välbekanthet med bicep-filer. Den innehåller detaljerad information om mallens struktur. En stegvis själv studie kurs som vägleder dig genom processen med att skapa en bicep-fil finns i [Självstudier: skapa och distribuera den första Azure Resource Manager bicep-filen](bicep-tutorial-create-first-bicep.md).

## <a name="template-format"></a>Mallformat

En bicep-fil har följande element. Elementen kan visas i vilken ordning som helst.

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

I följande exempel visas en implementering av de här elementen.

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

## <a name="target-scope"></a>Mål område

Som standard är mål omfånget inställt på `resourceGroup` . Om du distribuerar på resurs grupps nivå behöver du inte ange mål omfånget i din bicep-fil.

De tillåtna värdena är:

* **resourceGroup** – standardvärde, används för [resurs grupps distributioner](deploy-to-resource-group.md).
* **prenumeration** – används för [prenumerations distributioner](deploy-to-subscription.md).
* **managementGroup** -används för [distributioner av hanterings grupper](deploy-to-management-group.md).
* **klient organisation** – används för [klient distributioner](deploy-to-tenant.md).

## <a name="parameters"></a>Parametrar

Använd parametrar för värden som måste variera för olika distributioner. Du kan definiera ett standardvärde för den parameter som används om inget värde anges under distributionen.

Du kan till exempel lägga till en SKU-parameter för att ange olika storlekar för en resurs. Du kan använda mallar för att skapa standardvärdet, till exempel för att hämta resurs gruppens plats.

```bicep
param storageSKU string = 'Standard_LRS'
param location string = resourceGroup().location
```

För tillgängliga data typer, se [data typer i mallar](data-types.md).

Mer information finns i [parametrar i mallar](template-parameters.md).

## <a name="parameter-decorators"></a>Parameter dekoratörer

Du kan lägga till en eller flera dekoratörer för varje parameter. Dessa dekoratörer definierar de värden som tillåts för parametern. I följande exempel anges de SKU: er som kan distribueras via bicep-filen.

```bicep
@allowed([
  'Standard_LRS'
  'Standard_GRS'
  'Standard_ZRS'
  'Premium_LRS'
])
param storageSKU string = 'Standard_LRS'
```

I följande tabell beskrivs tillgängliga dekoratörer och hur du använder dem.

| Decorator | Tillämpa på | Argument | Beskrivning |
| --------- | ---- | ----------- | ------- |
| gett | all | matris | Tillåtna värden för parametern. Använd den här decorator för att se till att användaren har rätt värden. |
| beskrivning | all | sträng | Text som förklarar hur du använder-parametern. Beskrivningen visas för användare via portalen. |
| Max | matris, sträng | int | Den maximala längden för sträng-och mat ris parametrar. Värdet är inkluderat. |
| maxValue | int | int | Det maximala värdet för heltals parametern. Detta värde är inkluderat. |
| metadata | all | objekt | Anpassade egenskaper som ska användas för parametern. Kan innehålla en beskrivnings egenskap som motsvarar beskrivningen decorator. |
| minLength | matris, sträng | int | Den minsta längden för sträng-och mat ris parametrar. Värdet är inkluderat. |
| minValue | int | int | Det minsta värdet för parametern Integer. Detta värde är inkluderat. |
| skydda | sträng, objekt | inget | Markerar parametern som säker. Värdet för en säker parameter sparas inte i distributions historiken och loggas inte. Mer information finns i [skydda strängar och objekt](data-types.md#secure-strings-and-objects). |

## <a name="variables"></a>Variabler

Använd variabler för komplexa uttryck som upprepas i en bicep-fil. Du kan till exempel lägga till en variabel för ett resurs namn som konstrueras genom att kombinera flera värden.

```bicep
var uniqueStorageName = '${storagePrefix}${uniqueString(resourceGroup().id)}'
```

Du har inte angett någon [datatyp](data-types.md) för en variabel. I stället härleds data typen från värdet.

Mer information finns i [variabler i mallar](template-variables.md).

## <a name="resource"></a>Resurs

Använd `resource` nyckelordet för att definiera en resurs som ska distribueras. Resurs deklarationen innehåller ett symboliskt namn för resursen. Du kommer att använda det symboliska namnet i andra delar av bicep-filen om du behöver hämta ett värde från resursen.

Resurs deklarationen innehåller också resurs typen och API-versionen.

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

I resurs deklarationen inkluderar du egenskaper för resurs typen. De här egenskaperna är unika för varje resurs typ.

Mer information finns i [resurs deklaration i mallar](resource-declaration.md).

Lägg till ett uttryck för att [villkorligt distribuera en resurs](conditional-resource-deployment.md) `if` .

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

Om du vill [distribuera fler än en instans](https://github.com/Azure/bicep/blob/main/docs/spec/loops.md) av en resurs typ lägger du till ett `for` uttryck. Uttrycket kan iterera över medlemmar i en matris.

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

Använd moduler för att länka till andra bicep-filer som innehåller kod som du vill återanvända. Modulen innehåller en eller flera resurser som ska distribueras. Dessa resurser distribueras tillsammans med andra resurser i din bicep-fil.

```bicep
module webModule './webApp.bicep' = {
  name: 'webDeploy'
  params: {
    skuName: 'S1'
    location: location
  }
}
```

Med det symboliska namnet kan du referera till modulen någon annan stans i filen. Du kan till exempel hämta ett värde för utdata från en modul genom att använda det symboliska namnet och namnet på resultatvärdet.

Precis som med resurser kan du villkorligt eller iterativt distribuera en modul. Syntaxen är samma för moduler som resurser.

Mer information finns i [använda bicep-moduler](bicep-modules.md).

## <a name="resource-and-module-decorators"></a>Resurs-och modul dekoratörer

Du kan lägga till en decorator i en resurs-eller modul definition. Den enda decorator som stöds är `batchSize(int)` . Du kan bara använda den för en resurs-eller modul definition som använder ett `for` uttryck.

Som standard distribueras resurserna parallellt. Du vet inte i vilken ordning de slutförs. När du lägger till `batchSize` decorator distribuerar du instanser seriellt. Använd heltals argumentet för att ange antalet instanser som ska distribueras parallellt.

```bicep
@batchSize(3)
resource storageAccountResources 'Microsoft.Storage/storageAccounts@2019-06-01' = [for storageName in storageAccounts: {
  ...
}]
```

Mer information finns i [serie eller parallell](copy-resources.md#serial-or-parallel).

## <a name="outputs"></a>Utdata

Använd utdata för att returnera värdet från distributionen. Normalt returnerar du ett värde från en distribuerad resurs när du behöver återanvända det värdet för en annan åtgärd.

```bicep
output storageEndpoint object = stg.properties.primaryEndpoints
```

Ange en [datatyp](data-types.md) för värdet utdata.

Mer information finns i [utdata i mallar](template-outputs.md).

## <a name="comments"></a>Kommentarer

Använd `//` för kommentarer på en rad eller `/* ... */` för kommentarer på flera rader

I följande exempel visas en enkel rads kommentar.

```bicep
// This is your primary NIC.
resource nic1 'Microsoft.Network/networkInterfaces@2020-06-01' = {
   ...
}
```

I följande exempel visas en flerradig kommentar.

```bicep
/*
  This template assumes the key vault already exists and
  is in same subscription and resource group as the deployment.
*/
param existingKeyVaultName string
```

## <a name="multi-line-strings"></a>Strängar med flera rader

Du kan dela upp en sträng i flera rader. Använd tre enkla citat tecken `'''` för att starta och avsluta multi-line-strängen. 

Tecken i flera rad strängar hanteras som de är. Escape-tecken är onödigt. Du kan inte ta med `'''` i multi-line-strängen. String-interpolation stöds inte för närvarande.

Du kan antingen starta strängen direkt efter att ha öppnat `'''` eller inkludera en ny rad. I båda fallen innehåller den resulterande strängen ingen ny rad. Beroende på rad slutar i bicep-filen tolkas nya rader som `\r\n` eller `\n` .

I följande exempel visas en sträng med flera rader.

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

En introduktion till bicep finns i [Vad är bicep?](bicep-overview.md).
