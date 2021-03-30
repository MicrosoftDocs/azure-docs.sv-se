---
title: Azure Service Fabric CLI – sfctl Store
description: Lär dig mer om sfctl, Azure Service Fabric Command Line Interface. Innehåller en lista med kommandon för att utföra åtgärder på fil nivå på klustrets avbildnings arkiv.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: af5399be44d2946775622598c5c1db3d8355f7cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "86257078"
---
# <a name="sfctl-store"></a>sfctl store
Utför grundläggande åtgärder på filnivå på klustrets avbildnings lager.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| delete | Tar bort befintligt bild lagrings innehåll. |
| rot-info | Hämtar innehålls informationen i roten för avbildnings arkivet. |
| indikerar | Hämtar innehålls informationen för avbildnings arkivet. |

## <a name="sfctl-store-delete"></a>ta bort sfctl Store
Tar bort befintligt bild lagrings innehåll.

Tar bort befintligt avbildnings lagrings innehåll som hittas inom den angivna relativa sökvägen för avbildnings arkivet. Det här kommandot kan användas för att ta bort uppladdade programpaket när de har tillhandahållits.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --innehålls Sök väg [obligatoriskt] | Relativ sökväg till fil eller mapp i avbildnings arkivet från roten. |
| --timeout-t | Tids gränsen för servern för att utföra åtgärden på några sekunder. Denna timeout anger den tids period som klienten vill vänta tills den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard \: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-store-root-info"></a>sfctl Store-rot – info
Hämtar innehålls informationen i roten för avbildnings arkivet.

Returnerar informationen om avbildningens lagrings innehåll i roten på avbildnings lagrings platsen.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --timeout-t | Tids gränsen för servern för att utföra åtgärden på några sekunder. Denna timeout anger den tids period som klienten vill vänta tills den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard \: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-store-stat"></a>sfctl Store stat
Hämtar innehålls informationen för avbildnings arkivet.

Returnerar informationen om avbildningens lagrings innehåll på angiven contentPath. ContentPath är i förhållande till roten för avbildnings arkivet.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --innehålls Sök väg [obligatoriskt] | Relativ sökväg till fil eller mapp i avbildnings arkivet från roten. |
| --timeout-t | Tids gränsen för servern för att utföra åtgärden på några sekunder. Denna timeout anger den tids period som klienten vill vänta tills den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard \: 60. |

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
