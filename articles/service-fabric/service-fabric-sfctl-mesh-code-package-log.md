---
title: Azure Service Fabric CLI – sfctl-nätkod – paket-logg
description: Lär dig mer om sfctl, Azure Service Fabric Command Line Interface. Innehåller en lista med kommandon för att hämta loggar för ett angivet kod paket.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 9ac1d85a1a498f9f6fcd0a03f8f819d1cdfcac33
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "86257310"
---
# <a name="sfctl-mesh-code-package-log"></a>sfctl mesh code-package-log
Hämta loggarna för behållaren för det angivna kod paketet för den angivna tjänst repliken.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| get | Hämtar loggarna från behållaren. |

## <a name="sfctl-mesh-code-package-log-get"></a>sfctl-nätkod-paket-logg hämtning
Hämtar loggarna från behållaren.

Hämtar loggarna för behållaren för det angivna kod paketet för tjänst repliken.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --App-Name--program-Name [required] | Namnet på programmet. |
| --kod-paket namn [obligatoriskt] | Namn på tjänstens kod paket. |
| --replik-Name [required] | Service Fabric replikens namn. |
| --tjänst namn [obligatoriskt] | Namnet på tjänsten. |
| --pilslut | Antal rader som ska visas från slutet av loggarna. Standardvärdet är 100. all för att visa fullständiga loggar. |

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
