---
title: Azure Service Fabric CLI – sfctl-nod
description: Lär dig mer om sfctl, Azure Service Fabric Command Line Interface. Innehåller en lista med kommandon för att hantera klusternoder.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: f2cf1011db37892f71bdd06f409cec1a76463507
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "86257153"
---
# <a name="sfctl-node"></a>sfctl node
Hantera noderna som utgör ett kluster.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| Lägg till konfiguration-parameter-Overrides | Lägger till listan över konfigurations åsidosättningar på den angivna noden. |
| inaktivera | Inaktivera en Service Fabric klusternod med angivet inaktive rings syfte. |
| Aktivera | Aktivera en Service Fabric klusternod som för närvarande är inaktive rad. |
| Get-Configuration-Overrides | Hämtar listan över konfigurations åsidosättningar på den angivna noden. |
| attestering | Hämtar hälsan för en Service Fabric-nod. |
| information | Hämtar information om en angiven nod i Service Fabric klustret. |
| lista | Hämtar listan över noder i Service Fabric klustret. |
| läsa in | Hämtar inläsnings information för en Service Fabric-nod. |
| ta bort konfiguration – åsidosättningar | Tar bort konfigurations åsidosättningar på den angivna noden. |
| ta bort tillstånd | Meddelar Service Fabric att det beständiga läget på en nod har tagits bort permanent eller förlorats. |
| rapportera hälsa | Skickar en hälso rapport på Service Fabric-noden. |
| restart | Startar om en Service Fabric klusternod. |
| period | Startar eller stoppar en klusternod. |
| över gång-status | Hämtar förloppet för en åtgärd som startades med StartNodeTransition. |

## <a name="sfctl-node-add-configuration-parameter-overrides"></a>sfctl Node Add-Configuration-parameter-Overrides
Lägger till listan över konfigurations åsidosättningar på den angivna noden.

Med det här API: et kan du lägga till alla befintliga konfigurations åsidosättningar på den angivna noden.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --config-parameter-override-list [required] | Beskrivning för att lägga till en lista över konfigurations åsidosättningar. |
| --Node-Name [required] | Nodens namn. |
| --Force | Framtvinga tillägg av konfigurations åsidosättningar på angivna noder. |
| --timeout-t | Tids gränsen för servern för att utföra åtgärden på några sekunder. Denna timeout anger den tids period som klienten vill vänta tills den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard \: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-node-disable"></a>Inaktivera sfctl-nod
Inaktivera en Service Fabric klusternod med angivet inaktive rings syfte.

Inaktivera en Service Fabric klusternod med angivet inaktive rings syfte. När inaktive ringen har slutförts kan inaktive rings avsikten ökas, men inte minskas (till exempel kan en nod som inaktive ras med paus avsikten inaktive ras ytterligare med omstart, men inte på det andra sättet. Noder kan återaktiveras med hjälp av åtgärden aktivera en nod när som helst när de har inaktiverats. Om inaktive ringen inte är slutförd avbryts inaktive ringen. En nod som slutar fungera och som är inaktive rad och som är inaktive rad måste fortfarande aktive ras igen innan tjänsterna placeras på den noden.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --Node-Name [required] | Nodens namn. |
| --inaktive ring | Beskriver avsikten eller anledningen till att inaktivera noden. De möjliga värdena är följande. |
| --timeout-t | Tids gränsen för servern för att utföra åtgärden på några sekunder. Denna timeout anger den tids period som klienten vill vänta tills den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard \: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-node-enable"></a>Aktivera sfctl-noden
Aktivera en Service Fabric klusternod som för närvarande är inaktive rad.

Aktiverar en Service Fabric klusternod som för närvarande är inaktive rad. När den är aktive rad blir noden återigen ett giltigt mål för att placera nya repliker, och eventuella inaktiverade repliker på noden återaktiveras.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --Node-Name [required] | Nodens namn. |
| --timeout-t | Tids gränsen för servern för att utföra åtgärden på några sekunder. Denna timeout anger den tids period som klienten vill vänta tills den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard \: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-node-get-configuration-overrides"></a>sfctl Node get-Configuration-Overrides
Hämtar listan över konfigurations åsidosättningar på den angivna noden.

Med detta API kan du hämta alla befintliga konfigurations åsidosättningar på den angivna noden.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --Node-Name [required] | Nodens namn. |
| --timeout-t | Tids gränsen för servern för att utföra åtgärden på några sekunder. Denna timeout anger den tids period som klienten vill vänta tills den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard \: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-node-health"></a>sfctl-nods hälsa
Hämtar hälsan för en Service Fabric-nod.

Hämtar hälsan för en Service Fabric-nod. Använd EventsHealthStateFilter för att filtrera den samling hälso händelser som rapporteras på noden baserat på hälso tillståndet. Om den nod som du anger efter namn inte finns i hälso lagret returneras ett fel.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --Node-Name [required] | Nodens namn. |
| --händelser-hälso tillstånd – filter | Tillåter filtrering av samlingen av HealthEvent-objekt som returneras baserat på hälso tillstånd. De möjliga värdena för den här parametern är heltals värde för något av följande hälso tillstånd. Endast händelser som matchar filtret returneras. Alla händelser används för att utvärdera det sammanlagda hälso tillståndet. Om inget anges returneras alla poster. Tillstånds värden är flaggning-baserad uppräkning, så värdet kan vara en kombination av dessa värden, erhållna med hjälp av den bitvisa operatorn eller. Om det angivna värdet till exempel är 6 returneras alla händelser med hälso tillstånd svärdet OK (2) och varning (4).  <br> -Standard-standardvärdet. Matchar alla hälso tillstånd. Värdet är noll.  <br> -Inget – filter som inte matchar något värde för hälso tillstånd. Används för att returnera inga resultat för en specifik samling av tillstånd. Värdet är 1.  <br> – OK-filter som matchar inmatade hälso tillstånds värden OK. Värdet är 2.  <br> -Varnings filter som matchar inmatade värde varningar för hälso tillstånd. Värdet är 4.  <br> -Fel-filter som matchar InInformationen med hälso tillstånds värde fel. Värdet är 8.  <br> – Alla – filter som matchar indatamängden med ett värde för hälso tillstånd. Värdet är 65535. |
| --timeout-t | Tids gränsen för servern för att utföra åtgärden på några sekunder. Denna timeout anger den tids period som klienten vill vänta tills den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard \: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-node-info"></a>information om sfctl-nod
Hämtar information om en angiven nod i Service Fabric klustret.

Svaret innehåller namn, status, ID, hälsa, drift tid och annan information om noden.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --Node-Name [required] | Nodens namn. |
| --timeout-t | Tids gränsen för servern för att utföra åtgärden på några sekunder. Denna timeout anger den tids period som klienten vill vänta tills den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard \: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-node-list"></a>lista över sfctl-noder
Hämtar listan över noder i Service Fabric klustret.

Svaret innehåller namn, status, ID, hälsa, drift tid och annan information om noderna.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --fortsättnings-token | Parametern för fortsatt token används för att hämta nästa uppsättning resultat. En fortsättnings-token med ett värde som inte är tom inkluderas i svaret på API: et när resultatet från systemet inte passar i ett enda svar. När det här värdet skickas till nästa API-anrop returnerar API nästa uppsättning resultat. Om det inte finns några ytterligare resultat innehåller inte fortsättnings-token ett värde. Värdet för den här parametern får inte vara URL-kodat. |
| --Max-resultat | Det maximala antalet resultat som ska returneras som en del av de växlade frågorna. Den här parametern definierar den övre kanten på antalet returnerade resultat. Resultaten som returneras kan vara mindre än de angivna maximala resultaten om de inte får plats i meddelandet enligt de Max begränsningar för meddelande storlek som definierats i konfigurationen. Om den här parametern är noll eller inte anges, innehåller den växlade frågan så många resultat som möjligt som passar i retur meddelandet. |
| --Node-status-filter | Tillåter filtrering av noderna baserat på NodeStatus. Det går bara att returnera de noder som matchar det angivna filtervärdet. Filtervärdet kan vara något av följande.  Standard \: . |
| --timeout-t | Tids gränsen för servern för att utföra åtgärden på några sekunder. Denna timeout anger den tids period som klienten vill vänta tills den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard \: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-node-load"></a>sfctl Node load
Hämtar inläsnings information för en Service Fabric-nod.

Hämtar inläsnings information för en Service Fabric nod för alla mått som har belastning eller kapacitet definierat.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --Node-Name [required] | Nodens namn. |
| --timeout-t | Tids gränsen för servern för att utföra åtgärden på några sekunder. Denna timeout anger den tids period som klienten vill vänta tills den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard \: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-node-remove-configuration-overrides"></a>sfctl Node Remove-Configuration-Overrides
Tar bort konfigurations åsidosättningar på den angivna noden.

Med detta API kan du ta bort alla befintliga konfigurations åsidosättningar på den angivna noden.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --Node-Name [required] | Nodens namn. |
| --timeout-t | Tids gränsen för servern för att utföra åtgärden på några sekunder. Denna timeout anger den tids period som klienten vill vänta tills den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard \: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-node-remove-state"></a>sfctl Node Remove-State
Meddelar Service Fabric att det beständiga läget på en nod har tagits bort permanent eller förlorats.

Detta innebär att det inte går att återställa det sparade läget för noden. Detta inträffar vanligt vis om en hård disk rensats rent eller om en hård disk kraschar. Noden måste vara avstängd för att åtgärden ska lyckas. Den här åtgärden låter Service Fabric veta att replikerna på noden inte längre finns och att Service Fabric ska sluta vänta på att de repliker ska komma att säkerhets kopie ras. Kör inte denna cmdlet om läget på noden inte har tagits bort och noden kan komma att säkerhets kopie ras med sitt tillstånd intakt. Från Service Fabric 6,5, för att kunna använda det här API: t för Seed-noder, ändra startnoderna till vanliga (icke-start) noder och anropa sedan detta API för att ta bort nodens tillstånd. Om klustret körs på Azure kommer Service Fabric att försöka ändra det automatiskt till en nod som inte är en STARTNOD, efter att noden är nere. Detta gör du genom att kontrol lera att antalet icke-startnoder i den primära nodtypen inte är mindre än antalet startnoder. Om det behövs kan du lägga till fler noder till den primära nodtypen för att uppnå detta. För fristående kluster, om noden inte förväntas komma att säkerhets kopie ras med sitt tillstånd intakt, tar du bort noden från klustret, se https \: //docs.Microsoft.com/en-US/Azure/Service-Fabric/Service-Fabric-Cluster-Windows-Server-Add-Remove-Nodes.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --Node-Name [required] | Nodens namn. |
| --timeout-t | Tids gränsen för servern för att utföra åtgärden på några sekunder. Denna timeout anger den tids period som klienten vill vänta tills den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard \: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-node-report-health"></a>sfctl-nodens rapport – hälsa
Skickar en hälso rapport på Service Fabric-noden.

Rapporterar hälso tillståndet för den angivna Service Fabric-noden. Rapporten måste innehålla information om källan till hälso rapporten och egenskapen som den rapporteras om. Rapporten skickas till en Service Fabric Gateway-nod som vidarebefordrar till hälso lagret. Rapporten kan godkännas av gatewayen, men avvisas av hälso lagret efter extra verifiering. Hälso lagret kan till exempel avvisa rapporten på grund av en ogiltig parameter, t. ex. ett inaktuellt ordnings nummer. Om du vill se om rapporten har använts i hälso lagret kontrollerar du att rapporten visas i avsnittet HealthEvents.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --Health-Property [required] | Hälso informationens egenskaper. <br><br> En entitet kan ha hälso rapporter för olika egenskaper. Egenskapen är en sträng och inte en fast uppräkning som tillåter rapportörens flexibilitet att kategorisera det tillstånds villkor som utlöser rapporten. Till exempel kan en rapportör med SourceId "LocalWatchdog" övervaka statusen för den tillgängliga disken på en nod, så att den kan rapportera egenskapen "AvailableDisk" på noden. Samma rapportör kan övervaka nodens anslutning, så att den kan rapportera en egenskap "anslutning" på samma nod. I hälso lagret behandlas dessa rapporter som separata hälso händelser för den angivna noden. Tillsammans med värdet för SourceId kan egenskapen unikt identifiera hälso informationen. |
| --hälso tillstånd [krävs] | Möjliga värden är \: "ogiltig", "OK", "varning", "Error", "okänd". |
| --Node-Name [required] | Nodens namn. |
| --Käll-ID [obligatoriskt] | Det käll namn som identifierar klient/övervaknings-/system komponenten som genererade hälso informationen. |
| --Beskrivning | Beskrivning av hälso informationen. <br><br> Den representerar fritext som används för att lägga till läsbar information om rapporten. Den maximala sträng längden för beskrivningen är 4096 tecken. Om den angivna strängen blir längre trunkeras den automatiskt. Vid trunkering innehåller de sista tecknen i beskrivningen en markör, "[trunkerad]" och den totala sträng storleken är 4096 tecken. Förekomsten av markören anger för användare som har trunkerats. Observera att beskrivningen innehåller färre än 4096 tecken från den ursprungliga strängen när den trunkeras. |
| --omedelbar | En flagga som anger om rapporten ska skickas omedelbart. <br><br> En hälso rapport skickas till ett Service Fabric Gateway-program, som vidarebefordrar till hälso lagret. Om omedelbar är inställt på Sant skickas rapporten omedelbart från HTTP-gatewayen till hälso lagret, oavsett vilka klient inställningar för klient program varan som HTTP-gatewayen använder. Detta är användbart för kritiska rapporter som ska skickas så snart som möjligt. Beroende på tids inställningar och andra villkor kan det hända att det fortfarande inte går att skicka rapporten, till exempel om HTTP-gatewayen är stängd eller om meddelandet inte når gatewayen. Om omedelbar är inställt på false skickas rapporten baserat på hälso klient inställningarna från HTTP-gatewayen. Därför kommer den att grupperas enligt HealthReportSendInterval-konfigurationen. Detta är den rekommenderade inställningen eftersom den gör det möjligt för hälso klienten att optimera hälso rapporterings meddelanden till hälso Arkiv och bearbetning av hälso rapporter. Som standard skickas inte rapporter direkt. |
| --Remove-when-förfallo datum | Värde som anger om rapporten tas bort från hälso arkivet när den upphör att gälla. <br><br> Om värdet är True tas rapporten bort från hälso arkivet när den har gått ut. Om värdet är false behandlas rapporten som ett fel när den upphör att gälla. Värdet för den här egenskapen är falskt som standard. När klienter rapporterar regelbundet ska de ange RemoveWhenExpired false (standard). På så sätt har rapportören problem (t. ex. död läge) och kan inte rapportera. enheten utvärderas vid fel när hälso rapporten upphör att gälla. Den här flaggan anger att entiteten har fel hälso tillstånd. |
| --sekvens-nummer | Serie numret för den här hälso rapporten som en numerisk sträng. <br><br> Rapportens sekvensnummer används av hälso lagret för att identifiera inaktuella rapporter. Om inget värde anges genereras ett sekvensnummer automatiskt av hälso klienten när en rapport läggs till. |
| --timeout-t | Standard \: 60. |
| --TTL | Varaktigheten för vilken den här hälso rapporten är giltig. I det här fältet används ISO8601-format för att ange varaktighet. <br><br> När klienter rapporterar regelbundet bör de skicka rapporter med högre frekvens än tid till Live. Om klienterna rapporterar över över gången kan de ställa in tiden till oändligt. När TTL-tiden förfaller, tas den hälso händelse som innehåller hälso informationen antingen bort från hälso lagret, om RemoveWhenExpired är sant eller om den utvärderas som fel, om RemoveWhenExpired false. Om inget värde anges, är Time to Live standardvärdet oändligt. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-node-restart"></a>sfctl-nod, omstart
Startar om en Service Fabric klusternod.

Startar om en Service Fabric klusternod som redan har startats.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --Node-Name [required] | Nodens namn. |
| --Create-Fabric-dump | Ange sant om du vill skapa en dump för processen för Fabric-noden. Värdet är skiftlägeskänsligt.  Standard \: falskt. |
| --Node-instance-ID | Instans-ID för målnoden. Om instans-ID anges startas noden bara om den matchar den aktuella instansen av noden. Standardvärdet "0" skulle matcha alla instans-ID. Instans-ID: t kan hämtas med hjälp av Get Node-frågan.  Standardvärdet är \: 0. |
| --timeout-t | Tids gränsen för servern för att utföra åtgärden på några sekunder. Denna timeout anger den tids period som klienten vill vänta tills den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard \: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-node-transition"></a>sfctl Node-övergång
Startar eller stoppar en klusternod.

Startar eller stoppar en klusternod.  En klusternod är en process, inte själva OS-instansen.  Starta en nod genom att skicka in "Start" för parametern NodeTransitionType. Om du vill stoppa en nod, måste du skicka "Stop" för parametern NodeTransitionType. Det här API: et startar åtgärden – när API: et returnerar noden kanske inte har slutfört över gången ännu. Anropa GetNodeTransitionProgress med samma OperationId för att hämta åtgärdens förlopp.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --Node-instance-ID [required] | Nodens instans-ID för målnoden. Detta kan fastställas via GetNodeInfo-API. |
| --Node-Name [required] | Nodens namn. |
| --Node-övergångs-Type [required] | Anger vilken typ av över gång som ska utföras.  NodeTransitionType. Start startar en stoppad nod. NodeTransitionType. stop stoppar en nod som är igång. |
| --åtgärds-ID [obligatoriskt] | Ett GUID som identifierar ett anrop till detta API.  Detta skickas till motsvarande GetProgress-API. |
| --stopp-duration-in-Seconds [required] | Tiden, i sekunder, för att behålla noden stoppad.  Minimivärdet är 600, det maximala värdet är 14400.  När den här tiden går ut kommer noden automatiskt att säkerhets kopie ras. |
| --timeout-t | Tids gränsen för servern för att utföra åtgärden på några sekunder. Denna timeout anger den tids period som klienten vill vänta tills den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard \: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-node-transition-status"></a>sfctl Node-status
Hämtar förloppet för en åtgärd som startades med StartNodeTransition.

Hämtar förloppet för en åtgärd som startades med StartNodeTransition med hjälp av den angivna OperationId.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --Node-Name [required] | Nodens namn. |
| --åtgärds-ID [obligatoriskt] | Ett GUID som identifierar ett anrop till detta API.  Detta skickas till motsvarande GetProgress-API. |
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
