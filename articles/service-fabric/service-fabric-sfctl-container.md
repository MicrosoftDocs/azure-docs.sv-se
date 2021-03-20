---
title: Azure Service Fabric CLI – sfctl container
description: Lär dig mer om sfctl, Azure Service Fabric Command Line Interface. Innehåller en lista med kommandon för behållare.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: f82883b68ab911fb0b89fc117d9a9d77e05a781a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "86245899"
---
# <a name="sfctl-container"></a>sfctl container
Kör behållar relaterade kommandon på en klusternod.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| anropa-API | Anropa container-API på en behållare som distribuerats på en Service Fabric-nod för det aktuella kod paketet. |
| loggar | Hämtar behållar loggarna för container som distribuerats på en Service Fabric-nod. |

## <a name="sfctl-container-invoke-api"></a>sfctl container Invoke-API
Anropa container-API på en behållare som distribuerats på en Service Fabric-nod för det aktuella kod paketet.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --program-ID [obligatoriskt] | Programmets identitet. <br><br> Detta är vanligt vis det fullständiga namnet på programmet utan URI-schemat "Fabric \: ". Från och med version 6,0 avgränsas hierarkiska namn med " \~ "-symbolen. Om program namnet till exempel är "Fabric \: /MyApp/APP1" skulle program identiteten vara "MyApp \~ APP1" i 6.0 + och "MyApp/APP1" i tidigare versioner. |
| --kod-paket instans-ID [obligatoriskt] | ID som unikt identifierar en kod paket instans som distribuerats på en Service Fabric-nod. <br><br> Kan hämtas av ' service code-Package-List '. |
| --kod-paket namn [obligatoriskt] | Namnet på kod paketet som anges i tjänst manifestet som är registrerat som en del av en program typ i ett Service Fabric kluster. |
| --container-API-URI-Path [required] | Container REST API URI-sökväg, Använd {ID} i stället för container Name/ID. |
| --Node-Name [required] | Nodens namn. |
| --tjänst-manifest-namn [obligatoriskt] | Namnet på ett tjänst manifest som är registrerat som en del av en program typ i ett Service Fabric kluster. |
| --container-API-Body | HTTP-begärantext för container REST API. |
| --container-API-Content-Type | Innehålls typ för container REST API, standardvärdet är Application/JSON. |
| --container-API-HTTP-verb | HTTP-verb för container REST API, hämtas som standard. |
| --timeout-t | Standard \: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-container-logs"></a>sfctl container-loggar
Hämtar behållar loggarna för container som distribuerats på en Service Fabric-nod.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --program-ID [obligatoriskt] | Programmets identitet. <br><br> Detta är vanligt vis det fullständiga namnet på programmet utan URI-schemat "Fabric \: ". Från och med version 6,0 avgränsas hierarkiska namn med " \~ "-symbolen. Om program namnet till exempel är "Fabric \: /MyApp/APP1" skulle program identiteten vara "MyApp \~ APP1" i 6.0 + och "MyApp/APP1" i tidigare versioner. |
| --kod-paket instans-ID [obligatoriskt] | Kod paketets instans-ID, som kan hämtas av ' service code-Package-List '. |
| --kod-paket namn [obligatoriskt] | Namnet på kod paketet som anges i tjänst manifestet som är registrerat som en del av en program typ i ett Service Fabric kluster. |
| --Node-Name [required] | Nodens namn. |
| --tjänst-manifest-namn [obligatoriskt] | Namnet på ett tjänst manifest som är registrerat som en del av en program typ i ett Service Fabric kluster. |
| --pilslut | Antal rader som ska visas från slutet av loggarna. Standardvärdet är 100. all för att visa fullständiga loggar. |
| --timeout-t | Standard \: 60. |

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
