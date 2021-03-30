---
title: Azure Service Fabric CLI – sfctl kaos
description: Lär dig mer om sfctl, Azure Service Fabric Command Line Interface. Innehåller en lista med kommandon för att hantera kaos.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: f59eb3296c27e64eb6a4644b2f455e3704381f49
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "86260827"
---
# <a name="sfctl-chaos"></a>sfctl chaos
Starta, stoppa och rapportera om kaos test service.

## <a name="subgroups"></a>Under grupper
|Under grupp|Beskrivning|
| --- | --- |
| [Ange](service-fabric-sfctl-chaos-schedule.md) | Hämta och ange kaos-schemat. |
## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| händelser | Hämtar nästa segment av kaos-händelserna baserat på fortsättnings-token eller tidsintervallet. |
| get | Hämta status för kaos. |
| start | Startar kaos i klustret. |
| stoppa | Stoppar kaos om den körs i klustret och sätter kaos-schemat i stoppat läge. |

## <a name="sfctl-chaos-events"></a>sfctl kaos-händelser
Hämtar nästa segment av kaos-händelserna baserat på fortsättnings-token eller tidsintervallet.

Om du vill hämta nästa segment av kaos-händelserna kan du ange ContinuationToken. För att komma igång med ett nytt segment av kaos-händelser kan du ange tidsintervallet till StartTimeUtc och EndTimeUtc. Du kan inte ange både ContinuationToken och tidsintervallet i samma anrop. Om det finns fler än 100 kaos händelser returneras kaos-händelserna i flera segment där ett segment inte innehåller fler än 100 kaos-händelser och för att hämta nästa segment som du gör ett anrop till det här API: et med en fortsättnings-token.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --fortsättnings-token | Parametern för fortsatt token används för att hämta nästa uppsättning resultat. En fortsättnings-token med ett värde som inte är tom inkluderas i svaret på API: et när resultatet från systemet inte passar i ett enda svar. När det här värdet skickas till nästa API-anrop returnerar API nästa uppsättning resultat. Om det inte finns några ytterligare resultat innehåller inte fortsättnings-token ett värde. Värdet för den här parametern får inte vara URL-kodat. |
| --slut tid-UTC | Windows-filtiden som representerar slut tiden för tidsintervallet som en kaos-rapport ska genereras för. Mer information finns i [DateTime. ToFileTimeUtc-metoden](https\://msdn.microsoft.com/library/system.datetime.tofiletimeutc(v=vs.110).aspx) . |
| --Max-resultat | Det maximala antalet resultat som ska returneras som en del av de växlade frågorna. Den här parametern definierar den övre kanten på antalet returnerade resultat. Resultaten som returneras kan vara mindre än de angivna maximala resultaten om de inte får plats i meddelandet enligt de Max begränsningar för meddelande storlek som definierats i konfigurationen. Om den här parametern är noll eller inte anges, innehåller den växlade frågan så många resultat som möjligt som passar i retur meddelandet. |
| --Start-Time-UTC | Windows-filtiden som representerar start tiden för tidsintervallet som en kaos-rapport ska genereras för. Mer information finns i [DateTime. ToFileTimeUtc-metoden](https\://msdn.microsoft.com/library/system.datetime.tofiletimeutc(v=vs.110).aspx) . |
| --timeout-t | Tids gränsen för servern för att utföra åtgärden på några sekunder. Denna timeout anger den tids period som klienten vill vänta tills den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard \: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-chaos-get"></a>sfctl kaos get
Hämta status för kaos.

Hämta status för kaos som anger om kaos körs, kaos-parametrarna som används för att köra kaos och status för kaos-schemat.

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

## <a name="sfctl-chaos-start"></a>sfctl kaos-start
Startar kaos i klustret.

Om kaos inte redan körs i klustret börjar den kaos med de överförda kaos-parametrarna. Om kaos redan körs när anropet görs, Miss lyckas anropet med felkoden FABRIC_E_CHAOS_ALREADY_RUNNING. Mer information finns i artikeln [inducera kontrollerade kaos i Service Fabric-kluster](https\://docs.microsoft.com/azure/service-fabric/service-fabric-controlled-chaos) .

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --app-Type-Health-Policy-Map | JSON-kodad matris med ordboks poster (nyckel/värde) med högsta procent andel felaktiga program för specifika program typer. Varje ord lista anger som en nyckel för program typ namnet och ett heltal för ett värde som representerar den MaxPercentUnhealthyApplications procent som används för att utvärdera programmen för den angivna program typen. <br><br> Definierar en karta med max procent andel felaktiga program för specifika program typer. Princip mappningen för program typens hälso princip kan användas vid utvärdering av kluster hälsa för att beskriva enskilda program typer. De program typer som ingår i kartan utvärderas mot procent andelen som anges i kartan och inte med den globala MaxPercentUnhealthyApplications som definierats i kluster hälso principen. Programmen av program typer som anges i kartan räknas inte mot den globala programpoolen. Om exempelvis vissa program av en typ är kritiska kan kluster administratören lägga till en post till kartan för den program typen och tilldela den värdet 0% (inte tolerera några fel). Alla andra program kan utvärderas med MaxPercentUnhealthyApplications inställt på 20% för att tolerera vissa problem från tusentals program instanser. Mappningen av hälso princip för program typ används endast om kluster manifestet aktiverar utvärdering av program typens hälso tillstånd med hjälp av konfigurations posten för HealthManager/EnableApplicationTypeHealthEvaluation. <br><br> Exempel på JSON-kodad sträng: [{ \" Key \" : \" Fabric:/röstning \" , \" värde \" : \" 0 \" }] |
| --kaos-Target-filter | JSON-kodad ord lista med två sträng typs nycklar. De två nycklarna är NodeTypeInclusionList och ApplicationInclusionList. Värdena för båda dessa nycklar är sträng lista. chaos_target_filter definierar alla filter för riktade kaos-fel, t. ex. fel som bara är specifika nodtyper eller som endast gör vissa program fel. <br><br> Om chaos_target_filter inte används, orsakar kaos fel alla kluster enheter. Om chaos_target_filter används, fel i kaos bara de entiteter som uppfyller chaos_target_filters specifikationen. NodeTypeInclusionList och ApplicationInclusionList tillåter endast en semantik. Det går inte att ange en skärning av NodeTypeInclusionList och ApplicationInclusionList. Det går till exempel inte att ange "fel det här programmet endast när det finns på den nodtypen". När en entitet ingår i antingen NodeTypeInclusionList eller ApplicationInclusionList kan den entiteten inte uteslutas med hjälp av ChaosTargetFilter. Även om applicationX inte visas i ApplicationInclusionList, kan en del kaos iteration applicationX vara fel eftersom det sker på en nod i nodeTypeY som ingår i NodeTypeInclusionList. Om både NodeTypeInclusionList och ApplicationInclusionList är tomma genereras en ArgumentException. Alla typer av fel (starta om nod, starta om kod paket, ta bort replik, starta om replik, flytta primär och flytta sekundär) är aktiverade för noderna i dessa nodtyper. Om en nodtyp (t. ex. NodeTypeX) inte visas i NodeTypeInclusionList, aktive ras inte noder på radnivå (t. ex. NodeRestart) för noderna i NodeTypeX, men kod paketet och replik felen kan fortfarande aktive ras för NodeTypeX om ett program i ApplicationInclusionList sker på en nod i NodeTypeX. Högst 100 kan inkluderas i den här listan för att öka det här antalet, en konfigurations uppgradering krävs för MaxNumberOfNodeTypesInChaosEntityFilter-konfiguration. Alla repliker som hör till tjänster av dessa program är lämpar till replik fel (starta om replikering, ta bort replik, flytta primär och flytta sekundär) med kaos. Kaos kan bara starta om ett kod paket om kod paketet bara är värd för repliker av dessa program. Om ett program inte visas i listan kan det fortfarande uppstå fel i vissa kaos-iterationer om programmet avslutas på en nod i en nodtyp som ingår i NodeTypeInclusionList. Men om applicationX är knutet till nodeTypeY genom placerings begränsningar och applicationX saknas från ApplicationInclusionList och nodeTypeY inte finns i NodeTypeInclusionList, kommer applicationX aldrig att bli fel. Högst 1000-program namn kan inkluderas i den här listan för att öka det här antalet, så krävs en konfigurations uppgradering för MaxNumberOfApplicationsInChaosEntityFilter-konfigurationen. |
| --kontext | JSON-kodad karta av (sträng, sträng) typ nyckel/värde-par. Kartan kan användas för att registrera information om kaos-körningen. Det får inte finnas fler än 100 sådana par och varje sträng (nyckel eller värde) får innehålla högst 4095 tecken. Den här kartan anges av Start programmet för kaos-körningen för att eventuellt lagra kontexten för den aktuella körningen. |
| --Inaktivera-flytta-replik-fel | Inaktiverar flytt av primära och flytt av sekundära fel. |
| --Max-kluster-stabilisering | Maximal vänte tid i väntan på att alla kluster enheter ska bli stabila och felfria.  Standard \: 60. <br><br> Kaos körs i iterationer och i början av varje iteration verifierar det hälsan hos kluster enheter. Vid verifieringen om en klusterdelad enhet inte är stabil och felfri i MaxClusterStabilizationTimeoutInSeconds genererar kaos en misslyckad validerings händelse. |
| --Max-antal samtidiga fel | Det maximala antalet samtidiga fel som induceras per iteration. Kaos körs i iterationer och två på varandra följande iterationer åtskiljs av en validerings fas. Ju högre samtidighet, desto aggressivt injektion av fel – inducing mer komplexa serie tillstånd för att få fram buggar. Rekommendationen är att börja med ett värde på 2 eller 3 och att du ska vara försiktig när du går vidare.  Standard \: 1. |
| --Max-procent-ej felfri-appar | När du utvärderar kluster hälsa under kaos får du maximalt antal tillåtna program i procent för felaktiga program innan ett fel rapporteras. <br><br> Högsta tillåtna procent andel felaktiga program innan ett fel rapporteras. Om du till exempel vill att 10% av programmen ska vara felaktiga, skulle värdet vara 10. Procent andelen visar den maximala procent andelen program som kan vara felfria innan klustret betraktas som ett fel. Om procent andelen respekteras men det finns minst ett ohälsosamt program, utvärderas hälsan som varning. Detta beräknas genom att antalet felaktiga program divideras över det totala antalet program instanser i klustret, exklusive program av program typer som ingår i ApplicationTypeHealthPolicyMap. Beräkningen avrundar upp till att tolerera ett problem med ett litet antal program. Standardvärdet är noll. |
| --Max-procent-ej felfri-noder | När du utvärderar kluster hälsa under kaos får den högsta tillåtna procent andelen Felaktiga noder innan ett fel rapporteras. <br><br> Högsta tillåtna procent andel felaktiga noder innan ett fel rapporteras. Om du till exempel vill att 10% av noderna ska vara felaktiga, skulle värdet vara 10. Procent andelen visar den maximala procent andelen av noder som kan vara felfria innan klustret betraktas som ett fel. Om procent andelen respekteras men det finns minst en ohälsosam nod, utvärderas hälsan som varning. Procent andelen beräknas genom att antalet felaktiga noder divideras med det totala antalet noder i klustret. Beräkningen avrundar upp till att tolerera ett problem på ett litet antal noder. Standardvärdet är noll. I stora kluster är vissa noder alltid otillgängliga för reparationer, så den här procent andelen bör konfigureras för att tolerera. |
| --tid till körning | Total tid (i sekunder) som kaos ska köras innan den stoppas automatiskt. Det högsta tillåtna värdet är 4 294 967 295 (system. UInt32. MaxValue).  Standard \: 4294967295. |
| --timeout-t | Standard \: 60. |
| --vänte tid-mellan-fel | Vänte tid (i sekunder) mellan efterföljande fel i en enskild iteration.  Standard \: 20. <br><br> Ju större värdet är, desto lägre blir överlappande mellan fel och den enklare följd av tillstånds över gångar som klustret går igenom. Rekommendationen är att börja med ett värde mellan 1 och 5 och att vara försiktig när du går vidare. |
| --vänte tid – mellan iterationer | Tids separation (i sekunder) mellan två på varandra följande iterationer av kaos. Ju större värde, desto lägre frekvens för fel inmatning.  Standard \: 30. |
| --Varning-as-Error | Anger om varningar behandlas med samma allvarlighets grad som fel. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-chaos-stop"></a>sfctl kaos-stopp
Stoppar kaos om den körs i klustret och sätter kaos-schemat i stoppat läge.

Stoppar kaos från att köra nya fel. Flyg felen fortsätter att köras tills de är klara. Det aktuella kaos-schemat försätts i ett stoppat tillstånd. När ett schema har stoppats förblir det i stoppat läge och används inte för att kaos ska kunna schemalägga nya körningar av kaos. Ett nytt kaos-schema måste anges för att du ska kunna återuppta schemaläggningen.

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


## <a name="next-steps"></a>Nästa steg
- [Konfigurera](service-fabric-cli.md) Service Fabric cli.
- Lär dig hur du använder Service Fabric CLI med hjälp av [exempel skripten](./scripts/sfctl-upgrade-application.md).
