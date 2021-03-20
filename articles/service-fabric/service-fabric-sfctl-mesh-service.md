---
title: Azure Service Fabric CLI – sfctl nät tjänst
description: Lär dig mer om sfctl, Azure Service Fabric Command Line Interface. Innehåller en lista med kommandon för att hämta tjänst information för en program resurs.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 68ea876d9951b49a6683cc74df8b9107fd942e51
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "86245678"
---
# <a name="sfctl-mesh-service"></a>sfctl mesh service
Hämta tjänst information och lista över tjänster för en program resurs.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| lista | Visar en lista över alla tjänst resurser. |
| visa | Hämtar tjänst resursen med det aktuella namnet. |

## <a name="sfctl-mesh-service-list"></a>sfctl nät tjänst lista
Visar en lista över alla tjänst resurser.

Hämtar information om alla tjänster i en program resurs. Informationen inkluderar beskrivning och andra egenskaper för tjänsten.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --App-Name--program-Name [required] | Namnet på programmet. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-mesh-service-show"></a>sfctl nät tjänst show
Hämtar tjänst resursen med det aktuella namnet.

Hämtar information om tjänst resursen med det aktuella namnet. Informationen inkluderar beskrivning och andra egenskaper för tjänsten.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --App-Name--program-Name [required] | Namnet på programmet. |
| --Name-n [required] | Namnet på tjänsten. |

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
