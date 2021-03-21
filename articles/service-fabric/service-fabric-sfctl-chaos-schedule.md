---
title: Azure Service Fabric CLI – sfctl kaos schema
description: Lär dig mer om sfctl, Azure Service Fabric Command Line Interface. Innehåller en lista med kommandon för kaos-schemaläggning.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: dd911574cd37c18e3043cfc2482bd1fcd5f3857b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "86260856"
---
# <a name="sfctl-chaos-schedule"></a>sfctl chaos schedule
Hämta och ange kaos-schemat.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| get | Hämta kaos-schemat som definierar när och hur du ska köra kaos. |
| set | Ange det schema som används av kaos. |

## <a name="sfctl-chaos-schedule-get"></a>sfctl kaos schema get
Hämta kaos-schemat som definierar när och hur du ska köra kaos.

Hämtar den version av kaos-schemat som används och kaos-schemat som definierar när och hur du kör kaos.

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

## <a name="sfctl-chaos-schedule-set"></a>sfctl kaos schema uppsättning
Ange det schema som används av kaos.

Kaos schemalägger automatiskt körningar baserat på kaos-schemat. Kaos-schemat kommer att uppdateras om den angivna versionen matchar versionen på servern. När du uppdaterar kaos-schemat ökas versionen på servern med 1. Versionen på servern kommer att gå tillbaka till 0 när ett stort tal har nåtts. Om kaos körs när det här anropet görs fungerar inte anropet.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --kaos-Parameters-Dictionary | JSON-kodad lista som representerar en mappning av sträng namn till ChaosParameters som ska användas av jobb. |
| --förfallo Datum-datum-UTC | Datum och tid för när du ska sluta använda schemat för att schemalägga kaos.  Standard \: 9999-12-31T23 \: 59 \: 59.999 z. |
| – jobb | JSON-kodad lista över ChaosScheduleJobs som representerar när du ska köra kaos och med vilka parametrar som kaos ska köras med. |
| --Start-Date-UTC | Datum och tid för när du ska börja använda schemat för att schemalägga kaos.  Standard \: 1601 – 01-01T00 \: 00 \: 00.000 z. |
| --timeout-t | Standard \: 60. |
| --version | Schemats versions nummer. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

### <a name="examples"></a>Exempel

Följande kommando anger ett schema (förutsatt att det aktuella schemat har version 0) som börjar på 2016-01-01 och upphör att gälla 2038-01-01 som kör kaos 24 timmar på dagen, 7 dagar i veckan.
Kaos kommer att schemaläggas för klustret för den tiden.
```
sfctl chaos schedule set --version 0 --start-date-utc "2016-01-01T00:00:00.000Z" --expiry-date-utc "2038-01-01T00:00:00.000Z"
    --chaos-parameters-dictionary
    [
    {
        "Key":"adhoc",
        "Value":{
            "MaxConcurrentFaults":3,
            "EnableMoveReplicaFaults":true,
            "ChaosTargetFilter":{
                "NodeTypeInclusionList":[
                "N0010Ref",
                "N0020Ref",
                "N0030Ref",
                "N0040Ref",
                "N0050Ref"
                ]
            },
            "MaxClusterStabilizationTimeoutInSeconds":60,
            "WaitTimeBetweenIterationsInSeconds":15,
            "WaitTimeBetweenFaultsInSeconds":30,
            "TimeToRunInSeconds":"600",
            "Context":{
                "Map":{
                "test":"value"
                }
            },
            "ClusterHealthPolicy":{
                "MaxPercentUnhealthyNodes":0,
                "ConsiderWarningAsError":true,
                "MaxPercentUnhealthyApplications":0
            }
        }
    }
    ]
    --jobs
    [
    {
        "ChaosParameters":"adhoc",
        "Days":{
            "Sunday":true,
            "Monday":true,
            "Tuesday":true,
            "Wednesday":true,
            "Thursday":true,
            "Friday":true,
            "Saturday":true
        },
        "Times":[
            {
                "StartTime":{
                "Hour":0,
                "Minute":0
                },
                "EndTime":{
                "Hour":23,
                "Minute":59
                }
            }
        ]
    }
    ]
```



## <a name="next-steps"></a>Nästa steg
- [Konfigurera](service-fabric-cli.md) Service Fabric cli.
- Lär dig hur du använder Service Fabric CLI med hjälp av [exempel skripten](./scripts/sfctl-upgrade-application.md).
