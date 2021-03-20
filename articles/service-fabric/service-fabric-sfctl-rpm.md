---
title: Azure Service Fabric CLI – sfctl rpm
description: Lär dig mer om sfctl, Azure Service Fabric Command Line Interface. Innehåller en lista med kommandon för tjänsten Repair Manager.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 7317fd66303aaabf5232106aa7391439880bebaf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "86260291"
---
# <a name="sfctl-rpm"></a>sfctl rpm
Fråga och skicka kommandon till tjänsten Repair Manager.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| Godkänn-Force | Tvingar godkännandet av den aktuella reparations uppgiften. |
| delete | Tar bort en slutförd reparations uppgift. |
| lista | Hämtar en lista över reparations aktiviteter som matchar de aktuella filtren. |

## <a name="sfctl-rpm-approve-force"></a>sfctl rpm-godkännande-Force
Tvingar godkännandet av den aktuella reparations uppgiften.

Detta API stöder Service Fabric plattform; den är inte avsedd att användas direkt från din kod.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --aktivitets-ID [obligatoriskt] | ID för reparations uppgiften. |
| --version | Reparations uppgiftens aktuella versions nummer. Om den inte är noll kommer begäran endast att lyckas om det här värdet matchar den faktiska aktuella versionen av reparations aktiviteten. Om värdet är noll utförs ingen versions kontroll. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-rpm-delete"></a>ta bort sfctl rpm
Tar bort en slutförd reparations uppgift.

Detta API stöder Service Fabric plattform; den är inte avsedd att användas direkt från din kod.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --aktivitets-ID [obligatoriskt] | ID för den slutförda reparations uppgiften som ska tas bort. |
| --version | Reparations uppgiftens aktuella versions nummer. Om den inte är noll kommer begäran endast att lyckas om det här värdet matchar den faktiska aktuella versionen av reparations aktiviteten. Om värdet är noll utförs ingen versions kontroll. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-rpm-list"></a>sfctl rpm-lista
Hämtar en lista över reparations aktiviteter som matchar de aktuella filtren.

Detta API stöder Service Fabric plattform; den är inte avsedd att användas direkt från din kod.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --utförar-filter | Namnet på den reparations-utförar vars begärda uppgifter ska tas med i listan. |
| --tillstånd-filter | Ett bitvis-eller av följande värden som anger vilka uppgifts tillstånd som ska ingå i resultat listan. <ul><li>1 – skapad</li><li>2-begärd</li><li>4 – förbereder</li><li>8-godkänd</li><li>16-körning</li><li>32 – återställning</li><li>64-slutförd</li></ul>
| --aktivitets-ID-filter | Det ID-prefix för reparations aktivitet som ska matchas. |

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
