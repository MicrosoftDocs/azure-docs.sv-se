---
title: Azure Service Fabric CLI – sfctl nät tjänst – replik
description: Lär dig mer om sfctl, Azure Service Fabric Command Line Interface. Innehåller en lista med kommandon för att hämta replik information för en program resurs.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: cbfdba30663e2aa531ab1db955b0e035a0588709
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "86245729"
---
# <a name="sfctl-mesh-service-replica"></a>sfctl mesh service-replica
Hämta replik information och lista repliker för en specifik tjänst i en program resurs.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| lista | Visar en lista över alla repliker av en tjänst. |
| visa | Hämtar den aktuella repliken av tjänsten för ett program. |

## <a name="sfctl-mesh-service-replica-list"></a>sfctl nät tjänst – replik lista
Visar en lista över alla repliker av en tjänst.

Hämtar information om alla repliker av en tjänst. Informationen inkluderar beskrivning och andra egenskaper för tjänst repliken.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --App-Name--program-Name [required] | Namnet på programmet. |
| --tjänst namn [obligatoriskt] | Namnet på tjänsten. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-mesh-service-replica-show"></a>sfctl nät tjänst – replik show
Hämtar den aktuella repliken av tjänsten för ett program.

Hämtar information om tjänst repliken med det aktuella namnet. Informationen inkluderar beskrivning och andra egenskaper för tjänst repliken.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --App-Name--program-Name [required] | Namnet på programmet. |
| --Name-n [required] | Namnet på tjänst repliken. |
| --tjänst namn [obligatoriskt] | Namnet på tjänsten. |

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
