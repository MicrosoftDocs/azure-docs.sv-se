---
title: Azure Service Fabric CLI – sfctl-partition
description: Lär dig mer om sfctl, Azure Service Fabric Command Line Interface. Innehåller en lista med kommandon för att hantera partitioner för en tjänst.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: fff569c586548d84ed55018764363ad7f05e526d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "86257095"
---
# <a name="sfctl-partition"></a>sfctl partition
Fråga och hantera partitioner för alla tjänster.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| data förlust | Detta API kommer att orsaka data förlust för den angivna partitionen. |
| data förlust-status | Hämtar förloppet för en data förlust åtgärd för partitionen som startades med StartDataLoss-API: et. |
| attestering | Hämtar hälsan för den angivna Service Fabric partitionen. |
| information | Hämtar information om en Service Fabric partition. |
| lista | Hämtar listan över partitioner i en Service Fabric-tjänst. |
| läsa in | Hämtar inläsnings information för den angivna Service Fabric partitionen. |
| Load-reset | Återställer den aktuella inläsningen av en Service Fabric partition. |
| kvorum-förlust | Inducerar kvorum för en bestämd tillstånds känslig tjänst partition. |
| kvorum-förlust-status | Hämtar förloppet för en kvorum förlust åtgärd på en partition som har startats med StartQuorumLoss-API: et. |
| recover | Anger Service Fabric kluster som det ska försöka återställa en speciell partition som för närvarande har fastnat i kvorum. |
| Återställ – alla | Anger Service Fabric kluster som det ska försöka återställa tjänster (inklusive system tjänster) som för närvarande är låsta i kvorum. |
| rapportera hälsa | Skickar en hälso rapport på Service Fabric partition. |
| restart | Detta API kommer att starta om några eller alla repliker eller instanser av den angivna partitionen. |
| Restart-status | Hämtar förloppet för en PartitionRestart-åtgärd som startades med StartPartitionRestart. |
| SVC-namn | Hämtar namnet på den Service Fabric tjänsten för en partition. |

## <a name="sfctl-partition-data-loss"></a>data förlust för sfctl-partition
Detta API kommer att orsaka data förlust för den angivna partitionen.

Det utlöser ett anrop till OnDataLossAsync-API: et för partitionen.  Detta API kommer att orsaka data förlust för den angivna partitionen. Det utlöser ett anrop till OnDataLoss-API: et för partitionen. Faktisk data förlust beror på den angivna DataLossMode.
- PartialDataLoss: endast ett kvorum med repliker tas bort och OnDataLoss utlöses för partitionen men den faktiska data förlusten beror på förekomsten av flyg-replikering.  
- FullDataLoss: alla repliker tas bort eftersom alla data förloras och OnDataLoss utlöses. Detta API ska endast anropas med en tillstånds känslig tjänst som mål. Anropar det här API: et med en system tjänst eftersom målet inte är ett program.

> [!NOTE]   
> När detta API har anropats kan det inte ångras. Anrop av CancelOperation kommer bara att stoppa körningen och rensa det interna system läget. Data återställs inte om kommandot har förfallit tillräckligt mycket för att leda till data förlust. Anropa GetDataLossProgress-API: et med samma OperationId för att returnera information om åtgärden som startades med detta API.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --data förlust läge [obligatoriskt] | Den här uppräkningen skickas till StartDataLoss-API: et för att ange vilken typ av data förlust som ska induceras. |
| --åtgärds-ID [obligatoriskt] | Ett GUID som identifierar ett anrop till detta API.  Detta skickas till motsvarande GetProgress-API. |
| --partitions-ID [required] | Partitionens identitet. |
| --tjänst-ID [obligatoriskt] | Tjänstens identitet. Detta ID är vanligt vis det fullständiga namnet på tjänsten utan URI-schemat "Fabric \: ". Från och med version 6,0 avgränsas hierarkiska namn med " \~ "-symbolen. Om tjänst namnet till exempel är "Fabric \: /MyApp/APP1/svc1" skulle tjänst identiteten vara "MyApp \~ APP1 \~ svc1" i 6.0 + och "MyApp/APP1/svc1" i tidigare versioner. |
| --timeout-t | Tids gränsen för servern för att utföra åtgärden på några sekunder. Denna timeout anger den tids period som klienten vill vänta tills den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard \: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-partition-data-loss-status"></a>sfctl partition data-förlust-status
Hämtar förloppet för en data förlust åtgärd för partitionen som startades med StartDataLoss-API: et.

Hämtar förloppet för en data förlust åtgärd som har startats med StartDataLoss med hjälp av OperationId.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --åtgärds-ID [obligatoriskt] | Ett GUID som identifierar ett anrop till detta API.  Detta skickas till motsvarande GetProgress-API. |
| --partitions-ID [required] | Partitionens identitet. |
| --tjänst-ID [obligatoriskt] | Tjänstens identitet. Detta ID är vanligt vis det fullständiga namnet på tjänsten utan URI-schemat "Fabric \: ". Från och med version 6,0 avgränsas hierarkiska namn med " \~ "-symbolen. Om tjänst namnet till exempel är "Fabric \: /MyApp/APP1/svc1" skulle tjänst identiteten vara "MyApp \~ APP1 \~ svc1" i 6.0 + och "MyApp/APP1/svc1" i tidigare versioner. |
| --timeout-t | Tids gränsen för servern för att utföra åtgärden på några sekunder. Denna timeout anger den tids period som klienten vill vänta tills den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard \: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-partition-health"></a>hälso tillstånd för sfctl-partition
Hämtar hälsan för den angivna Service Fabric partitionen.

Använd EventsHealthStateFilter för att filtrera insamling av hälso händelser som rapporter ATS för tjänsten baserat på hälso tillståndet. Använd ReplicasHealthStateFilter för att filtrera samlingen av ReplicaHealthState-objekt på partitionen. Om du anger en partition som inte finns i hälso lagret returnerar den här begäran ett fel.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --partitions-ID [required] | Partitionens identitet. |
| --händelser-hälso tillstånd – filter | Tillåter filtrering av samlingen av HealthEvent-objekt som returneras baserat på hälso tillstånd. De möjliga värdena för den här parametern är heltals värde för något av följande hälso tillstånd. Endast händelser som matchar filtret returneras. Alla händelser används för att utvärdera det sammanlagda hälso tillståndet. Om inget anges returneras alla poster. Tillstånds värden är flaggning-baserad uppräkning, så värdet kan vara en kombination av dessa värden, erhållna med hjälp av den bitvisa operatorn eller. Om det angivna värdet till exempel är 6 returneras alla händelser med hälso tillstånd svärdet OK (2) och varning (4).  <br> -Standard-standardvärdet. Matchar alla hälso tillstånd. Värdet är noll.  <br> -Inget – filter som inte matchar något värde för hälso tillstånd. Används för att returnera inga resultat för en specifik samling av tillstånd. Värdet är 1.  <br> – OK-filter som matchar inmatade hälso tillstånds värden OK. Värdet är 2.  <br> -Varnings filter som matchar inmatade värde varningar för hälso tillstånd. Värdet är 4.  <br> -Fel-filter som matchar InInformationen med hälso tillstånds värde fel. Värdet är 8.  <br> – Alla – filter som matchar indatamängden med ett värde för hälso tillstånd. Värdet är 65535. |
| --Exkludera-hälso statistik | Anger om hälso statistik ska returneras som en del av frågeresultatet. Falskt som standard. Statistiken visar antalet underordnade entiteter i hälso tillståndet OK, varning och fel. |
| --repliker-hälso tillstånd – filter | Tillåter filtrering av samlingen av ReplicaHealthState-objekt på partitionen. Värdet kan hämtas från medlemmar eller bitvisa åtgärder på medlemmar i HealthStateFilter. Endast repliker som matchar filtret kommer att returneras. Alla repliker kommer att användas för att utvärdera det sammanlagda hälso tillståndet. Om inget anges returneras alla poster. Tillstånds värden är flaggning-baserad uppräkning, så värdet kan vara en kombination av de här värdena som erhålls med hjälp av bitvis or-operator. Om det angivna värdet till exempel är 6 returneras alla händelser med hälso tillstånd svärdet OK (2) och varning (4). De möjliga värdena för den här parametern är heltals värde för något av följande hälso tillstånd.  <br> -Standard-standardvärdet. Matchar alla hälso tillstånd. Värdet är noll.  <br> -Inget – filter som inte matchar något värde för hälso tillstånd. Används för att returnera inga resultat för en specifik samling av tillstånd. Värdet är 1.  <br> – OK-filter som matchar inmatade hälso tillstånds värden OK. Värdet är 2.  <br> -Varnings filter som matchar inmatade värde varningar för hälso tillstånd. Värdet är 4.  <br> -Fel-filter som matchar InInformationen med hälso tillstånds värde fel. Värdet är 8.  <br> – Alla – filter som matchar indatamängden med ett värde för hälso tillstånd. Värdet är 65535. |
| --timeout-t | Tids gränsen för servern för att utföra åtgärden på några sekunder. Denna timeout anger den tids period som klienten vill vänta tills den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard \: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-partition-info"></a>information om sfctl-partition
Hämtar information om en Service Fabric partition.

Hämtar information om den angivna partitionen. Svaret innehåller partitions-ID, partitionerings schema information, nycklar som stöds av partitionen, status, hälso tillstånd och annan information om partitionen.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --partitions-ID [required] | Partitionens identitet. |
| --timeout-t | Tids gränsen för servern för att utföra åtgärden på några sekunder. Denna timeout anger den tids period som klienten vill vänta tills den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard \: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-partition-list"></a>lista över sfctl-partitioner
Hämtar listan över partitioner i en Service Fabric-tjänst.

Svaret innehåller partitions-ID, partitionerings schema information, nycklar som stöds av partitionen, status, hälso tillstånd och annan information om partitionen.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --tjänst-ID [obligatoriskt] | Tjänstens identitet. Detta ID är vanligt vis det fullständiga namnet på tjänsten utan URI-schemat "Fabric \: ". Från och med version 6,0 avgränsas hierarkiska namn med " \~ "-symbolen. Om tjänst namnet till exempel är "Fabric \: /MyApp/APP1/svc1" skulle tjänst identiteten vara "MyApp \~ APP1 \~ svc1" i 6.0 + och "MyApp/APP1/svc1" i tidigare versioner. |
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

## <a name="sfctl-partition-load"></a>sfctl partition load
Hämtar inläsnings information för den angivna Service Fabric partitionen.

Returnerar information om belastningen på en angiven partition. Svaret innehåller en lista över inläsnings rapporter för en Service Fabric partition. Varje rapport innehåller inläsnings måttets namn, värde och senaste rapporterade tid i UTC.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --partitions-ID [required] | Partitionens identitet. |
| --timeout-t | Tids gränsen för servern för att utföra åtgärden på några sekunder. Denna timeout anger den tids period som klienten vill vänta tills den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard \: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-partition-load-reset"></a>sfctl partition load-reset
Återställer den aktuella inläsningen av en Service Fabric partition.

Återställer den aktuella inläsningen av en Service Fabric partition till standard belastningen för tjänsten.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --partitions-ID [required] | Partitionens identitet. |
| --timeout-t | Tids gränsen för servern för att utföra åtgärden på några sekunder. Denna timeout anger den tids period som klienten vill vänta tills den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard \: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-partition-quorum-loss"></a>sfctl partition, kvorum-förlust
Inducerar kvorum för en bestämd tillstånds känslig tjänst partition.

Det här API: et är användbart för en tillfälligt kvorum förlust på din tjänst. Anropa GetQuorumLossProgress-API: et med samma OperationId för att returnera information om åtgärden som startades med detta API. Detta kan endast anropas för tillstånds känsliga beständiga (HasPersistedState = = true)-tjänster.  Använd inte detta API för tillstånds lösa tjänster eller tillstånds känsliga tjänster som endast är i minnet.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --åtgärds-ID [obligatoriskt] | Ett GUID som identifierar ett anrop till detta API.  Detta skickas till motsvarande GetProgress-API. |
| --partitions-ID [required] | Partitionens identitet. |
| --kvorum-förlust-varaktighet [obligatoriskt] | Den tid som partitionen ska behållas i förlorat kvorum.  Detta måste anges i sekunder. |
| --kvorum-förstörande läge [obligatoriskt] | Den här uppräkningen skickas till StartQuorumLoss-API: et för att ange vilken typ av kvorum förlust som ska induceras. |
| --tjänst-ID [obligatoriskt] | Tjänstens identitet. Detta ID är vanligt vis det fullständiga namnet på tjänsten utan URI-schemat "Fabric \: ". Från och med version 6,0 avgränsas hierarkiska namn med " \~ "-symbolen. Om tjänst namnet till exempel är "Fabric \: /MyApp/APP1/svc1" skulle tjänst identiteten vara "MyApp \~ APP1 \~ svc1" i 6.0 + och "MyApp/APP1/svc1" i tidigare versioner. |
| --timeout-t | Tids gränsen för servern för att utföra åtgärden på några sekunder. Denna timeout anger den tids period som klienten vill vänta tills den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard \: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-partition-quorum-loss-status"></a>sfctl partitionens kvorum-status
Hämtar förloppet för en kvorum förlust åtgärd på en partition som har startats med StartQuorumLoss-API: et.

Hämtar förloppet för en återställning av kvorum som har startats med StartQuorumLoss med hjälp av den angivna OperationId.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --åtgärds-ID [obligatoriskt] | Ett GUID som identifierar ett anrop till detta API.  Detta skickas till motsvarande GetProgress-API. |
| --partitions-ID [required] | Partitionens identitet. |
| --tjänst-ID [obligatoriskt] | Tjänstens identitet. Detta ID är vanligt vis det fullständiga namnet på tjänsten utan URI-schemat "Fabric \: ". Från och med version 6,0 avgränsas hierarkiska namn med " \~ "-symbolen. Om tjänst namnet till exempel är "Fabric \: /MyApp/APP1/svc1" skulle tjänst identiteten vara "MyApp \~ APP1 \~ svc1" i 6.0 + och "MyApp/APP1/svc1" i tidigare versioner. |
| --timeout-t | Tids gränsen för servern för att utföra åtgärden på några sekunder. Denna timeout anger den tids period som klienten vill vänta tills den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard \: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-partition-recover"></a>återställning av sfctl-partition
Anger Service Fabric kluster som det ska försöka återställa en speciell partition som för närvarande har fastnat i kvorum.

Den här åtgärden bör endast utföras om det är känt att de repliker som är nere inte kan återställas. Felaktig användning av detta API kan orsaka potentiell data förlust.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --partitions-ID [required] | Partitionens identitet. |
| --timeout-t | Tids gränsen för servern för att utföra åtgärden på några sekunder. Denna timeout anger den tids period som klienten vill vänta tills den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard \: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-partition-recover-all"></a>sfctl-partition återställning-alla
Anger Service Fabric kluster som det ska försöka återställa tjänster (inklusive system tjänster) som för närvarande är låsta i kvorum.

Den här åtgärden bör endast utföras om det är känt att de repliker som är nere inte kan återställas. Felaktig användning av detta API kan orsaka potentiell data förlust.

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

## <a name="sfctl-partition-report-health"></a>sfctl-partitions rapport – hälsa
Skickar en hälso rapport på Service Fabric partition.

Rapporterar hälso tillståndet för den angivna Service Fabric partitionen. Rapporten måste innehålla information om källan till hälso rapporten och egenskapen som den rapporteras om. Rapporten skickas till en Service Fabric Gateway-partition, som vidarebefordras till hälso lagret. Rapporten kan godkännas av gatewayen, men avvisas av hälso lagret efter extra verifiering. Hälso lagret kan till exempel avvisa rapporten på grund av en ogiltig parameter, t. ex. ett inaktuellt ordnings nummer. Om du vill se om rapporten har använts i hälso lagret kontrollerar du att rapporten visas i avsnittet händelser.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --Health-Property [required] | Hälso informationens egenskaper. <br><br> En entitet kan ha hälso rapporter för olika egenskaper. Egenskapen är en sträng och inte en fast uppräkning som tillåter rapportörens flexibilitet att kategorisera det tillstånds villkor som utlöser rapporten. Till exempel kan en rapportör med SourceId "LocalWatchdog" övervaka statusen för den tillgängliga disken på en nod, så att den kan rapportera egenskapen "AvailableDisk" på noden. Samma rapportör kan övervaka nodens anslutning, så att den kan rapportera en egenskap "anslutning" på samma nod. I hälso lagret behandlas dessa rapporter som separata hälso händelser för den angivna noden. Tillsammans med värdet för SourceId kan egenskapen unikt identifiera hälso informationen. |
| --hälso tillstånd [krävs] | Möjliga värden är \: "ogiltig", "OK", "varning", "Error", "okänd". |
| --partitions-ID [required] | Partitionens identitet. |
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

## <a name="sfctl-partition-restart"></a>sfctl-partition, omstart
Detta API kommer att starta om några eller alla repliker eller instanser av den angivna partitionen.

Detta API är användbart för att testa redundans. Om den används som mål för en tillstånds lös tjänstmall måste RestartPartitionMode vara AllReplicasOrInstances. Anropa GetPartitionRestartProgress-API: et med samma OperationId för att få förloppet.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --åtgärds-ID [obligatoriskt] | Ett GUID som identifierar ett anrop till detta API.  Detta skickas till motsvarande GetProgress-API. |
| --partitions-ID [required] | Partitionens identitet. |
| --Restart-partition-mode [required] | Beskriv vilka partitioner som ska startas om. |
| --tjänst-ID [obligatoriskt] | Tjänstens identitet. Detta ID är vanligt vis det fullständiga namnet på tjänsten utan URI-schemat "Fabric \: ". Från och med version 6,0 avgränsas hierarkiska namn med " \~ "-symbolen. Om tjänst namnet till exempel är "Fabric \: /MyApp/APP1/svc1" skulle tjänst identiteten vara "MyApp \~ APP1 \~ svc1" i 6.0 + och "MyApp/APP1/svc1" i tidigare versioner. |
| --timeout-t | Tids gränsen för servern för att utföra åtgärden på några sekunder. Denna timeout anger den tids period som klienten vill vänta tills den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard \: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-partition-restart-status"></a>sfctl-partition, omstart-status
Hämtar förloppet för en PartitionRestart-åtgärd som startades med StartPartitionRestart.

Hämtar förloppet för en PartitionRestart som startas med StartPartitionRestart med hjälp av den angivna OperationId.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --åtgärds-ID [obligatoriskt] | Ett GUID som identifierar ett anrop till detta API.  Detta skickas till motsvarande GetProgress-API. |
| --partitions-ID [required] | Partitionens identitet. |
| --tjänst-ID [obligatoriskt] | Tjänstens identitet. Detta ID är vanligt vis det fullständiga namnet på tjänsten utan URI-schemat "Fabric \: ". Från och med version 6,0 avgränsas hierarkiska namn med " \~ "-symbolen. Om tjänst namnet till exempel är "Fabric \: /MyApp/APP1/svc1" skulle tjänst identiteten vara "MyApp \~ APP1 \~ svc1" i 6.0 + och "MyApp/APP1/svc1" i tidigare versioner. |
| --timeout-t | Tids gränsen för servern för att utföra åtgärden på några sekunder. Denna timeout anger den tids period som klienten vill vänta tills den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard \: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-partition-svc-name"></a>sfctl partition SVC – namn
Hämtar namnet på den Service Fabric tjänsten för en partition.

Hämtar namnet på tjänsten för den angivna partitionen. Ett 404-fel returneras om partitions-ID: t inte finns i klustret.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --partitions-ID [required] | Partitionens identitet. |
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
