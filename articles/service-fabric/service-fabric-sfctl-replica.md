---
title: Azure Service Fabric CLI – sfctl-replikering
description: Lär dig mer om sfctl, Azure Service Fabric Command Line Interface. Innehåller en lista med kommandon för att hantera repliker.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 3ed1ecca686471e052f07e945738ad1a49e68464
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "86260310"
---
# <a name="sfctl-replica"></a>sfctl replica
Hantera de repliker som hör till-tjänstepartitioner.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| distribueras | Hämtar information om replik som distribuerats på en Service Fabric-nod. |
| distribuerad lista | Hämtar listan över repliker som har distribuerats på en Service Fabric-nod. |
| attestering | Hämtar hälsan för en Service Fabric tillstånds känslig tjänst-replikering eller tillstånds lös tjänst instans. |
| information | Hämtar information om en replik av en Service Fabric partition. |
| lista | Hämtar information om repliker av en Service Fabric-tjänstepartition. |
| ta bort | Tar bort en tjänst replik som körs på en nod. |
| rapportera hälsa | Skickar en hälso rapport på Service Fabric repliken. |
| restart | Startar om en tjänst replik för en beständiga tjänst som körs på en nod. |

## <a name="sfctl-replica-deployed"></a>sfctl-replik distribuerad
Hämtar information om replik som distribuerats på en Service Fabric-nod.

Hämtar information om repliken som distribuerats på en Service Fabric-nod. Informationen omfattar tjänst typ, tjänst namn, aktuell tjänst åtgärd, aktuell start datum för tjänst åtgärd, partitions-ID, replik/instans-ID, rapporterad belastning och annan information.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --Node-Name [required] | Nodens namn. |
| --partitions-ID [required] | Partitionens identitet. |
| --replik-ID [obligatoriskt] | Replikens identifierare. |
| --timeout-t | Tids gränsen för servern för att utföra åtgärden på några sekunder. Denna timeout anger den tids period som klienten vill vänta tills den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard \: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-replica-deployed-list"></a>sfctl-replik distribuerad – lista
Hämtar listan över repliker som har distribuerats på en Service Fabric-nod.

Hämtar listan med information om repliker som distribuerats på en Service Fabric-nod. Informationen inkluderar partitions-ID, replik-ID, status för repliken, namnet på tjänsten, namnet på tjänst typen och annan information. Använd parametrarna PartitionId eller ServiceManifestName för att returnera information om de distribuerade replikerna som matchar de angivna värdena för dessa parametrar.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --program-ID [obligatoriskt] | Programmets identitet. Detta är vanligt vis det fullständiga namnet på programmet utan URI-schemat "Fabric \: ". Från och med version 6,0 avgränsas hierarkiska namn med " \~ "-symbolen. Om program namnet till exempel är "Fabric \: /MyApp/APP1" skulle program identiteten vara "MyApp \~ APP1" i 6.0 + och "MyApp/APP1" i tidigare versioner. |
| --Node-Name [required] | Nodens namn. |
| --partitions-ID | Partitionens identitet. |
| --tjänst-manifest-namn | Namnet på ett tjänst manifest som är registrerat som en del av en program typ i ett Service Fabric kluster. |
| --timeout-t | Tids gränsen för servern för att utföra åtgärden på några sekunder. Denna timeout anger den tids period som klienten vill vänta tills den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard \: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-replica-health"></a>hälso tillstånd för sfctl-replik
Hämtar hälsan för en Service Fabric tillstånds känslig tjänst-replikering eller tillstånds lös tjänst instans.

Hämtar hälsan för en Service Fabric replik. Använd EventsHealthStateFilter för att filtrera den samling hälso händelser som rapporteras på repliken baserat på hälso tillståndet.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --partitions-ID [required] | Partitionens identitet. |
| --replik-ID [obligatoriskt] | Replikens identifierare. |
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

## <a name="sfctl-replica-info"></a>information om sfctl-replikering
Hämtar information om en replik av en Service Fabric partition.

Svaret innehåller ID, roll, status, hälsa, nodnamn, drift tid och annan information om repliken.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --partitions-ID [required] | Partitionens identitet. |
| --replik-ID [obligatoriskt] | Replikens identifierare. |
| --timeout-t | Tids gränsen för servern för att utföra åtgärden på några sekunder. Denna timeout anger den tids period som klienten vill vänta tills den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard \: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-replica-list"></a>lista över sfctl-replikering
Hämtar information om repliker av en Service Fabric-tjänstepartition.

GetReplicas-slutpunkten returnerar information om replikerna för den angivna partitionen. Svaret innehåller ID, roll, status, hälsa, nodnamn, drift tid och annan information om repliken.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --partitions-ID [required] | Partitionens identitet. |
| --fortsättnings-token | Parametern för fortsatt token används för att hämta nästa uppsättning resultat. En fortsättnings-token med ett värde som inte är tom inkluderas i svaret på API: et när resultatet från systemet inte passar i ett enda svar. När det här värdet skickas till nästa API-anrop returnerar API nästa uppsättning resultat. Om det inte finns några ytterligare resultat innehåller inte fortsättnings-token ett värde. Värdet för den här parametern får inte vara URL-kodat. |
| --timeout-t | Tids gränsen för servern för att utföra åtgärden på några sekunder. Denna timeout anger den tids period som klienten vill vänta tills den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard \: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-replica-remove"></a>ta bort sfctl-replik
Tar bort en tjänst replik som körs på en nod.

Detta API simulerar ett Service Fabric replik fel genom att ta bort en replik från ett Service Fabric-kluster. Borttagningen stänger repliken, överflyttar repliken till rollen ingen och tar sedan bort all statusinformation för repliken från klustret. Med det här API: et testas sökväg för replik tillstånds borttagning och den permanenta sökvägen för rapport felet simuleras via klient-API: er Varning-det finns inga säkerhets kontroller som utförs när denna API används. Felaktig användning av detta API kan leda till data förlust för tillstånds känsliga tjänster. Dessutom påverkar forceRemove-flaggan alla andra repliker som finns i samma process.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --Node-Name [required] | Nodens namn. |
| --partitions-ID [required] | Partitionens identitet. |
| --replik-ID [obligatoriskt] | Replikens identifierare. |
| --Framtvinga-ta bort | Ta bort ett Service Fabric program eller en tjänst framtvingar utan att gå igenom den korrekt avstängnings ordningen. Den här parametern kan användas för att framtvinga borttagning av ett program eller en tjänst för vilken borttagning har nått tids gränsen på grund av problem i Service koden som förhindrar en korrekt stängning av repliker. |
| --timeout-t | Tids gränsen för servern för att utföra åtgärden på några sekunder. Denna timeout anger den tids period som klienten vill vänta tills den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard \: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-replica-report-health"></a>sfctl Replica-rapport – hälsa
Skickar en hälso rapport på Service Fabric repliken.

Rapporterar hälso tillståndet för den angivna Service Fabric repliken. Rapporten måste innehålla information om källan till hälso rapporten och egenskapen som den rapporteras om. Rapporten skickas till en Service Fabric Gateway-replik som vidarebefordrar till hälso lagret. Rapporten kan godkännas av gatewayen, men avvisas av hälso lagret efter extra verifiering. Hälso lagret kan till exempel avvisa rapporten på grund av en ogiltig parameter, t. ex. ett inaktuellt ordnings nummer. Om du vill se om rapporten användes i hälso lagret kör du hämta replik hälsa och kontrollerar att rapporten visas i avsnittet HealthEvents.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --Health-Property [required] | Hälso informationens egenskaper. <br><br> En entitet kan ha hälso rapporter för olika egenskaper. Egenskapen är en sträng och inte en fast uppräkning som tillåter rapportörens flexibilitet att kategorisera det tillstånds villkor som utlöser rapporten. Till exempel kan en rapportör med SourceId "LocalWatchdog" övervaka statusen för den tillgängliga disken på en nod, så att den kan rapportera egenskapen "AvailableDisk" på noden. Samma rapportör kan övervaka nodens anslutning, så att den kan rapportera en egenskap "anslutning" på samma nod. I hälso lagret behandlas dessa rapporter som separata hälso händelser för den angivna noden. Tillsammans med värdet för SourceId kan egenskapen unikt identifiera hälso informationen. |
| --hälso tillstånd [krävs] | Möjliga värden är \: "ogiltig", "OK", "varning", "Error", "okänd". |
| --partitions-ID [required] | Partitionens identitet. |
| --replik-ID [obligatoriskt] | Partitionens identitet. |
| --Käll-ID [obligatoriskt] | Det käll namn som identifierar klient/övervaknings-/system komponenten som genererade hälso informationen. |
| --Beskrivning | Beskrivning av hälso informationen. <br><br> Den representerar fritext som används för att lägga till läsbar information om rapporten. Den maximala sträng längden för beskrivningen är 4096 tecken. Om den angivna strängen blir längre trunkeras den automatiskt. Vid trunkering innehåller de sista tecknen i beskrivningen en markör, "[trunkerad]" och den totala sträng storleken är 4096 tecken. Förekomsten av markören anger för användare som har trunkerats. Observera att beskrivningen innehåller färre än 4096 tecken från den ursprungliga strängen när den trunkeras. |
| --omedelbar | En flagga som anger om rapporten ska skickas omedelbart. <br><br> En hälso rapport skickas till ett Service Fabric Gateway-program, som vidarebefordrar till hälso lagret. Om omedelbar är inställt på Sant skickas rapporten omedelbart från HTTP-gatewayen till hälso lagret, oavsett vilka klient inställningar för klient program varan som HTTP-gatewayen använder. Detta är användbart för kritiska rapporter som ska skickas så snart som möjligt. Beroende på tids inställningar och andra villkor kan det hända att det fortfarande inte går att skicka rapporten, till exempel om HTTP-gatewayen är stängd eller om meddelandet inte når gatewayen. Om omedelbar är inställt på false skickas rapporten baserat på hälso klient inställningarna från HTTP-gatewayen. Därför kommer den att grupperas enligt HealthReportSendInterval-konfigurationen. Detta är den rekommenderade inställningen eftersom den gör det möjligt för hälso klienten att optimera hälso rapporterings meddelanden till hälso Arkiv och bearbetning av hälso rapporter. Som standard skickas inte rapporter direkt. |
| --Remove-when-förfallo datum | Värde som anger om rapporten tas bort från hälso arkivet när den upphör att gälla. <br><br> Om värdet är True tas rapporten bort från hälso arkivet när den har gått ut. Om värdet är false behandlas rapporten som ett fel när den upphör att gälla. Värdet för den här egenskapen är falskt som standard. När klienter rapporterar regelbundet ska de ange RemoveWhenExpired false (standard). På så sätt har rapportören problem (t. ex. död läge) och kan inte rapportera. enheten utvärderas vid fel när hälso rapporten upphör att gälla. Den här flaggan anger att entiteten har fel hälso tillstånd. |
| --sekvens-nummer | Serie numret för den här hälso rapporten som en numerisk sträng. <br><br> Rapportens sekvensnummer används av hälso lagret för att identifiera inaktuella rapporter. Om inget värde anges genereras ett sekvensnummer automatiskt av hälso klienten när en rapport läggs till. |
| --tjänst-typ | Vilken typ av tjänst replik (tillstånds lös eller tillstånds känslig) som hälso tillståndet rapporteras för. Följande är möjliga värden \: ' stateable ', ' stateable '.  Standard \: tillstånds känslig. |
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

## <a name="sfctl-replica-restart"></a>sfctl-replikering, omstart
Startar om en tjänst replik för en beständiga tjänst som körs på en nod.

Startar om en tjänst replik för en beständiga tjänst som körs på en nod. Varning-det finns inga säkerhets kontroller som utförs när denna API används. Felaktig användning av detta API kan leda till tillgänglighets förlust för tillstånds känsliga tjänster.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --Node-Name [required] | Nodens namn. |
| --partitions-ID [required] | Partitionens identitet. |
| --replik-ID [obligatoriskt] | Replikens identifierare. |
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
