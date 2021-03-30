---
title: Azure Service Fabric CLI – sfctl nät hemlighet
description: Lär dig mer om sfctl, Azure Service Fabric Command Line Interface. Innehåller en lista med kommandon för att hämta och ta bort Service Fabric nät hemliga resurser.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 2c91a59e57613934c946e3cd56089b330893a51a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "86260335"
---
# <a name="sfctl-mesh-secret"></a>sfctl mesh secret
Hämta och ta bort resurser för nät hemlighet.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| delete | Tar bort den hemliga resursen. |
| lista | Visar en lista över alla hemliga resurser. |
| visa | Hämtar den hemliga resursen med det aktuella namnet. |

## <a name="sfctl-mesh-secret-delete"></a>sfctl nät hemlighet Delete
Tar bort den hemliga resursen.

Tar bort den angivna hemliga resursen och alla dess namngivna värden.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --Name-n [required] | Namnet på den hemliga resursen. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-mesh-secret-list"></a>sfctl nät hemlig lista
Visar en lista över alla hemliga resurser.

Hämtar information om alla hemliga resurser i en specifik resurs grupp. Informationen inkluderar beskrivning och andra egenskaper för hemligheten.

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-mesh-secret-show"></a>sfctl nät hemlighet show
Hämtar den hemliga resursen med det aktuella namnet.

Hämtar information om den hemliga resursen med det aktuella namnet. Informationen inkluderar beskrivning och andra egenskaper för hemligheten.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --Name-n [required] | Namnet på den hemliga resursen. |

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
