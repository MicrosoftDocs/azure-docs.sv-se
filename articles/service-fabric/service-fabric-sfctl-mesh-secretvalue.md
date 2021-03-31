---
title: Azure Service Fabric CLI – sfctl nät secretvalue
description: Lär dig mer om sfctl, Azure Service Fabric Command Line Interface. Innehåller en lista med kommandon för att hämta och ta bort Service Fabric nät secretvalue resurser.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 985fb505aae96f4ebd1ba8aeb61679081f303243
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "86245780"
---
# <a name="sfctl-mesh-secretvalue"></a>sfctl mesh secretvalue
Hämta och ta bort nät secretvalue-resurser.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| delete | Tar bort det angivna värdet för den namngivna hemliga resursen. |
| lista | Lista namn på alla värden för den angivna hemliga resursen. |
| visa | Visar det angivna värdet för den hemliga resursen. |

## <a name="sfctl-mesh-secretvalue-delete"></a>sfctl nät secretvalue ta bort
Tar bort det angivna värdet för den namngivna hemliga resursen.

Tar bort den hemliga värde resurs som identifieras med namnet. Namnet på resursen är vanligt vis den version som är kopplad till det värdet. Det går inte att ta bort om det angivna värdet används.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --Secret-Name-n [required] | Namnet på den hemliga resursen. |
| --version-v [obligatoriskt] | Namnet på den hemliga versionen. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-mesh-secretvalue-list"></a>sfctl nät secretvalue-lista
Lista namn på alla värden för den angivna hemliga resursen.

Hämtar information om alla hemliga värde resurser för den angivna hemliga resursen. Informationen innehåller namnen på de hemliga värde resurserna, men inte de faktiska värdena.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --Secret-Name-n [required] | Namnet på den hemliga resursen. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-mesh-secretvalue-show"></a>sfctl nät secretvalue show
Visar det angivna värdet för den hemliga resursen.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --Secret-Name-n [required] | Namnet på den hemliga resursen. |
| --version-v [obligatoriskt] | Namnet på den hemliga versionen. |
| --show-Value | Visa det faktiska värdet för den hemliga versionen. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |


## <a name="next-steps"></a>Nästa steg
- [Konfigurera](service-fabric-cli.md) Service Fabric cli.
- Lär dig hur du använder Service Fabric CLI med hjälp av [exempel skripten](./scripts/sfctl-upgrade-application.md).
