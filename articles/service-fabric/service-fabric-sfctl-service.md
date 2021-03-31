---
title: Azure Service Fabric CLI – sfctl-tjänst
description: Lär dig mer om sfctl, Azure Service Fabric Command Line Interface. Innehåller en lista med kommandon för att hantera tjänster, tjänst typer och service paket.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 426220f38e6ddfaaf8b24bf4f7d34473d881ae56
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "86245610"
---
# <a name="sfctl-service"></a>sfctl service
Skapa, ta bort och hantera tjänst, tjänst typer och service paket.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| App-Name | Hämtar namnet på Service Fabric program för en tjänst. |
| kod-paket lista | Hämtar listan över kod paket som har distribuerats på en Service Fabric-nod. |
| skapa | Skapar den angivna Service Fabrics tjänsten. |
| delete | Tar bort en befintlig Service Fabric-tjänst. |
| distribuerad-typ | Hämtar information om en angiven tjänste typ för det program som distribuerats på en nod i ett Service Fabric kluster. |
| distribuerad-typ lista | Hämtar listan med information om tjänst typer från de program som distribueras på en nod i ett Service Fabric kluster. |
| beskrivning | Hämtar beskrivningen av en befintlig Service Fabric-tjänst. |
| Get-container – loggar | Hämtar behållar loggarna för container som distribuerats på en Service Fabric-nod. |
| attestering | Hämtar hälso tillståndet för den angivna Service Fabrics tjänsten. |
| information | Hämtar information om den specifika tjänst som hör till Service Fabric programmet. |
| lista | Hämtar information om alla tjänster som tillhör programmet som anges av program-ID: t. |
| manifest | Hämtar manifestet som beskriver en tjänst typ. |
| paketera distribution | Laddar ned paket som är associerade med angivet tjänst manifest till avbildningens cacheminne på den angivna noden. |
| paket-hälsa | Hämtar information om hälso tillståndet för ett tjänst paket för ett särskilt program som har distribuerats för en Service Fabric nod och program. |
| paket-info | Hämtar listan över tjänst paket som har distribuerats på en Service Fabric nod som matchar exakt det angivna namnet. |
| paket lista | Hämtar listan över tjänst paket som har distribuerats på en Service Fabric-nod. |
| recover | Anger Service Fabric kluster som det ska försöka återställa den angivna tjänsten som för närvarande fastnar i kvorum. |
| rapportera hälsa | Skickar en hälso rapport i Service Fabrics tjänsten. |
| försvinner | Lös en Service Fabric partition. |
| typ-lista | Hämtar listan med information om tjänst typer som stöds av en etablerad program typ i ett Service Fabric kluster. |
| update | Uppdaterar den angivna tjänsten med den angivna uppdaterings beskrivningen. |

## <a name="sfctl-service-app-name"></a>sfctl-tjänstens App-Name
Hämtar namnet på Service Fabric program för en tjänst.

Hämtar namnet på programmet för den angivna tjänsten. Ett 404 FABRIC_E_SERVICE_DOES_NOT_EXIST-fel returneras om en tjänst med det angivna tjänst-ID: t inte finns.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
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

## <a name="sfctl-service-code-package-list"></a>sfctl service code-Package-List
Hämtar listan över kod paket som har distribuerats på en Service Fabric-nod.

Hämtar listan över kod paket som har distribuerats på en Service Fabric-nod för det aktuella programmet.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --program-ID [obligatoriskt] | Programmets identitet. Detta är vanligt vis det fullständiga namnet på programmet utan URI-schemat "Fabric \: ". Från och med version 6,0 avgränsas hierarkiska namn med " \~ "-symbolen. Om program namnet till exempel är "Fabric \: /MyApp/APP1" skulle program identiteten vara "MyApp \~ APP1" i 6.0 + och "MyApp/APP1" i tidigare versioner. |
| --Node-Name [required] | Nodens namn. |
| --kod-paket namn | Namnet på kod paketet som anges i tjänst manifestet som är registrerat som en del av en program typ i ett Service Fabric kluster. |
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

## <a name="sfctl-service-create"></a>Skapa sfctl-tjänst
Skapar den angivna Service Fabrics tjänsten.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --app-ID [required] | Programmets identitet. Detta är vanligt vis det fullständiga namnet på programmet utan URI-schemat "Fabric \: ". Från och med version 6,0 avgränsas hierarkiska namn med- \~ symbolen. Om program namnet exempelvis är Fabric \: /MyApp/APP1, skulle program identiteten vara ' MyApp \~ APP1 ' i 6.0 + och ' MyApp/APP1 ' i tidigare versioner. |
| --namn [obligatoriskt] | Namnet på tjänsten. Detta bör vara underordnat program-ID: t. Detta är det fullständiga namnet inklusive `fabric\:` URI. Till exempel service `fabric\:/A/B` är ett underordnat program `fabric\:/A` . |
| --tjänst-typ [obligatoriskt] | Namnet på tjänst typen. |
| --aktiverings läge | Tjänst paketets aktiverings läge. |
| --begränsningar | Placeringen är begränsad som en sträng. Placerings begränsningar är booleska uttryck för Node-egenskaper och möjliggör begränsning av en tjänst till särskilda noder baserat på tjänst kraven. Om du till exempel vill placera en tjänst på noder där NodeType är blått anger du följande \: "NodeColor = = blått". |
| --korrelerad-tjänst | Namnet på mål tjänsten som ska korreleras med. |
| --korrelation | Korrelera tjänsten med en befintlig tjänst med en justerings tilldelning. |
| --DNS-namn | DNS-namnet på den tjänst som ska skapas. Den Service Fabric DNS-systemtjänsten måste vara aktive rad för den här inställningen. |
| --antal instanser | Antalet instanser. Detta gäller endast för tillstånds lösa tjänster. |
| --int-schema | Anger att tjänsten ska vara enhetligt partitionerad i ett intervall med osignerade heltal. |
| --int-Scheme-Count | Antalet partitioner i heltals nyckel intervallet som ska skapas, om ett enhetligt heltals-partitions schema används. |
| --int-schema – hög | Slutet av heltals intervallet för nyckeln, om ett enhetligt heltals-partitions schema används. |
| --int-schema – låg | Början av heltals intervallet för nyckeln, om ett enhetligt heltals-partitions schema används. |
| --Load – Metrics | JSON-kodad lista över mått som används vid belastnings utjämning av tjänster mellan noder. |
| --min-Replica-set-size | Den minsta replik uppsättningens storlek som ett tal. Detta gäller endast för tillstånds känsliga tjänster. |
| --flytta – kostnad | Anger flytt kostnaden för tjänsten. Möjliga värden är \: "noll", "Low", "medium", "High", "VeryHigh". |
| --namngivet-schema | Anger att tjänsten ska ha flera namngivna partitioner. |
| --namngiven-Scheme-List | JSON-kodad lista med namn för att partitionera tjänsten över, om du använder det namngivna partitionsnamnet. |
| --inget beständigt tillstånd | Om värdet är true betyder det att tjänsten inte har något beständigt tillstånd lagrad på den lokala disken, eller att den bara lagrar tillstånd i minnet. |
| --placering-princip-lista | JSON-kodad lista över placerings principer för tjänsten och alla associerade domän namn. Principer kan vara en eller flera av \: `NonPartiallyPlaceService` , `PreferPrimaryDomain` , `RequireDomain` , `RequireDomainDistribution` . |
| --kvorum-förlust-vänta | Den längsta tid i sekunder som en partition kan vara i status för kvorum. Detta gäller endast för tillstånds känsliga tjänster. |
| --Replica-restart-wait | Tiden, i sekunder, mellan när en replik kraschar och när en ny replik skapas. Detta gäller endast för tillstånds känsliga tjänster. |
| --skalning – principer | JSON-kodad lista över skalnings principer för den här tjänsten. |
| --service-placement-Time | Varaktigheten för vilka repliker kan vara inbygga innan rapportering som skapas fastnar. Detta gäller endast för tillstånds känsliga tjänster. |
| --Singleton-schema | Anger att tjänsten ska ha en enda partition eller vara en icke-partitionerad tjänst. |
| --Behåll-för-replik-Behåll | Maximal varaktighet, i sekunder, för vilken StandBy-repliker ska behållas innan de tas bort. Detta gäller endast för tillstånds känsliga tjänster. |
| --tillstånds känslig | Anger att tjänsten är en tillstånds känslig tjänst. |
| --tillstånds lös | Anger att tjänsten är en tillstånds lös tjänst. |
| --mål-replik-ange storlek | Målets replik uppsättnings storlek som ett tal. Detta gäller endast för tillstånds känsliga tjänster. |
| --timeout-t | Standard \: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-service-delete"></a>ta bort sfctl-tjänst
Tar bort en befintlig Service Fabric-tjänst.

En tjänst måste skapas innan den kan tas bort. Som standard kommer Service Fabric att försöka stänga tjänst repliker på ett korrekt sätt och sedan ta bort tjänsten. Men om tjänsten har problem med att stänga av repliken kan borttagnings åtgärden ta lång tid eller bli fastnad. Använd den valfria ForceRemove-flaggan för att hoppa över den korrekt stängnings ordningen och framtvinga borttagning av tjänsten.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --tjänst-ID [obligatoriskt] | Tjänstens identitet. Detta ID är vanligt vis det fullständiga namnet på tjänsten utan URI-schemat "Fabric \: ". Från och med version 6,0 avgränsas hierarkiska namn med " \~ "-symbolen. Om tjänst namnet till exempel är "Fabric \: /MyApp/APP1/svc1" skulle tjänst identiteten vara "MyApp \~ APP1 \~ svc1" i 6.0 + och "MyApp/APP1/svc1" i tidigare versioner. |
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

## <a name="sfctl-service-deployed-type"></a>sfctl-tjänsten har distribuerats-typ
Hämtar information om en angiven tjänste typ för det program som distribuerats på en nod i ett Service Fabric kluster.

Hämtar listan med information om en angiven tjänst typ från de program som distribueras på en nod i ett Service Fabric kluster. Svaret innehåller namnet på tjänst typen, dess registrerings status, kod paketet som registrerade det och aktiverings-ID: t för tjänste paketet. Varje post representerar en aktivering av en tjänst typ, differentierad med aktiverings-ID: t.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --program-ID [obligatoriskt] | Programmets identitet. Detta är vanligt vis det fullständiga namnet på programmet utan URI-schemat "Fabric \: ". Från och med version 6,0 avgränsas hierarkiska namn med " \~ "-symbolen. Om program namnet till exempel är "Fabric \: /MyApp/APP1" skulle program identiteten vara "MyApp \~ APP1" i 6.0 + och "MyApp/APP1" i tidigare versioner. |
| --Node-Name [required] | Nodens namn. |
| --tjänst-typ-namn [obligatoriskt] | Anger namnet på en Service Fabric tjänst typ. |
| --tjänst-manifest-namn | Namnet på tjänst manifestet som används för att filtrera listan över distribuerad tjänst typs information. Om det här alternativet anges innehåller svaret bara information om tjänst typer som har definierats i tjänst manifestet. |
| --timeout-t | Tids gränsen för servern för att utföra åtgärden på några sekunder. Denna timeout anger den tids period som klienten vill vänta tills den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard \: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-service-deployed-type-list"></a>sfctl tjänst distribuerad – typ lista
Hämtar listan med information om tjänst typer från de program som distribueras på en nod i ett Service Fabric kluster.

Hämtar listan med information om tjänst typer från de program som distribueras på en nod i ett Service Fabric kluster. Svaret innehåller namnet på tjänst typen, dess registrerings status, kod paketet som registrerade det och aktiverings-ID: t för tjänste paketet.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --program-ID [obligatoriskt] | Programmets identitet. Detta är vanligt vis det fullständiga namnet på programmet utan URI-schemat "Fabric \: ". Från och med version 6,0 avgränsas hierarkiska namn med " \~ "-symbolen. Om program namnet till exempel är "Fabric \: /MyApp/APP1" skulle program identiteten vara "MyApp \~ APP1" i 6.0 + och "MyApp/APP1" i tidigare versioner. |
| --Node-Name [required] | Nodens namn. |
| --tjänst-manifest-namn | Namnet på tjänst manifestet som används för att filtrera listan över distribuerad tjänst typs information. Om det här alternativet anges innehåller svaret bara information om tjänst typer som har definierats i tjänst manifestet. |
| --timeout-t | Tids gränsen för servern för att utföra åtgärden på några sekunder. Denna timeout anger den tids period som klienten vill vänta tills den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard \: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-service-description"></a>Beskrivning av sfctl-tjänsten
Hämtar beskrivningen av en befintlig Service Fabric-tjänst.

Hämtar beskrivningen av en befintlig Service Fabric-tjänst. En tjänst måste skapas innan dess beskrivning kan hämtas.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
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

## <a name="sfctl-service-get-container-logs"></a>sfctl service get-container-Logs
Hämtar behållar loggarna för container som distribuerats på en Service Fabric-nod.

Hämtar behållar loggarna för container som distribuerats på en Service Fabric-nod för det aktuella kod paketet.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --program-ID [obligatoriskt] | Programmets identitet. Detta är vanligt vis det fullständiga namnet på programmet utan URI-schemat "Fabric \: ". Från och med version 6,0 avgränsas hierarkiska namn med " \~ "-symbolen. Om program namnet till exempel är "Fabric \: /MyApp/APP1" skulle program identiteten vara "MyApp \~ APP1" i 6.0 + och "MyApp/APP1" i tidigare versioner. |
| --kod-paket namn [obligatoriskt] | Namnet på kod paketet som anges i tjänst manifestet som är registrerat som en del av en program typ i ett Service Fabric kluster. |
| --Node-Name [required] | Nodens namn. |
| --tjänst-manifest-namn [obligatoriskt] | Namnet på ett tjänst manifest som är registrerat som en del av en program typ i ett Service Fabric kluster. |
| --föregående | Anger om behållar loggar ska hämtas från avslutade/döda behållare för kod paket instansen. |
| --pilslut | Antal rader som ska visas från slutet av loggarna. Standardvärdet är 100. all för att visa fullständiga loggar. |
| --timeout-t | Tids gränsen för servern för att utföra åtgärden på några sekunder. Denna timeout anger den tids period som klienten vill vänta tills den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard \: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-service-health"></a>sfctl-tjänstens hälsa
Hämtar hälso tillståndet för den angivna Service Fabrics tjänsten.

Hämtar hälso informationen för den angivna tjänsten. Använd EventsHealthStateFilter för att filtrera insamling av hälso händelser som rapporter ATS för tjänsten baserat på hälso tillståndet. Använd PartitionsHealthStateFilter för att filtrera samlingen med returnerade partitioner. Om du anger en tjänst som inte finns i hälso lagret returnerar den här begäran ett fel.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --tjänst-ID [obligatoriskt] | Tjänstens identitet. Detta ID är vanligt vis det fullständiga namnet på tjänsten utan URI-schemat "Fabric \: ". Från och med version 6,0 avgränsas hierarkiska namn med " \~ "-symbolen. Om tjänst namnet till exempel är "Fabric \: /MyApp/APP1/svc1" skulle tjänst identiteten vara "MyApp \~ APP1 \~ svc1" i 6.0 + och "MyApp/APP1/svc1" i tidigare versioner. |
| --händelser-hälso tillstånd – filter | Tillåter filtrering av samlingen av HealthEvent-objekt som returneras baserat på hälso tillstånd. De möjliga värdena för den här parametern är heltals värde för något av följande hälso tillstånd. Endast händelser som matchar filtret returneras. Alla händelser används för att utvärdera det sammanlagda hälso tillståndet. Om inget anges returneras alla poster. Tillstånds värden är flaggning-baserad uppräkning, så värdet kan vara en kombination av dessa värden, erhållna med hjälp av den bitvisa operatorn eller. Om det angivna värdet till exempel är 6 returneras alla händelser med hälso tillstånd svärdet OK (2) och varning (4).  <br> -Standard-standardvärdet. Matchar alla hälso tillstånd. Värdet är noll.  <br> -Inget – filter som inte matchar något värde för hälso tillstånd. Används för att returnera inga resultat för en specifik samling av tillstånd. Värdet är 1.  <br> – OK-filter som matchar inmatade hälso tillstånds värden OK. Värdet är 2.  <br> -Varnings filter som matchar inmatade värde varningar för hälso tillstånd. Värdet är 4.  <br> -Fel-filter som matchar InInformationen med hälso tillstånds värde fel. Värdet är 8.  <br> – Alla – filter som matchar indatamängden med ett värde för hälso tillstånd. Värdet är 65535. |
| --Exkludera-hälso statistik | Anger om hälso statistik ska returneras som en del av frågeresultatet. Falskt som standard. Statistiken visar antalet underordnade entiteter i hälso tillståndet OK, varning och fel. |
| --partitioner-hälso tillstånd – filter | Tillåter filtrering av de partitioner hälso tillstånds objekt som returneras i resultatet av tjänstens hälso tillstånd baserat på deras hälso tillstånd. De möjliga värdena för den här parametern är heltals värde för något av följande hälso tillstånd. Endast partitioner som matchar filtret returneras. Alla partitioner används för att utvärdera det sammanlagda hälso tillståndet. Om inget anges returneras alla poster. Tillstånds värden är flaggning-baserad uppräkning, vilket innebär att värdet kan vara en kombination av dessa värden som erhålls med hjälp av bitvis-eller-operator. Om det tillhandahållna värdet till exempel är 6 returneras hälso tillståndet för partitioner med värdet OK (2) och varning (4).  <br> -Standard-standardvärdet. Matchar alla hälso tillstånd. Värdet är noll.  <br> -Inget – filter som inte matchar något värde för hälso tillstånd. Används för att returnera inga resultat för en specifik samling av tillstånd. Värdet är 1.  <br> – OK-filter som matchar inmatade hälso tillstånds värden OK. Värdet är 2.  <br> -Varnings filter som matchar inmatade värde varningar för hälso tillstånd. Värdet är 4.  <br> -Fel-filter som matchar InInformationen med hälso tillstånds värde fel. Värdet är 8.  <br> – Alla – filter som matchar indatamängden med ett värde för hälso tillstånd. Värdet är 65535. |
| --timeout-t | Tids gränsen för servern för att utföra åtgärden på några sekunder. Denna timeout anger den tids period som klienten vill vänta tills den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard \: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-service-info"></a>information om sfctl-tjänsten
Hämtar information om den specifika tjänst som hör till Service Fabric programmet.

Returnerar information om den angivna tjänsten som tillhör det angivna Service Fabric programmet.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --program-ID [obligatoriskt] | Programmets identitet. Detta är vanligt vis det fullständiga namnet på programmet utan URI-schemat "Fabric \: ". Från och med version 6,0 avgränsas hierarkiska namn med " \~ "-symbolen. Om program namnet till exempel är "Fabric \: /MyApp/APP1" skulle program identiteten vara "MyApp \~ APP1" i 6.0 + och "MyApp/APP1" i tidigare versioner. |
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

## <a name="sfctl-service-list"></a>lista över sfctl-tjänster
Hämtar information om alla tjänster som tillhör programmet som anges av program-ID: t.

Returnerar information om alla tjänster som tillhör programmet som anges av program-ID: t.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --program-ID [obligatoriskt] | Programmets identitet. Detta är vanligt vis det fullständiga namnet på programmet utan URI-schemat "Fabric \: ". Från och med version 6,0 avgränsas hierarkiska namn med " \~ "-symbolen. Om program namnet till exempel är "Fabric \: /MyApp/APP1" skulle program identiteten vara "MyApp \~ APP1" i 6.0 + och "MyApp/APP1" i tidigare versioner. |
| --fortsättnings-token | Parametern för fortsatt token används för att hämta nästa uppsättning resultat. En fortsättnings-token med ett värde som inte är tom inkluderas i svaret på API: et när resultatet från systemet inte passar i ett enda svar. När det här värdet skickas till nästa API-anrop returnerar API nästa uppsättning resultat. Om det inte finns några ytterligare resultat innehåller inte fortsättnings-token ett värde. Värdet för den här parametern får inte vara URL-kodat. |
| --tjänst-typ-namn | Tjänst typs namnet som används för att filtrera tjänsterna att fråga efter. |
| --timeout-t | Tids gränsen för servern för att utföra åtgärden på några sekunder. Denna timeout anger den tids period som klienten vill vänta tills den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard \: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-service-manifest"></a>sfctl-tjänst manifest
Hämtar manifestet som beskriver en tjänst typ.

Hämtar manifestet som beskriver en tjänst typ. Svaret innehåller tjänst Manifestets XML som en sträng.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --program-typ-namn [obligatoriskt] | Namnet på program typen. |
| --program-Type-version [required] | Versionen av program typen. |
| --tjänst-manifest-namn [obligatoriskt] | Namnet på ett tjänst manifest som är registrerat som en del av en program typ i ett Service Fabric kluster. |
| --timeout-t | Tids gränsen för servern för att utföra åtgärden på några sekunder. Denna timeout anger den tids period som klienten vill vänta tills den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard \: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-service-package-deploy"></a>sfctl service-paket – distribuera
Laddar ned paket som är associerade med angivet tjänst manifest till avbildningens cacheminne på den angivna noden.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --app-Type-Name [required] | Namnet på applikations manifestet för motsvarande begärd tjänst manifest. |
| --app-Type-version [required] | Versionen av applikations manifestet för motsvarande begärda tjänst manifest. |
| --Node-Name [required] | Nodens namn. |
| --tjänst-manifest-namn [obligatoriskt] | Namnet på tjänst manifestet som är associerat med de paket som ska hämtas. |
| --Dela-princip | JSON-kodad lista över delnings principer. Varje delnings princip element består av ett "name" och "scope". Namnet motsvarar namnet på koden, konfigurationen eller data paketet som ska delas. Omfånget kan vara antingen none, all, Code, config eller data. |
| --timeout-t | Standard \: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-service-package-health"></a>sfctl Service Package-Health
Hämtar information om hälso tillståndet för ett tjänst paket för ett särskilt program som har distribuerats för en Service Fabric nod och program.

Hämtar information om hälso tillståndet för ett tjänst paket för ett enskilt program som distribuerats på en Service Fabric-nod. Använd EventsHealthStateFilter om du vill filtrera efter samlingen av HealthEvent-objekt som rapporteras i det distribuerade tjänst paketet baserat på hälso tillstånd.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --program-ID [obligatoriskt] | Programmets identitet. Detta är vanligt vis det fullständiga namnet på programmet utan URI-schemat "Fabric \: ". Från och med version 6,0 avgränsas hierarkiska namn med " \~ "-symbolen. Om program namnet till exempel är "Fabric \: /MyApp/APP1" skulle program identiteten vara "MyApp \~ APP1" i 6.0 + och "MyApp/APP1" i tidigare versioner. |
| --Node-Name [required] | Nodens namn. |
| --tjänst-paket-namn [obligatoriskt] | Namnet på tjänst paketet. |
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

## <a name="sfctl-service-package-info"></a>sfctl service-paket – info
Hämtar listan över tjänst paket som har distribuerats på en Service Fabric nod som matchar exakt det angivna namnet.

Returnerar information om de tjänst paket som har distribuerats på en Service Fabric-nod för det aktuella programmet. De här resultaten är av tjänst paket vars namn matchar exakt det tjänste paket namn som anges som parameter.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --program-ID [obligatoriskt] | Programmets identitet. Detta är vanligt vis det fullständiga namnet på programmet utan URI-schemat "Fabric \: ". Från och med version 6,0 avgränsas hierarkiska namn med " \~ "-symbolen. Om program namnet till exempel är "Fabric \: /MyApp/APP1" skulle program identiteten vara "MyApp \~ APP1" i 6.0 + och "MyApp/APP1" i tidigare versioner. |
| --Node-Name [required] | Nodens namn. |
| --tjänst-paket-namn [obligatoriskt] | Namnet på tjänst paketet. |
| --timeout-t | Tids gränsen för servern för att utföra åtgärden på några sekunder. Denna timeout anger den tids period som klienten vill vänta tills den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard \: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-service-package-list"></a>sfctl Service Package-List
Hämtar listan över tjänst paket som har distribuerats på en Service Fabric-nod.

Returnerar information om de tjänst paket som har distribuerats på en Service Fabric-nod för det aktuella programmet.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --program-ID [obligatoriskt] | Programmets identitet. Detta är vanligt vis det fullständiga namnet på programmet utan URI-schemat "Fabric \: ". Från och med version 6,0 avgränsas hierarkiska namn med " \~ "-symbolen. Om program namnet till exempel är "Fabric \: /MyApp/APP1" skulle program identiteten vara "MyApp \~ APP1" i 6.0 + och "MyApp/APP1" i tidigare versioner. |
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

## <a name="sfctl-service-recover"></a>återställa sfctl-tjänst
Anger Service Fabric kluster som det ska försöka återställa den angivna tjänsten som för närvarande fastnar i kvorum.

Anger Service Fabric kluster som det ska försöka återställa den angivna tjänsten som för närvarande fastnar i kvorum. Den här åtgärden bör endast utföras om det är känt att de repliker som är nere inte kan återställas. Felaktig användning av detta API kan orsaka potentiell data förlust.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
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

## <a name="sfctl-service-report-health"></a>sfctl service-rapport – hälsa
Skickar en hälso rapport i Service Fabrics tjänsten.

Rapporterar hälso tillståndet för den angivna Service Fabrics tjänsten. Rapporten måste innehålla information om källan till hälso rapporten och egenskapen som den rapporteras om. Rapporten skickas till en Service Fabric Gateway-tjänst som vidarebefordrar till hälso lagret. Rapporten kan godkännas av gatewayen, men avvisas av hälso lagret efter extra verifiering. Hälso lagret kan till exempel avvisa rapporten på grund av en ogiltig parameter, t. ex. ett inaktuellt ordnings nummer. Om du vill se om rapporten har använts i hälso lagret kontrollerar du att rapporten visas i tjänstens hälso tillstånds händelser.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --Health-Property [required] | Hälso informationens egenskaper. <br><br> En entitet kan ha hälso rapporter för olika egenskaper. Egenskapen är en sträng och inte en fast uppräkning som tillåter rapportörens flexibilitet att kategorisera det tillstånds villkor som utlöser rapporten. Till exempel kan en rapportör med SourceId "LocalWatchdog" övervaka statusen för den tillgängliga disken på en nod, så att den kan rapportera egenskapen "AvailableDisk" på noden. Samma rapportör kan övervaka nodens anslutning, så att den kan rapportera en egenskap "anslutning" på samma nod. I hälso lagret behandlas dessa rapporter som separata hälso händelser för den angivna noden. Tillsammans med värdet för SourceId kan egenskapen unikt identifiera hälso informationen. |
| --hälso tillstånd [krävs] | Möjliga värden är \: "ogiltig", "OK", "varning", "Error", "okänd". |
| --tjänst-ID [obligatoriskt] | Tjänstens identitet. <br><br> Detta är vanligt vis det fullständiga namnet på tjänsten utan URI-schemat "Fabric \: ". Från och med version 6,0 avgränsas hierarkiska namn med- \~ symbolen. Om tjänst namnet till exempel är Fabric \: /MyApp/APP1/svc1, skulle tjänst identiteten vara "MyApp \~ APP1 \~ svc1" i 6.0 + och "MyApp/APP1/svc1" i tidigare versioner. |
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

## <a name="sfctl-service-resolve"></a>lösa sfctl-tjänst
Lös en Service Fabric partition.

Lös en Service Fabric-diskpartition för att hämta slut punkterna för tjänst replikerna.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --tjänst-ID [obligatoriskt] | Tjänstens identitet. Detta ID är vanligt vis det fullständiga namnet på tjänsten utan URI-schemat "Fabric \: ". Från och med version 6,0 avgränsas hierarkiska namn med " \~ "-symbolen. Om tjänst namnet till exempel är "Fabric \: /MyApp/APP1/svc1" skulle tjänst identiteten vara "MyApp \~ APP1 \~ svc1" i 6.0 + och "MyApp/APP1/svc1" i tidigare versioner. |
| --partition-nyckel-typ | Nyckel typ för partitionen. Den här parametern krävs om partitions schema för tjänsten är Int64Range eller namngett. De möjliga värdena är följande. -Ingen (1)-anger att parametern PartitionKeyValue inte har angetts. Detta gäller för partitionerna med partitionerings schema som singleton. Detta är standardvärdet. Värdet är 1. -Int64Range (2)-anger att parametern PartitionKeyValue är en Int64-partitionsnyckel. Detta gäller för partitionerna med partitionerings schema som Int64Range. Värdet är 2. -Named (3) – anger att parametern PartitionKeyValue är ett namn på partitionen. Detta är giltigt för partitionerna med partitionerings schema enligt namnet. Värdet är 3. |
| --partition-nyckel-värde | Partitionsnyckel. Detta krävs om partitions schema för tjänsten är Int64Range eller namngett. Detta är inte partitions-ID: t, utan i stället heltals nyckel svärdet eller namnet på partitions-ID: t. Om till exempel din tjänst använder intervallbaserade partitioner från 0 till 10, skulle de PartitionKeyValue vara ett heltal inom intervallet. Fråga tjänst beskrivning för att se intervallet eller namnet. |
| --tidigare-RSP-version | Värdet i fältet version för det svar som togs emot tidigare. Detta krävs om användaren vet att resultatet som tidigare har infallit är inaktuellt. |
| --timeout-t | Tids gränsen för servern för att utföra åtgärden på några sekunder. Denna timeout anger den tids period som klienten vill vänta tills den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard \: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-service-type-list"></a>sfctl Service Type-List
Hämtar listan med information om tjänst typer som stöds av en etablerad program typ i ett Service Fabric kluster.

Hämtar listan med information om tjänst typer som stöds av en etablerad program typ i ett Service Fabric kluster. Den angivna program typen måste finnas. Annars returneras en 404-status.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --program-typ-namn [obligatoriskt] | Namnet på program typen. |
| --program-Type-version [required] | Versionen av program typen. |
| --timeout-t | Tids gränsen för servern för att utföra åtgärden på några sekunder. Denna timeout anger den tids period som klienten vill vänta tills den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard \: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-service-update"></a>uppdatering av sfctl-tjänsten
Uppdaterar den angivna tjänsten med den angivna uppdaterings beskrivningen.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --tjänst-ID [obligatoriskt] | Tjänstens identitet. Detta är vanligt vis det fullständiga namnet på tjänsten utan URI-schemat "Fabric \: ". Från och med version 6,0 avgränsas hierarkiska namn med " \~ "-symbolen. Om tjänst namnet till exempel är Fabric \: /MyApp/APP1/svc1, skulle tjänst identiteten vara "MyApp \~ APP1 \~ svc1" i 6.0 + och "MyApp/APP1/svc1" i tidigare versioner. |
| --begränsningar | Placeringen är begränsad som en sträng. Placerings begränsningar är booleska uttryck för Node-egenskaper och möjliggör begränsning av en tjänst till särskilda noder baserat på tjänst kraven. Om du till exempel vill placera en tjänst på noder där NodeType är blått anger du följande \: "NodeColor = = blått". |
| --korrelerad-tjänst | Namnet på mål tjänsten som ska korreleras med. |
| --korrelation | Korrelera tjänsten med en befintlig tjänst med en justerings tilldelning. |
| --antal instanser | Antalet instanser. Detta gäller endast för tillstånds lösa tjänster. |
| --Load – Metrics | JSON-kodad lista över mått som används vid belastnings utjämning på noder. |
| --min-Replica-set-size | Den minsta replik uppsättningens storlek som ett tal. Detta gäller endast för tillstånds känsliga tjänster. |
| --flytta – kostnad | Anger flytt kostnaden för tjänsten. Möjliga värden är \: "noll", "Low", "medium", "High", "VeryHigh". |
| --placering-princip-lista | JSON-kodad lista över placerings principer för tjänsten och alla associerade domän namn. Principer kan vara en eller flera av \: `NonPartiallyPlaceService` , `PreferPrimaryDomain` , `RequireDomain` , `RequireDomainDistribution` . |
| --kvorum-förlust-vänta | Den längsta tid i sekunder som en partition kan vara i status för kvorum. Detta gäller endast för tillstånds känsliga tjänster. |
| --Replica-restart-wait | Tiden, i sekunder, mellan när en replik kraschar och när en ny replik skapas. Detta gäller endast för tillstånds känsliga tjänster. |
| --skalning – principer | JSON-kodad lista över skalnings principer för den här tjänsten. |
| --service-placement-Time | Varaktigheten för vilka repliker kan vara inbygga innan rapportering som skapas fastnar. Detta gäller endast för tillstånds känsliga tjänster. |
| --Behåll-för-replik-Behåll | Maximal varaktighet, i sekunder, för vilken StandBy-repliker ska behållas innan de tas bort. Detta gäller endast för tillstånds känsliga tjänster. |
| --tillstånds känslig | Anger att mål tjänsten är en tillstånds känslig tjänst. |
| --tillstånds lös | Anger att mål tjänsten är en tillstånds lös tjänst. |
| --mål-replik-ange storlek | Målets replik uppsättnings storlek som ett tal. Detta gäller endast för tillstånds känsliga tjänster. |
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
