---
title: Azure Service Fabric CLI – sfctl nät volym
description: Lär dig mer om sfctl, Azure Service Fabric Command Line Interface. Innehåller en lista med kommandon för att hämta och ta bort volym resurser.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 57efca87aefad346fda175b073409868d21564ae
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "86245524"
---
# <a name="sfctl-mesh-volume"></a>sfctl mesh volume
Hämta och ta bort volym resurser.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| delete | Tar bort volym resursen. |
| lista | Visar en lista över alla volym resurser. |
| visa | Hämtar volym resursen med det aktuella namnet. |

## <a name="sfctl-mesh-volume-delete"></a>sfctl nät volym borttagning
Tar bort volym resursen.

Tar bort volym resursen som identifieras av namnet.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --Name-n [required] | Namnet på volymen. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-mesh-volume-list"></a>sfctl nät volym lista
Visar en lista över alla volym resurser.

Hämtar information om alla volym resurser i en specifik resurs grupp. Informationen inkluderar beskrivning och andra egenskaper för volymen.

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-mesh-volume-show"></a>sfctl nät volym Visa
Hämtar volym resursen med det aktuella namnet.

Hämtar information om volym resursen med det aktuella namnet. Informationen inkluderar beskrivning och andra egenskaper för volymen.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --Name-n [required] | Namnet på volymen. |

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
