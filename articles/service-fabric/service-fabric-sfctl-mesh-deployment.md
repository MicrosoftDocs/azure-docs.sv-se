---
title: Azure Service Fabric CLI – sfctl nät distribution
description: Lär dig mer om sfctl, Azure-Service Fabric kommando rads gränssnitt. Innehåller en lista med kommandon för att skapa Service Fabric nät resurser.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: fb2adafab88eb1d3855cdec8268601fb4e15dcbb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "86257279"
---
# <a name="sfctl-mesh-deployment"></a>sfctl mesh deployment
Skapa Service Fabric nät resurser.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| skapa | Skapar en distribution av Service Fabric nät resurser. |

## <a name="sfctl-mesh-deployment-create"></a>sfctl nät distribution skapa
Skapar en distribution av Service Fabric nät resurser.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --yaml-Files [required] | Kommaavgränsade relativa eller absoluta fil Sök vägar för alla yaml-filer eller relativa eller absoluta sökvägen till katalogen (rekursiv) som innehåller yaml-filer. |
| --parametrar | En relativ eller absolut sökväg till en yaml-fil eller ett JSON-objekt som innehåller de parametrar som behöver åsidosättas. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

### <a name="examples"></a>Exempel

Konsoliderar och distribuerar alla resurser till klustret genom att åsidosätta de parametrar som anges i yaml-filen
``` 
sfctl mesh deployment create --input-yaml-files ./app.yaml,./network.yaml --parameters  
./param.yaml    
```

Konsoliderar och distribuerar alla resurser i en katalog till kluster genom att åsidosätta de parametrar som anges i yaml-filen

``` 
sfctl mesh deployment create --input-yaml-files ./resources --parameters ./param.yaml
```

Konsoliderar och distribuerar alla resurser i en katalog till kluster genom att åsidosätta de parametrar som skickas direkt som JSON-objekt
``` 
sfctl mesh deployment create --input-yaml-files ./resources --parameters "{ 'my_param' :    
{'value' : 'my_value'} }"   
```

## <a name="next-steps"></a>Nästa steg
- [Konfigurera](service-fabric-cli.md) Service Fabric cli.
- Lär dig hur du använder Service Fabric CLI med hjälp av [exempel skripten](./scripts/sfctl-upgrade-application.md).
