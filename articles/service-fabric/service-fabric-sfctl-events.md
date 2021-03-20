---
title: Azure Service Fabric CLI – sfctl-händelser
description: Beskriver sfctl-kommandon Service Fabric för CLI-.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 635bddef1a98d6ed2b112662d8c9c44fe65e9e32
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "76906104"
---
# <a name="sfctl-events"></a>sfctl-händelser
Hämta händelser från händelse lagret (om EventStore-tjänsten redan är installerad).

System tjänsten EventStore kan läggas till via en konfigurations uppgradering till valfritt SFRP-kluster som kör >= 6,4. Kontrol lera följande URL \: -https \: //docs.Microsoft.com/Azure/Service-Fabric/Service-Fabric-Diagnostics-eventstore.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| lista över alla program | Hämtar alla program-relaterade händelser. |
| lista över alla noder | Hämtar alla noder-relaterade händelser. |
| lista över alla partitioner | Hämtar alla partitioner relaterade händelser. |
| alla-tjänster-lista | Hämtar alla tjänster-relaterade händelser. |
| program lista | Hämtar program relaterade händelser. |
| kluster lista | Hämtar alla kluster relaterade händelser. |
| Node-List | Hämtar ett Node-relaterade händelser. |
| partition – alla-repliker – lista | Hämtar alla repliker-relaterade händelser för en partition. |
| partition – lista | Hämtar en partition-relaterade händelser. |
| partition-Replica-List | Hämtar en partitions replik-relaterade händelser. |
| tjänst lista | Hämtar ett service-relaterade händelser. |

## <a name="sfctl-events-all-applications-list"></a>sfctl-lista över alla program – lista
Hämtar alla program-relaterade händelser.

Svaret är en lista över ApplicationEvent-objekt.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --slut tid UTC [required] | Slut tiden för en uppslags fråga i ISO UTC ÅÅÅÅ-MM-ddTHH \: mm- \: SSZ. |
| --Start-Time-UTC [required] | Start tiden för en uppslags fråga i ISO UTC ÅÅÅÅ-MM-ddTHH \: mm- \: SSZ. |
| --händelse-types-filter | Det här är en kommaavgränsad sträng som anger vilka typer av FabricEvents som endast ska tas med i svaret. |
| --Exkludera-analys-händelser | Den här paramen inaktiverar hämtning av AnalysisEvents om True har skickats. |
| --hoppa över korrelations uppslag | Den här paramen inaktiverar sökningen av CorrelatedEvents-information om True har skickats. annars fylls fältet CorrelationEvents bearbetas och HasCorrelatedEvents i varje FabricEvent. |
| --timeout-t | Tids gränsen för servern för att utföra åtgärden på några sekunder. Denna timeout anger den tids period som klienten vill vänta tills den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard \: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-events-all-nodes-list"></a>sfctl-händelser alla-noder-lista
Hämtar alla noder-relaterade händelser.

Svaret är en lista över NodeEvent-objekt.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --slut tid UTC [required] | Slut tiden för en uppslags fråga i ISO UTC ÅÅÅÅ-MM-ddTHH \: mm- \: SSZ. |
| --Start-Time-UTC [required] | Start tiden för en uppslags fråga i ISO UTC ÅÅÅÅ-MM-ddTHH \: mm- \: SSZ. |
| --händelse-types-filter | Det här är en kommaavgränsad sträng som anger vilka typer av FabricEvents som endast ska tas med i svaret. |
| --Exkludera-analys-händelser | Den här paramen inaktiverar hämtning av AnalysisEvents om True har skickats. |
| --hoppa över korrelations uppslag | Den här paramen inaktiverar sökningen av CorrelatedEvents-information om True har skickats. annars fylls fältet CorrelationEvents bearbetas och HasCorrelatedEvents i varje FabricEvent. |
| --timeout-t | Tids gränsen för servern för att utföra åtgärden på några sekunder. Denna timeout anger den tids period som klienten vill vänta tills den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard \: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-events-all-partitions-list"></a>sfctl händelser alla-partitioner-lista
Hämtar alla partitioner relaterade händelser.

Svaret är en lista över PartitionEvent-objekt.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --slut tid UTC [required] | Slut tiden för en uppslags fråga i ISO UTC ÅÅÅÅ-MM-ddTHH \: mm- \: SSZ. |
| --Start-Time-UTC [required] | Start tiden för en uppslags fråga i ISO UTC ÅÅÅÅ-MM-ddTHH \: mm- \: SSZ. |
| --händelse-types-filter | Det här är en kommaavgränsad sträng som anger vilka typer av FabricEvents som endast ska tas med i svaret. |
| --Exkludera-analys-händelser | Den här paramen inaktiverar hämtning av AnalysisEvents om True har skickats. |
| --hoppa över korrelations uppslag | Den här paramen inaktiverar sökningen av CorrelatedEvents-information om True har skickats. annars fylls fältet CorrelationEvents bearbetas och HasCorrelatedEvents i varje FabricEvent. |
| --timeout-t | Tids gränsen för servern för att utföra åtgärden på några sekunder. Denna timeout anger den tids period som klienten vill vänta tills den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard \: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-events-all-services-list"></a>sfctl-händelser alla-tjänster-lista
Hämtar alla tjänster-relaterade händelser.

Svaret är en lista över ServiceEvent-objekt.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --slut tid UTC [required] | Slut tiden för en uppslags fråga i ISO UTC ÅÅÅÅ-MM-ddTHH \: mm- \: SSZ. |
| --Start-Time-UTC [required] | Start tiden för en uppslags fråga i ISO UTC ÅÅÅÅ-MM-ddTHH \: mm- \: SSZ. |
| --händelse-types-filter | Det här är en kommaavgränsad sträng som anger vilka typer av FabricEvents som endast ska tas med i svaret. |
| --Exkludera-analys-händelser | Den här paramen inaktiverar hämtning av AnalysisEvents om True har skickats. |
| --hoppa över korrelations uppslag | Den här paramen inaktiverar sökningen av CorrelatedEvents-information om True har skickats. annars fylls fältet CorrelationEvents bearbetas och HasCorrelatedEvents i varje FabricEvent. |
| --timeout-t | Tids gränsen för servern för att utföra åtgärden på några sekunder. Denna timeout anger den tids period som klienten vill vänta tills den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard \: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-events-application-list"></a>sfctl Events-program-List
Hämtar program relaterade händelser.

Svaret är en lista över ApplicationEvent-objekt.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --program-ID [obligatoriskt] | Programmets identitet. Detta är vanligt vis det fullständiga namnet på programmet utan URI-schemat "Fabric \: ". Från och med version 6,0 avgränsas hierarkiska namn med " \~ "-symbolen. Om program namnet till exempel är "Fabric \: /MyApp/APP1" skulle program identiteten vara "MyApp \~ APP1" i 6.0 + och "MyApp/APP1" i tidigare versioner. |
| --slut tid UTC [required] | Slut tiden för en uppslags fråga i ISO UTC ÅÅÅÅ-MM-ddTHH \: mm- \: SSZ. |
| --Start-Time-UTC [required] | Start tiden för en uppslags fråga i ISO UTC ÅÅÅÅ-MM-ddTHH \: mm- \: SSZ. |
| --händelse-types-filter | Det här är en kommaavgränsad sträng som anger vilka typer av FabricEvents som endast ska tas med i svaret. |
| --Exkludera-analys-händelser | Den här paramen inaktiverar hämtning av AnalysisEvents om True har skickats. |
| --hoppa över korrelations uppslag | Den här paramen inaktiverar sökningen av CorrelatedEvents-information om True har skickats. annars fylls fältet CorrelationEvents bearbetas och HasCorrelatedEvents i varje FabricEvent. |
| --timeout-t | Tids gränsen för servern för att utföra åtgärden på några sekunder. Denna timeout anger den tids period som klienten vill vänta tills den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard \: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-events-cluster-list"></a>sfctl händelser kluster-lista
Hämtar alla kluster relaterade händelser.

Svaret är en lista över ClusterEvent-objekt.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --slut tid UTC [required] | Slut tiden för en uppslags fråga i ISO UTC ÅÅÅÅ-MM-ddTHH \: mm- \: SSZ. |
| --Start-Time-UTC [required] | Start tiden för en uppslags fråga i ISO UTC ÅÅÅÅ-MM-ddTHH \: mm- \: SSZ. |
| --händelse-types-filter | Det här är en kommaavgränsad sträng som anger vilka typer av FabricEvents som endast ska tas med i svaret. |
| --Exkludera-analys-händelser | Den här paramen inaktiverar hämtning av AnalysisEvents om True har skickats. |
| --hoppa över korrelations uppslag | Den här paramen inaktiverar sökningen av CorrelatedEvents-information om True har skickats. annars fylls fältet CorrelationEvents bearbetas och HasCorrelatedEvents i varje FabricEvent. |
| --timeout-t | Tids gränsen för servern för att utföra åtgärden på några sekunder. Denna timeout anger den tids period som klienten vill vänta tills den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard \: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-events-node-list"></a>noden sfctl Events-List
Hämtar ett Node-relaterade händelser.

Svaret är en lista över NodeEvent-objekt.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --slut tid UTC [required] | Slut tiden för en uppslags fråga i ISO UTC ÅÅÅÅ-MM-ddTHH \: mm- \: SSZ. |
| --Node-Name [required] | Nodens namn. |
| --Start-Time-UTC [required] | Start tiden för en uppslags fråga i ISO UTC ÅÅÅÅ-MM-ddTHH \: mm- \: SSZ. |
| --händelse-types-filter | Det här är en kommaavgränsad sträng som anger vilka typer av FabricEvents som endast ska tas med i svaret. |
| --Exkludera-analys-händelser | Den här paramen inaktiverar hämtning av AnalysisEvents om True har skickats. |
| --hoppa över korrelations uppslag | Den här paramen inaktiverar sökningen av CorrelatedEvents-information om True har skickats. annars fylls fältet CorrelationEvents bearbetas och HasCorrelatedEvents i varje FabricEvent. |
| --timeout-t | Tids gränsen för servern för att utföra åtgärden på några sekunder. Denna timeout anger den tids period som klienten vill vänta tills den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard \: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-events-partition-all-replicas-list"></a>sfctl Events partition-alla-Replicas-List
Hämtar alla repliker-relaterade händelser för en partition.

Svaret är en lista över ReplicaEvent-objekt.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --slut tid UTC [required] | Slut tiden för en uppslags fråga i ISO UTC ÅÅÅÅ-MM-ddTHH \: mm- \: SSZ. |
| --partitions-ID [required] | Partitionens identitet. |
| --Start-Time-UTC [required] | Start tiden för en uppslags fråga i ISO UTC ÅÅÅÅ-MM-ddTHH \: mm- \: SSZ. |
| --händelse-types-filter | Det här är en kommaavgränsad sträng som anger vilka typer av FabricEvents som endast ska tas med i svaret. |
| --Exkludera-analys-händelser | Den här paramen inaktiverar hämtning av AnalysisEvents om True har skickats. |
| --hoppa över korrelations uppslag | Den här paramen inaktiverar sökningen av CorrelatedEvents-information om True har skickats. annars fylls fältet CorrelationEvents bearbetas och HasCorrelatedEvents i varje FabricEvent. |
| --timeout-t | Tids gränsen för servern för att utföra åtgärden på några sekunder. Denna timeout anger den tids period som klienten vill vänta tills den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard \: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-events-partition-list"></a>sfctl Events partition-List
Hämtar en partition-relaterade händelser.

Svaret är en lista över PartitionEvent-objekt.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --slut tid UTC [required] | Slut tiden för en uppslags fråga i ISO UTC ÅÅÅÅ-MM-ddTHH \: mm- \: SSZ. |
| --partitions-ID [required] | Partitionens identitet. |
| --Start-Time-UTC [required] | Start tiden för en uppslags fråga i ISO UTC ÅÅÅÅ-MM-ddTHH \: mm- \: SSZ. |
| --händelse-types-filter | Det här är en kommaavgränsad sträng som anger vilka typer av FabricEvents som endast ska tas med i svaret. |
| --Exkludera-analys-händelser | Den här paramen inaktiverar hämtning av AnalysisEvents om True har skickats. |
| --hoppa över korrelations uppslag | Den här paramen inaktiverar sökningen av CorrelatedEvents-information om True har skickats. annars fylls fältet CorrelationEvents bearbetas och HasCorrelatedEvents i varje FabricEvent. |
| --timeout-t | Tids gränsen för servern för att utföra åtgärden på några sekunder. Denna timeout anger den tids period som klienten vill vänta tills den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard \: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-events-partition-replica-list"></a>sfctl Events partition-Replica-List
Hämtar en partitions replik-relaterade händelser.

Svaret är en lista över ReplicaEvent-objekt.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --slut tid UTC [required] | Slut tiden för en uppslags fråga i ISO UTC ÅÅÅÅ-MM-ddTHH \: mm- \: SSZ. |
| --partitions-ID [required] | Partitionens identitet. |
| --replik-ID [obligatoriskt] | Replikens identifierare. |
| --Start-Time-UTC [required] | Start tiden för en uppslags fråga i ISO UTC ÅÅÅÅ-MM-ddTHH \: mm- \: SSZ. |
| --händelse-types-filter | Det här är en kommaavgränsad sträng som anger vilka typer av FabricEvents som endast ska tas med i svaret. |
| --Exkludera-analys-händelser | Den här paramen inaktiverar hämtning av AnalysisEvents om True har skickats. |
| --hoppa över korrelations uppslag | Den här paramen inaktiverar sökningen av CorrelatedEvents-information om True har skickats. annars fylls fältet CorrelationEvents bearbetas och HasCorrelatedEvents i varje FabricEvent. |
| --timeout-t | Tids gränsen för servern för att utföra åtgärden på några sekunder. Denna timeout anger den tids period som klienten vill vänta tills den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard \: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-events-service-list"></a>sfctl Events service-list
Hämtar ett service-relaterade händelser.

Svaret är en lista över ServiceEvent-objekt.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --slut tid UTC [required] | Slut tiden för en uppslags fråga i ISO UTC ÅÅÅÅ-MM-ddTHH \: mm- \: SSZ. |
| --tjänst-ID [obligatoriskt] | Tjänstens identitet. Detta ID är vanligt vis det fullständiga namnet på tjänsten utan URI-schemat "Fabric \: ". Från och med version 6,0 avgränsas hierarkiska namn med " \~ "-symbolen. Om tjänst namnet till exempel är "Fabric \: /MyApp/APP1/svc1" skulle tjänst identiteten vara "MyApp \~ APP1 \~ svc1" i 6.0 + och "MyApp/APP1/svc1" i tidigare versioner. |
| --Start-Time-UTC [required] | Start tiden för en uppslags fråga i ISO UTC ÅÅÅÅ-MM-ddTHH \: mm- \: SSZ. |
| --händelse-types-filter | Det här är en kommaavgränsad sträng som anger vilka typer av FabricEvents som endast ska tas med i svaret. |
| --Exkludera-analys-händelser | Den här paramen inaktiverar hämtning av AnalysisEvents om True har skickats. |
| --hoppa över korrelations uppslag | Den här paramen inaktiverar sökningen av CorrelatedEvents-information om True har skickats. annars fylls fältet CorrelationEvents bearbetas och HasCorrelatedEvents i varje FabricEvent. |
| --timeout-t | Tids gränsen för servern för att utföra åtgärden på några sekunder. Denna timeout anger den tids period som klienten vill vänta tills den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard \: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

