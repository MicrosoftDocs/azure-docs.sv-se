---
title: Azure Service Fabric CLI – sfctl-Skriv
description: Lär dig mer om sfctl, Azure Service Fabric Command Line Interface. Innehåller en lista med kommandon för Docker Compose-program.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: e84b35ff3dfa7be0c848a4d7b906c622d9b88914
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "86257345"
---
# <a name="sfctl-compose"></a>sfctl compose
Skapa, ta bort och hantera Docker Compose-program.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| skapa | Skapar en Service Fabric Skriv distribution. |
| lista | Hämtar listan över Skriv distributioner som skapats i Service Fabric klustret. |
| ta bort | Tar bort en befintlig Service Fabric skapar distribution från klustret. |
| status | Hämtar information om en Service Fabric Skriv-distribution. |
| uppgradera | Börjar uppgradera en Skriv distribution i Service Fabric klustret. |
| uppgradera-återställning | Börjar återställa en uppgradering av Skriv distributionen i Service Fabric klustret. |
| uppgradering-status | Hämtar information om den senaste uppgraderingen som utförts på den här Service Fabric Skriv distributionen. |

## <a name="sfctl-compose-create"></a>sfctl skapa
Skapar en Service Fabric Skriv distribution.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --distributions namn [obligatoriskt] | Namnet på distributionen. |
| --File-Path [required] | Sökväg till mål Docker-filen. |
| --krypterad-pass | Använd redan krypterad pass fras i stället för att uppmanas att ange ett lösen ord för behållar registret. |
| --har-pass | Kommer att uppmanas att ange ett lösen ord till behållar registret. |
| --timeout-t | Standard \: 60. |
| --användare | Användar namn för att ansluta till behållar registret. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-compose-list"></a>sfctl Skriv lista
Hämtar listan över Skriv distributioner som skapats i Service Fabric klustret.

Hämtar status för de Skriv distributioner som skapades eller håller på att skapas i Service Fabric klustret. Svaret innehåller namn, status och annan information om Skriv distributionerna. Om listan över distributioner inte får plats på en sida returneras en resultat sida och en fortsättnings-token som kan användas för att hämta nästa sida.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --fortsättnings-token | Parametern för fortsatt token används för att hämta nästa uppsättning resultat. En fortsättnings-token med ett värde som inte är tom inkluderas i svaret på API: et när resultatet från systemet inte passar i ett enda svar. När det här värdet skickas till nästa API-anrop returnerar API nästa uppsättning resultat. Om det inte finns några ytterligare resultat innehåller inte fortsättnings-token ett värde. Värdet för den här parametern får inte vara URL-kodat. |
| --Max-resultat | Det maximala antalet resultat som ska returneras som en del av de växlade frågorna. Den här parametern definierar den övre kanten på antalet returnerade resultat. Resultaten som returneras kan vara mindre än de angivna maximala resultaten om de inte får plats i meddelandet enligt de Max begränsningar för meddelande storlek som definierats i konfigurationen. Om den här parametern är noll eller inte anges, innehåller den växlade frågan så många resultat som möjligt som passar i retur meddelandet. |
| --timeout-t | Tids gränsen för servern för att utföra åtgärden på några sekunder. Denna timeout anger den tids period som klienten vill vänta tills den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard \: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-compose-remove"></a>sfctl Skriv Remove
Tar bort en befintlig Service Fabric skapar distribution från klustret.

Tar bort en befintlig Service Fabric Skriv distribution.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --distributions namn [obligatoriskt] | Distributionens identitet. |
| --timeout-t | Tids gränsen för servern för att utföra åtgärden på några sekunder. Denna timeout anger den tids period som klienten vill vänta tills den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard \: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-compose-status"></a>status för sfctl-sammanställning
Hämtar information om en Service Fabric Skriv-distribution.

Returnerar statusen för den Skriv distribution som skapades eller håller på att skapas i Service Fabric klustret och vars namn matchar det som angavs som parameter. Svaret innehåller namn, status och annan information om distributionen.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --distributions namn [obligatoriskt] | Distributionens identitet. |
| --timeout-t | Tids gränsen för servern för att utföra åtgärden på några sekunder. Denna timeout anger den tids period som klienten vill vänta tills den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard \: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-compose-upgrade"></a>sfctl-Skriv uppgradering
Börjar uppgradera en Skriv distribution i Service Fabric klustret.

Verifierar de angivna uppgraderings parametrarna och börjar uppgradera distributionen om parametrarna är giltiga.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --distributions namn [obligatoriskt] | Namnet på distributionen. |
| --File-Path [required] | Sökväg till mål Docker-filen. |
| --standard-SVC-typ-Health-Map | JSON-kodad ord lista som beskriver hälso principen som används för att utvärdera tjänsternas hälso tillstånd. |
| --krypterad-pass | Använd redan krypterad pass fras i stället för att uppmanas att ange ett lösen ord för behållar registret. |
| --Failure-åtgärd | Möjliga värden är \: "ogiltig", "rollback", "Manual". |
| --Framtvinga omstart | Processerna startas om under uppgraderingen även när kod versionen inte har ändrats. <br><br> Uppgraderingen ändrar bara konfiguration eller data. |
| --har-pass | Kommer att uppmanas att ange ett lösen ord till behållar registret. |
| --hälso kontroll-försök igen | Hur lång tid det tar mellan försök att utföra hälso kontroller om programmet eller klustret inte är felfritt. |
| --hälso kontroll – stabil | Hur lång tid programmet eller klustret måste vara felfritt innan uppgraderingen fortsätter till nästa uppgraderings domän. <br><br> Den tolkas först som en sträng som representerar en varaktighet på ISO 8601. Om detta Miss lyckas tolkas det som ett tal som representerar det totala antalet millisekunder. |
| --hälso kontroll-vänta | Vänte tiden när en uppgraderings domän har slutförts innan hälso kontrollerna påbörjas. |
| --replik-ange-check | Maximal tid det tar att blockera bearbetningen av en uppgraderings domän och förhindra tillgänglighet när det uppstår oväntade problem. <br><br> När tids gränsen går ut fortsätter bearbetningen av uppgraderings domänen oavsett problem med tillgänglighets förlust. Tids gränsen återställs i början av varje uppgraderings domän. Giltiga värden är mellan 0 och 42949672925. |
| --SVC-typ-Health-Map | JSON-kodad lista över objekt som beskriver hälso principerna som används för att utvärdera hälso tillståndet för olika tjänst typer. |
| --timeout-t | Standard \: 60. |
| --ej felfri – app | Högsta tillåtna procent andel felaktiga program innan ett fel rapporteras. <br><br> Om du till exempel vill att 10% av programmen ska vara felaktiga, skulle värdet vara 10. Procent andelen visar den maximala procent andelen program som kan vara felfria innan klustret betraktas som ett fel. Om procent andelen respekteras men det finns minst ett ohälsosamt program, utvärderas hälsan som varning. Detta beräknas genom att antalet felaktiga program divideras över det totala antalet program instanser i klustret. |
| --uppgradering-Domain-timeout | Hur lång tid varje uppgraderings domän måste vara slutförd innan FailureAction körs. <br><br> Den tolkas först som en sträng som representerar en varaktighet på ISO 8601. Om detta Miss lyckas tolkas det som ett tal som representerar det totala antalet millisekunder. |
| --uppgraderings typ | Standard \: rullande. |
| --uppgraderings läge | Möjliga värden är \: "ogiltig", "UnmonitoredAuto", "UnmonitoredManual", "övervakad".  Standard \: UnmonitoredAuto. |
| --uppgraderings-timeout | Den tid som den övergripande uppgraderingen måste slutföras innan FailureAction körs. <br><br> Den tolkas först som en sträng som representerar en varaktighet på ISO 8601. Om detta Miss lyckas tolkas det som ett tal som representerar det totala antalet millisekunder. |
| --användare | Användar namn för att ansluta till behållar registret. |
| --Varning-as-Error | Anger om varningar behandlas med samma allvarlighets grad som fel. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-compose-upgrade-rollback"></a>sfctl-Skriv uppgradering – återställning
Börjar återställa en uppgradering av Skriv distributionen i Service Fabric klustret.

Återställa en uppgradering av Service Fabric-distribution.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --distributions namn [obligatoriskt] | Distributionens identitet. |
| --timeout-t | Tids gränsen för servern för att utföra åtgärden på några sekunder. Denna timeout anger den tids period som klienten vill vänta tills den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard \: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-compose-upgrade-status"></a>sfctl Skriv Upgrade-status
Hämtar information om den senaste uppgraderingen som utförts på den här Service Fabric Skriv distributionen.

Returnerar information om tillståndet för uppgraderingen av Skriv distributionen tillsammans med information som hjälper till att felsöka program hälso problem.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --distributions namn [obligatoriskt] | Distributionens identitet. |
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
