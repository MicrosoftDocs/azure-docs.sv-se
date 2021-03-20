---
title: Azure Service Fabric CLI – sfctl-program
description: Lär dig mer om sfctl, Azure Service Fabric Command Line Interface. Innehåller en lista med kommandon för att hantera program.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 880770345eb7d65850db322bd97d64c60b6681ee
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "86260860"
---
# <a name="sfctl-application"></a>sfctl application
Skapa, ta bort och hantera program och program typer.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| skapa | Skapar ett Service Fabric program med hjälp av den angivna beskrivningen. |
| delete | Tar bort ett befintligt Service Fabric-program. |
| distribueras | Hämtar information om ett program som distribuerats på en Service Fabric-nod. |
| distribuerad hälsa | Hämtar information om hälso tillståndet för ett program som distribuerats på en Service Fabric-nod. |
| distribuerad lista | Hämtar listan över program som har distribuerats på en Service Fabric-nod. |
| attestering | Hämtar hälso tillståndet för Service Fabric-programmet. |
| information | Hämtar information om ett Service Fabric-program. |
| lista | Hämtar listan med program som skapats i det Service Fabric kluster som matchar de angivna filtren. |
| läsa in | Läser in information om ett Service Fabric-program. |
| manifest | Hämtar manifestet som beskriver en program typ. |
| Skrivet | Etablerar eller registrerar en Service Fabric program typ med klustret med hjälp av sfpkg-paketet i den externa lagringen eller med hjälp av programpaketet i avbildnings arkivet. |
| rapportera hälsa | Skickar en hälso rapport i Service Fabric programmet. |
| typ | Hämtar listan över program typer i Service Fabric klustret som matchar det angivna namnet. |
| typ-lista | Hämtar listan över program typer i Service Fabric klustret. |
| avetablera | Tar bort eller avregistrerar en Service Fabric program typ från klustret. |
| uppgradera | Startar uppgraderingen av ett program i Service Fabric klustret. |
| uppgradera – återuppta | Återupptar uppgraderingen av ett program i Service Fabric klustret. |
| uppgradera-återställning | Börjar återställa den pågående uppgraderingen av ett program i Service Fabric klustret. |
| uppgradering-status | Hämtar information om den senaste uppgraderingen som utförs i det här programmet. |
| upload | Kopiera ett Service Fabric-programpaket till avbildnings arkivet. |

## <a name="sfctl-application-create"></a>Skapa sfctl-program
Skapar ett Service Fabric program med hjälp av den angivna beskrivningen.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --App-Name [required] | Namnet på programmet, inklusive URI-schemat ' Fabric \: '. |
| --app-Type [required] | Det program typs namn som finns i applikations manifestet. |
| --app-version [required] | Versionen av program typen som definieras i applikations manifestet. |
| --Max-Node-Count | Det maximala antalet noder där Service Fabric reserverar kapaciteten för det här programmet. Observera att detta inte innebär att tjänsterna i det här programmet kommer att placeras på alla noderna. |
| --mått | En JSON-kodad lista över beskrivningar av program kapacitets värden. Ett mått definieras som ett namn som är associerat med en uppsättning kapaciteter för varje nod som programmet finns på. |
| --min-Node-Count | Det minsta antal noder där Service Fabric reserverar kapaciteten för det här programmet. Observera att detta inte innebär att tjänsterna i det här programmet kommer att placeras på alla noderna. |
| --parametrar | En JSON-kodad lista över program parameter åsidosättningar som ska tillämpas när du skapar programmet. |
| --timeout-t | Standard \: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-application-delete"></a>ta bort sfctl-program
Tar bort ett befintligt Service Fabric-program.

Ett program måste skapas innan det kan tas bort. Om du tar bort ett program tas alla tjänster som ingår i programmet bort. Som standard kommer Service Fabric att försöka stänga tjänst repliker på ett korrekt sätt och sedan ta bort tjänsten. Men om en tjänst har problem med att stänga av repliken kan borttagnings åtgärden ta lång tid eller bli fastnad. Använd den valfria ForceRemove-flaggan för att hoppa över den korrekt stängnings ordningen och framtvinga borttagning av programmet och alla dess tjänster.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --program-ID [obligatoriskt] | Programmets identitet. Detta är vanligt vis det fullständiga namnet på programmet utan URI-schemat "Fabric \: ". Från och med version 6,0 avgränsas hierarkiska namn med " \~ "-symbolen. Om program namnet till exempel är "Fabric \: /MyApp/APP1" skulle program identiteten vara "MyApp \~ APP1" i 6.0 + och "MyApp/APP1" i tidigare versioner. |
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

## <a name="sfctl-application-deployed"></a>sfctl-programmet har distribuerats
Hämtar information om ett program som distribuerats på en Service Fabric-nod.

Den här frågan returnerar system program information om det angivna program-ID: t är för System program. Resultaten omfattar distribuerade program i aktiva, aktiverade och hämtnings bara tillstånd. Den här frågan kräver att nodnamnet motsvarar en nod i klustret. Frågan Miss lyckas om det angivna nodnamnet inte pekar på några aktiva Service Fabric noder i klustret.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --program-ID [obligatoriskt] | Programmets identitet. Detta är vanligt vis det fullständiga namnet på programmet utan URI-schemat "Fabric \: ". Från och med version 6,0 avgränsas hierarkiska namn med " \~ "-symbolen. Om program namnet till exempel är "Fabric \: /MyApp/APP1" skulle program identiteten vara "MyApp \~ APP1" i 6.0 + och "MyApp/APP1" i tidigare versioner. |
| --Node-Name [required] | Nodens namn. |
| --ta med hälso tillstånd | Ta med hälso tillståndet för en entitet. Om den här parametern är falsk eller inte anges, är det hälso tillstånd som returnerades "okänt". När värdet är true går frågan parallellt till noden och tjänsten för hälso tillstånds systemet innan resultatet slås samman. Därför är frågan dyrare och kan ta längre tid. |
| --timeout-t | Tids gränsen för servern för att utföra åtgärden på några sekunder. Denna timeout anger den tids period som klienten vill vänta tills den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard \: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-application-deployed-health"></a>sfctl-programmet har distribuerats – hälsa
Hämtar information om hälso tillståndet för ett program som distribuerats på en Service Fabric-nod.

Hämtar information om hälso tillståndet för ett program som distribuerats på en Service Fabric-nod. Använd EventsHealthStateFilter om du vill filtrera efter samlingen av HealthEvent-objekt som rapporteras i det distribuerade programmet baserat på hälso tillstånd. Använd DeployedServicePackagesHealthStateFilter om du vill filtrera efter DeployedServicePackageHealth underordnade baserat på hälso tillstånd.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --program-ID [obligatoriskt] | Programmets identitet. Detta är vanligt vis det fullständiga namnet på programmet utan URI-schemat "Fabric \: ". Från och med version 6,0 avgränsas hierarkiska namn med " \~ "-symbolen. Om program namnet till exempel är "Fabric \: /MyApp/APP1" skulle program identiteten vara "MyApp \~ APP1" i 6.0 + och "MyApp/APP1" i tidigare versioner. |
| --Node-Name [required] | Nodens namn. |
| --distribuerade-tjänst-paket-hälso tillstånd-filter | Tillåter filtrering av de distribuerade tjänst paketets hälso tillstånds objekt som returneras i resultatet av distribuerad program hälso fråga baserat på deras hälso tillstånd. De möjliga värdena för den här parametern är heltals värde för något av följande hälso tillstånd. Endast distribuerade tjänst paket som matchar filtret returneras. Alla distribuerade tjänst paket används för att utvärdera det sammanlagda hälso tillståndet för det distribuerade programmet. Om inget anges returneras alla poster. Tillstånds värden är flaggning-baserad uppräkning, så värdet kan vara en kombination av dessa värden, erhållna med hjälp av den bitvisa operatorn eller. Om det tillhandahållna värdet till exempel är 6 returneras hälso tillståndet för tjänst paket med hälso tillstånds värdet OK (2) och varning (4).  <br> -Standard-standardvärdet. Matchar alla hälso tillstånd. Värdet är noll.  <br> -Inget – filter som inte matchar något värde för hälso tillstånd. Används för att returnera inga resultat för en specifik samling av tillstånd. Värdet är 1.  <br> – OK-filter som matchar inmatade hälso tillstånds värden OK. Värdet är 2.  <br> -Varnings filter som matchar inmatade värde varningar för hälso tillstånd. Värdet är 4.  <br> -Fel-filter som matchar InInformationen med hälso tillstånds värde fel. Värdet är 8.  <br> – Alla – filter som matchar indatamängden med ett värde för hälso tillstånd. Värdet är 65535. |
| --händelser-hälso tillstånd – filter | Tillåter filtrering av samlingen av HealthEvent-objekt som returneras baserat på hälso tillstånd. De möjliga värdena för den här parametern är heltals värde för något av följande hälso tillstånd. Endast händelser som matchar filtret returneras. Alla händelser används för att utvärdera det sammanlagda hälso tillståndet. Om inget anges returneras alla poster. Tillstånds värden är flaggning-baserad uppräkning, så värdet kan vara en kombination av dessa värden, erhållna med hjälp av den bitvisa operatorn eller. Om det angivna värdet till exempel är 6 returneras alla händelser med hälso tillstånd svärdet OK (2) och varning (4).  <br> -Standard-standardvärdet. Matchar alla hälso tillstånd. Värdet är noll.  <br> -Inget – filter som inte matchar något värde för hälso tillstånd. Används för att returnera inga resultat för en specifik samling av tillstånd. Värdet är 1.  <br> – OK-filter som matchar inmatade hälso tillstånds värden OK. Värdet är 2.  <br> -Varnings filter som matchar inmatade värde varningar för hälso tillstånd. Värdet är 4.  <br> -Fel-filter som matchar InInformationen med hälso tillstånds värde fel. Värdet är 8.  <br> – Alla – filter som matchar indatamängden med ett värde för hälso tillstånd. Värdet är 65535. |
| --Exkludera-hälso statistik | Anger om hälso statistik ska returneras som en del av frågeresultatet. Falskt som standard. Statistiken visar antalet underordnade entiteter i hälso tillståndet OK, varning och fel. |
| --timeout-t | Tids gränsen för servern för att utföra åtgärden på några sekunder. Denna timeout anger den tids period som klienten vill vänta tills den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard \: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-application-deployed-list"></a>sfctl program distribuerad – lista
Hämtar listan över program som har distribuerats på en Service Fabric-nod.

Hämtar listan över program som har distribuerats på en Service Fabric-nod. Resultaten omfattar inte information om distribuerade system program, om det inte uttryckligen efter frågas av ID. Resultaten omfattar distribuerade program i aktiva, aktiverade och hämtnings bara tillstånd. Den här frågan kräver att nodnamnet motsvarar en nod i klustret. Frågan Miss lyckas om det angivna nodnamnet inte pekar på några aktiva Service Fabric noder i klustret.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --Node-Name [required] | Nodens namn. |
| --fortsättnings-token | Parametern för fortsatt token används för att hämta nästa uppsättning resultat. En fortsättnings-token med ett värde som inte är tom inkluderas i svaret på API: et när resultatet från systemet inte passar i ett enda svar. När det här värdet skickas till nästa API-anrop returnerar API nästa uppsättning resultat. Om det inte finns några ytterligare resultat innehåller inte fortsättnings-token ett värde. Värdet för den här parametern får inte vara URL-kodat. |
| --ta med hälso tillstånd | Ta med hälso tillståndet för en entitet. Om den här parametern är falsk eller inte anges, är det hälso tillstånd som returnerades "okänt". När värdet är true går frågan parallellt till noden och tjänsten för hälso tillstånds systemet innan resultatet slås samman. Därför är frågan dyrare och kan ta längre tid. |
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

## <a name="sfctl-application-health"></a>sfctl program hälsa
Hämtar hälso tillståndet för Service Fabric-programmet.

Returnerar hälso status för Service Fabric-programmet. Svaret rapporterar antingen OK, fel eller varnings hälso tillstånd. Om entiteten inte hittas i hälso lagret kommer den att returnera fel.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --program-ID [obligatoriskt] | Programmets identitet. Detta är vanligt vis det fullständiga namnet på programmet utan URI-schemat "Fabric \: ". Från och med version 6,0 avgränsas hierarkiska namn med " \~ "-symbolen. Om program namnet till exempel är "Fabric \: /MyApp/APP1" skulle program identiteten vara "MyApp \~ APP1" i 6.0 + och "MyApp/APP1" i tidigare versioner. |
| --distribuerade-program-hälso tillstånd – filter | Tillåter filtrering av de distribuerade program hälso tillstånds objekt som returneras i resultatet av en program hälso fråga baserat på deras hälso tillstånd. De möjliga värdena för den här parametern är heltals värde för något av följande hälso tillstånd. Endast distribuerade program som matchar filtret kommer att returneras. Alla distribuerade program används för att utvärdera det sammanlagda hälso tillståndet. Om inget anges returneras alla poster. Tillstånds värden är flaggning-baserad uppräkning, så värdet kan vara en kombination av dessa värden, erhållna med hjälp av bitvis-eller-operator. Om det tillhandahållna värdet till exempel är 6 returneras hälso tillståndet för distribuerade program med hälso tillståndet OK (2) och varning (4).  <br> -Standard-standardvärdet. Matchar alla hälso tillstånd. Värdet är noll.  <br> -Inget – filter som inte matchar något värde för hälso tillstånd. Används för att returnera inga resultat för en specifik samling av tillstånd. Värdet är 1.  <br> – OK-filter som matchar inmatade hälso tillstånds värden OK. Värdet är 2.  <br> -Varnings filter som matchar inmatade värde varningar för hälso tillstånd. Värdet är 4.  <br> -Fel-filter som matchar InInformationen med hälso tillstånds värde fel. Värdet är 8.  <br> – Alla – filter som matchar indatamängden med ett värde för hälso tillstånd. Värdet är 65535. |
| --händelser-hälso tillstånd – filter | Tillåter filtrering av samlingen av HealthEvent-objekt som returneras baserat på hälso tillstånd. De möjliga värdena för den här parametern är heltals värde för något av följande hälso tillstånd. Endast händelser som matchar filtret returneras. Alla händelser används för att utvärdera det sammanlagda hälso tillståndet. Om inget anges returneras alla poster. Tillstånds värden är flaggning-baserad uppräkning, så värdet kan vara en kombination av dessa värden, erhållna med hjälp av den bitvisa operatorn eller. Om det angivna värdet till exempel är 6 returneras alla händelser med hälso tillstånd svärdet OK (2) och varning (4).  <br> -Standard-standardvärdet. Matchar alla hälso tillstånd. Värdet är noll.  <br> -Inget – filter som inte matchar något värde för hälso tillstånd. Används för att returnera inga resultat för en specifik samling av tillstånd. Värdet är 1.  <br> – OK-filter som matchar inmatade hälso tillstånds värden OK. Värdet är 2.  <br> -Varnings filter som matchar inmatade värde varningar för hälso tillstånd. Värdet är 4.  <br> -Fel-filter som matchar InInformationen med hälso tillstånds värde fel. Värdet är 8.  <br> – Alla – filter som matchar indatamängden med ett värde för hälso tillstånd. Värdet är 65535. |
| --Exkludera-hälso statistik | Anger om hälso statistik ska returneras som en del av frågeresultatet. Falskt som standard. Statistiken visar antalet underordnade entiteter i hälso tillståndet OK, varning och fel. |
| --tjänster-hälso tillstånd – filter | Tillåter filtrering av tjänsternas hälso tillstånds objekt som returneras i resultatet av tjänstens hälso tillstånds fråga baserat på deras hälso tillstånd. De möjliga värdena för den här parametern är heltals värde för något av följande hälso tillstånd. Endast tjänster som matchar filtret returneras. Alla tjänster används för att utvärdera det sammanlagda hälso tillståndet. Om inget anges returneras alla poster. Tillstånds värden är flaggning-baserad uppräkning, så värdet kan vara en kombination av dessa värden, erhållna med hjälp av bitvis-eller-operator. Om det tillhandahållna värdet till exempel är 6, returneras hälso tillståndet för tjänster med hälso tillstånds värdet OK (2) och varning (4).  <br> -Standard-standardvärdet. Matchar alla hälso tillstånd. Värdet är noll.  <br> -Inget – filter som inte matchar något värde för hälso tillstånd. Används för att returnera inga resultat för en specifik samling av tillstånd. Värdet är 1.  <br> – OK-filter som matchar inmatade hälso tillstånds värden OK. Värdet är 2.  <br> -Varnings filter som matchar inmatade värde varningar för hälso tillstånd. Värdet är 4.  <br> -Fel-filter som matchar InInformationen med hälso tillstånds värde fel. Värdet är 8.  <br> – Alla – filter som matchar indatamängden med ett värde för hälso tillstånd. Värdet är 65535. |
| --timeout-t | Tids gränsen för servern för att utföra åtgärden på några sekunder. Denna timeout anger den tids period som klienten vill vänta tills den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard \: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-application-info"></a>information om sfctl-program
Hämtar information om ett Service Fabric-program.

Returnerar informationen om programmet som har skapats eller håller på att skapas i Service Fabric klustret och vars namn matchar det som angavs som parameter. Svaret innehåller namn, typ, status, parametrar och annan information om programmet.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --program-ID [obligatoriskt] | Programmets identitet. Detta är vanligt vis det fullständiga namnet på programmet utan URI-schemat "Fabric \: ". Från och med version 6,0 avgränsas hierarkiska namn med " \~ "-symbolen. Om program namnet till exempel är "Fabric \: /MyApp/APP1" skulle program identiteten vara "MyApp \~ APP1" i 6.0 + och "MyApp/APP1" i tidigare versioner. |
| --exclude-Application-Parameters | Den flagga som anger om program parametrar ska uteslutas från resultatet. |
| --timeout-t | Tids gränsen för servern för att utföra åtgärden på några sekunder. Denna timeout anger den tids period som klienten vill vänta tills den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard \: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-application-list"></a>lista över sfctl program
Hämtar listan med program som skapats i det Service Fabric kluster som matchar de angivna filtren.

Hämtar information om de program som har skapats eller håller på att skapas i Service Fabric klustret och matchar de angivna filtren. Svaret innehåller namn, typ, status, parametrar och annan information om programmet. Om programmen inte får plats på en sida returneras en resultat sida och en fortsättnings-token som kan användas för att hämta nästa sida. Filter ApplicationTypeName och ApplicationDefinitionKindFilter kan inte anges samtidigt.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --program-definition-Natura-filter | Används för att filtrera på ApplicationDefinitionKind, vilket är mekanismen som används för att definiera ett Service Fabric-program.  <br> – Standardvärde – standardvärdet som utför samma funktion som att välja "alla". Värdet är 0.  <br> – Alla – filter som matchar inmatade värden med ett ApplicationDefinitionKind-värde. Värdet är 65535.  <br> -ServiceFabricApplicationDescription – filter som matchar InInformationen med ApplicationDefinitionKind-värdet ServiceFabricApplicationDescription. Värdet är 1.  <br> – Skriv-filter som matchar InInformationen med ApplicationDefinitionKind-värde-Skriv. Värdet är 2. |
| --program-typ-namn | Det program typs namn som används för att filtrera de program som ska frågas efter. Det här värdet får inte innehålla program typ versionen. |
| --fortsättnings-token | Parametern för fortsatt token används för att hämta nästa uppsättning resultat. En fortsättnings-token med ett värde som inte är tom inkluderas i svaret på API: et när resultatet från systemet inte passar i ett enda svar. När det här värdet skickas till nästa API-anrop returnerar API nästa uppsättning resultat. Om det inte finns några ytterligare resultat innehåller inte fortsättnings-token ett värde. Värdet för den här parametern får inte vara URL-kodat. |
| --exclude-Application-Parameters | Den flagga som anger om program parametrar ska uteslutas från resultatet. |
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

## <a name="sfctl-application-load"></a>sfctl program inläsning
Läser in information om ett Service Fabric-program.

Returnerar inläsnings information om programmet som skapades eller håller på att skapas i Service Fabric klustret och vars namn matchar det som angavs som parameter. Svaret innehåller namn, lägsta noder, maximalt antal noder, antalet noder som programmet använder för närvarande och information om program inläsnings statistik för programmet.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --program-ID [obligatoriskt] | Programmets identitet. Detta är vanligt vis det fullständiga namnet på programmet utan URI-schemat "Fabric \: ". Från och med version 6,0 avgränsas hierarkiska namn med " \~ "-symbolen. Om program namnet till exempel är "Fabric \: /MyApp/APP1" skulle program identiteten vara "MyApp \~ APP1" i 6.0 + och "MyApp/APP1" i tidigare versioner. |
| --timeout-t | Tids gränsen för servern för att utföra åtgärden på några sekunder. Denna timeout anger den tids period som klienten vill vänta tills den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard \: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-application-manifest"></a>sfctl program manifest
Hämtar manifestet som beskriver en program typ.

Svaret innehåller program Manifestets XML som en sträng.

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

## <a name="sfctl-application-provision"></a>sfctl applikations etablering
Etablerar eller registrerar en Service Fabric program typ med klustret med hjälp av sfpkg-paketet i den externa lagringen eller med hjälp av programpaketet i avbildnings arkivet.

Etablerar en Service Fabric program typ med klustret. Etableringen krävs innan det går att instansiera nya program. Etablerings åtgärden kan utföras antingen på det programpaket som anges av relativePathInImageStore, eller med hjälp av URI: n för den externa. sfpkg. Om inte--external-provision har angetts kommer det här kommandot att förväntas tillhandahålla avbildnings lagring.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --program-Package-Download-URI | Sökvägen till programpaketet '. sfpkg ' från var programpaketet kan hämtas med hjälp av HTTP-eller HTTPS-protokoll. <br><br> Endast för etablerings typ extern lagring. Programpaketet kan lagras i en extern lagrings plats som tillhandahåller åtgärden Hämta för att hämta filen. Protokoll som stöds är HTTP och HTTPS och sökvägen måste tillåta Läs åtkomst. |
| --program-typ-build-Path | Endast för etablerings avbildnings lagring. Den relativa sökvägen för programpaketet i avbildnings arkivet som angavs under föregående uppladdning. |
| --program-typ-namn | Endast för etablerings typ extern lagring. Namnet på program typen representerar namnet på den program typ som finns i applikations manifestet. |
| --program-Type-version | Endast för etablerings typ extern lagring. Program typ versionen representerar versionen av den program typ som finns i applikations manifestet. |
| --extern-provision | Platsen där programpaket kan registreras eller tillhandahållas. Anger att etableringen avser ett programpaket som tidigare har överförts till en extern lagrings plats. Programpaketet slutar med fil namns tillägget *. sfpkg. |
| --No-wait | Anger om etableringen ska ske asynkront eller inte. <br><br> När värdet är true, returnerar etablerings åtgärden när begäran accepteras av systemet och etablerings åtgärden fortsätter utan att någon tids gräns överskrids. Standardvärdet är false. För stora programpaket rekommenderar vi att du anger värdet till sant. |
| --timeout-t | Standard \: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-application-report-health"></a>sfctl program rapport – hälsa
Skickar en hälso rapport i Service Fabric programmet.

Rapporterar hälso tillstånd för det angivna Service Fabric programmet. Rapporten måste innehålla information om källan till hälso rapporten och egenskapen som den rapporteras om. Rapporten skickas till ett Service Fabric Gateway-program, som vidarebefordrar till hälso lagret. Rapporten kan godkännas av gatewayen, men avvisas av hälso lagret efter extra verifiering. Hälso lagret kan till exempel avvisa rapporten på grund av en ogiltig parameter, t. ex. ett inaktuellt ordnings nummer. Om du vill se om rapporten användes i hälso lagret, hämtar du program hälsan och kontrollerar att rapporten visas.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --program-ID [obligatoriskt] | Programmets identitet. <br><br> Detta är vanligt vis det fullständiga namnet på programmet utan URI-schemat "Fabric \: ". Från och med version 6,0 avgränsas hierarkiska namn med- \~ symbolen. Om program namnet exempelvis är Fabric \: /MyApp/APP1, skulle program identiteten vara ' MyApp \~ APP1 ' i 6.0 + och ' MyApp/APP1 ' i tidigare versioner. |
| --Health-Property [required] | Hälso informationens egenskaper. <br><br> En entitet kan ha hälso rapporter för olika egenskaper. Egenskapen är en sträng och inte en fast uppräkning som tillåter rapportörens flexibilitet att kategorisera det tillstånds villkor som utlöser rapporten. Till exempel kan en rapportör med SourceId "LocalWatchdog" övervaka statusen för den tillgängliga disken på en nod, så att den kan rapportera egenskapen "AvailableDisk" på noden. Samma rapportör kan övervaka nodens anslutning, så att den kan rapportera en egenskap "anslutning" på samma nod. I hälso lagret behandlas dessa rapporter som separata hälso händelser för den angivna noden. Tillsammans med värdet för SourceId kan egenskapen unikt identifiera hälso informationen. |
| --hälso tillstånd [krävs] | Möjliga värden är \: "ogiltig", "OK", "varning", "Error", "okänd". |
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

## <a name="sfctl-application-type"></a>sfctl program typ
Hämtar listan över program typer i Service Fabric klustret som matchar det angivna namnet.

Returnerar information om de program typer som har tillhandahållits eller håller på att tillhandahållas i Service Fabric klustret. De här resultaten är av program typer vars namn matchar exakt det som anges som parameter och som uppfyller de angivna frågeparametrarna. Alla versioner av program typen som matchar program typ namnet returneras, och varje version returneras som en program typ. Svaret innehåller namn, version, status och annan information om program typen. Det här är en växlings fråga, vilket innebär att om inte alla program typer får plats på en sida, returneras en resultat sida samt en fortsättnings-token som kan användas för att hämta nästa sida. Om det till exempel finns 10 program typer men en sida bara passar de första tre program typerna, eller om max resultatet är 3, returneras tre. Hämta efterföljande sidor genom att använda den returnerade fortsättnings-token i nästa fråga för att få åtkomst till resten av resultaten. En tom fortsättnings-token returneras om det inte finns några efterföljande sidor.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --program-typ-namn [obligatoriskt] | Namnet på program typen. |
| --program-Type-version | Versionen av program typen. |
| --fortsättnings-token | Parametern för fortsatt token används för att hämta nästa uppsättning resultat. En fortsättnings-token med ett värde som inte är tom inkluderas i svaret på API: et när resultatet från systemet inte passar i ett enda svar. När det här värdet skickas till nästa API-anrop returnerar API nästa uppsättning resultat. Om det inte finns några ytterligare resultat innehåller inte fortsättnings-token ett värde. Värdet för den här parametern får inte vara URL-kodat. |
| --exclude-Application-Parameters | Den flagga som anger om program parametrar ska uteslutas från resultatet. |
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

## <a name="sfctl-application-type-list"></a>sfctl program typ – lista
Hämtar listan över program typer i Service Fabric klustret.

Returnerar information om de program typer som har tillhandahållits eller håller på att tillhandahållas i Service Fabric klustret. Varje version av en program typ returneras som en program typ. Svaret innehåller namn, version, status och annan information om program typen. Det här är en växlings fråga, vilket innebär att om inte alla program typer får plats på en sida, returneras en resultat sida samt en fortsättnings-token som kan användas för att hämta nästa sida. Om det till exempel finns 10 program typer men en sida bara passar de första tre program typerna, eller om max resultatet är 3, returneras tre. Hämta efterföljande sidor genom att använda den returnerade fortsättnings-token i nästa fråga för att få åtkomst till resten av resultaten. En tom fortsättnings-token returneras om det inte finns några efterföljande sidor.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --program-typ-definition-kind-filter | Används för att filtrera på ApplicationTypeDefinitionKind, vilket är mekanismen som används för att definiera en Service Fabric program typ.  <br> – Standardvärde – standardvärdet som utför samma funktion som att välja "alla". Värdet är 0.  <br> – Alla – filter som matchar inmatade värden med ett ApplicationTypeDefinitionKind-värde. Värdet är 65535.  <br> -ServiceFabricApplicationPackage – filter som matchar InInformationen med ApplicationTypeDefinitionKind-värdet ServiceFabricApplicationPackage. Värdet är 1.  <br> – Skriv-filter som matchar InInformationen med ApplicationTypeDefinitionKind-värde-Skriv. Värdet är 2. |
| --fortsättnings-token | Parametern för fortsatt token används för att hämta nästa uppsättning resultat. En fortsättnings-token med ett värde som inte är tom inkluderas i svaret på API: et när resultatet från systemet inte passar i ett enda svar. När det här värdet skickas till nästa API-anrop returnerar API nästa uppsättning resultat. Om det inte finns några ytterligare resultat innehåller inte fortsättnings-token ett värde. Värdet för den här parametern får inte vara URL-kodat. |
| --exclude-Application-Parameters | Den flagga som anger om program parametrar ska uteslutas från resultatet. |
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

## <a name="sfctl-application-unprovision"></a>avetableringen av sfctl-program
Tar bort eller avregistrerar en Service Fabric program typ från klustret.

Den här åtgärden kan bara utföras om alla program instanser av program typen har tagits bort. När program typen har avregistrerats kan inga nya program instanser skapas för den aktuella program typen.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --program-typ-namn [obligatoriskt] | Namnet på program typen. |
| --program-Type-version [required] | Versionen av program typen som definieras i applikations manifestet. |
| --async-parameter | Flaggan som anger om en avetablering ska ske asynkront eller inte. När värdet är true, returnerar avetablerings åtgärden när begäran accepteras av systemet, och avetablerings åtgärden fortsätter utan tids gräns. Standardvärdet är false. Vi rekommenderar dock att du ställer in det på sant för stora programpaket som har tillhandahållits. |
| --timeout-t | Tids gränsen för servern för att utföra åtgärden på några sekunder. Denna timeout anger den tids period som klienten vill vänta tills den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard \: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-application-upgrade"></a>sfctl program uppgradering
Startar uppgraderingen av ett program i Service Fabric klustret.

Verifierar de angivna program uppgraderings parametrarna och börjar uppgradera programmet om parametrarna är giltiga. Observera att uppgraderings beskrivningen ersätter den befintliga program beskrivningen. Det innebär att om parametrarna inte anges kommer de befintliga parametrarna i programmen att skrivas över med listan med tomma parametrar. Detta leder till att programmet använder standardvärdet för parametrarna från applikations manifestet.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --program-ID [obligatoriskt] | Programmets identitet. <br><br> Detta är vanligt vis det fullständiga namnet på programmet utan URI-schemat "Fabric \: ". Från och med version 6,0 avgränsas hierarkiska namn med " \~ "-symbolen. Om program namnet till exempel är "Fabric \: /MyApp/APP1" skulle program identiteten vara "MyApp \~ APP1" i 6.0 + och "MyApp/APP1" i tidigare versioner. |
| --program version [krävs] | Mål program typ versionen (som finns i applikations manifestet) för program uppgraderingen. |
| --parametrar [required] | En JSON-kodad lista över program parameter åsidosättningar som ska tillämpas vid uppgradering av programmet. |
| --standard-service-Health-Policy | JSON-kodad specifikation av den hälso princip som används som standard för att utvärdera hälso tillståndet för en tjänst typ. |
| --Failure-åtgärd | Åtgärden som ska utföras när en övervakad uppgradering påträffar övervaknings principen eller hälso policy överträdelser. |
| --Framtvinga omstart | Framtvinga omstart av processer under uppgraderingen även när kod versionen inte har ändrats. |
| --hälso kontroll-retry-timeout | Hur lång tid det tar mellan försök att utföra hälso kontroller om programmet eller klustret inte är felfritt.  Standard \: PT0H10M0S. |
| --hälso kontroll – stabil varaktighet | Hur lång tid programmet eller klustret måste vara felfritt innan uppgraderingen fortsätter till nästa uppgraderings domän.  Standard \: PT0H2M0S. <br><br> Den tolkas först som en sträng som representerar en varaktighet på ISO 8601. Om detta Miss lyckas tolkas det som ett tal som representerar det totala antalet millisekunder. |
| --hälso kontroll-vänte tid | Vänte tiden när en uppgraderings domän har slutförts innan hälso kontrollerna påbörjas.  Standardvärdet är \: 0. |
| --Max-ej felfri-appar | Högsta tillåtna procent andel ej hälsodistribuerade program. Representeras som ett tal mellan 0 och 100. |
| --läge | Det läge som används för att övervaka hälsan vid en rullande uppgradering.  Standard \: UnmonitoredAuto. |
| --replik-set-kontrol lera timeout | Maximal tid det tar att blockera bearbetningen av en uppgraderings domän och förhindra tillgänglighet när det uppstår oväntade problem. Mätt i sekunder. |
| --tjänst-hälso princip | JSON-kodad karta med tjänst typens hälso princip per tjänst typs namn. Kartan är tom som standard. |
| --timeout-t | Standard \: 60. |
| --uppgradering-Domain-timeout | Hur lång tid varje uppgraderings domän måste vara slutförd innan FailureAction körs.  Standard \: p10675199dt02h48m-05.4775807 s. <br><br> Den tolkas först som en sträng som representerar en varaktighet på ISO 8601. Om detta Miss lyckas tolkas det som ett tal som representerar det totala antalet millisekunder. |
| --uppgraderings-timeout | Den tid som den övergripande uppgraderingen måste slutföras innan FailureAction körs.  Standard \: p10675199dt02h48m-05.4775807 s. <br><br> Den tolkas först som en sträng som representerar en varaktighet på ISO 8601. Om detta Miss lyckas tolkas det som ett tal som representerar det totala antalet millisekunder. |
| --Varning-as-Error | Anger om varningar behandlas med samma allvarlighets grad som fel. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-application-upgrade-resume"></a>sfctl program uppgradering – återuppta
Återupptar uppgraderingen av ett program i Service Fabric klustret.

Återupptar en oövervakad manuell Service Fabric program uppgradering. Service Fabric uppgraderar en uppgraderings domän i taget. När Service Fabric har slutfört en uppgraderings domän för oövervakade manuella uppgraderingar väntar det på att du ska anropa detta API innan du fortsätter till nästa uppgraderings domän.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --program-ID [obligatoriskt] | Programmets identitet. Detta är vanligt vis det fullständiga namnet på programmet utan URI-schemat "Fabric \: ". Från och med version 6,0 avgränsas hierarkiska namn med " \~ "-symbolen. Om program namnet till exempel är "Fabric \: /MyApp/APP1" skulle program identiteten vara "MyApp \~ APP1" i 6.0 + och "MyApp/APP1" i tidigare versioner. |
| --uppgradering-domän namn [obligatoriskt] | Namnet på den uppgraderings domän där uppgraderingen ska återupptas. |
| --timeout-t | Tids gränsen för servern för att utföra åtgärden på några sekunder. Denna timeout anger den tids period som klienten vill vänta tills den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard \: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-application-upgrade-rollback"></a>sfctl program uppgradering-återställning
Börjar återställa den pågående uppgraderingen av ett program i Service Fabric klustret.

Börjar återställa den aktuella program uppgraderingen till den tidigare versionen. Detta API kan bara användas för att återställa den aktuella pågående uppgraderingen som går framåt till den nya versionen. Om programmet inte håller på att uppgraderas använder du StartApplicationUpgrade-API för att uppgradera det till önskad version, inklusive att återställa till en tidigare version.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --program-ID [obligatoriskt] | Programmets identitet. Detta är vanligt vis det fullständiga namnet på programmet utan URI-schemat "Fabric \: ". Från och med version 6,0 avgränsas hierarkiska namn med " \~ "-symbolen. Om program namnet till exempel är "Fabric \: /MyApp/APP1" skulle program identiteten vara "MyApp \~ APP1" i 6.0 + och "MyApp/APP1" i tidigare versioner. |
| --timeout-t | Tids gränsen för servern för att utföra åtgärden på några sekunder. Denna timeout anger den tids period som klienten vill vänta tills den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard \: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-application-upgrade-status"></a>sfctl program uppgradering-status
Hämtar information om den senaste uppgraderingen som utförs i det här programmet.

Returnerar information om tillståndet för den senaste program uppgraderingen och information om hur du felsöker program hälso problem.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --program-ID [obligatoriskt] | Programmets identitet. Detta är vanligt vis det fullständiga namnet på programmet utan URI-schemat "Fabric \: ". Från och med version 6,0 avgränsas hierarkiska namn med " \~ "-symbolen. Om program namnet till exempel är "Fabric \: /MyApp/APP1" skulle program identiteten vara "MyApp \~ APP1" i 6.0 + och "MyApp/APP1" i tidigare versioner. |
| --timeout-t | Tids gränsen för servern för att utföra åtgärden på några sekunder. Denna timeout anger den tids period som klienten vill vänta tills den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard \: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-application-upload"></a>sfctl program uppladdning
Kopiera ett Service Fabric-programpaket till avbildnings arkivet.

Du kan också Visa uppladdnings förloppet för varje fil i paketet. Överförings förloppet skickas till `stderr` .

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --sökväg [obligatoriskt] | Sökväg till lokalt programpaket. |
| --Komprimera | Gäller endast för Service Fabric programpaket. Skapa en ny mapp som innehåller det komprimerade programpaketet till standard platsen eller till den plats som anges av parametern Compressed-location och ladda sedan upp den nya mappen. <br><br> Om det redan finns en komprimerad fil som genererats av sfctl kommer den att skrivas över om den här flaggan har angetts. Ett fel returneras om katalogen inte är ett programpaket. Om det redan är ett komprimerat programpaket kopieras mappen över som är. Som standard tas det nyligen skapade komprimerade programpaketet bort efter en lyckad uppladdning. Om överföringen inte lyckas rensar du det komprimerade paketet manuellt efter behov. Borttagningen tar inte bort tomma kataloger som kan ha skapats om parametern Compressed location refererar till obefintliga kataloger. |
| --komprimerad plats | Platsen där det komprimerade programpaketet ska läggas till. <br><br> Om ingen plats anges placeras det komprimerade paketet under en nyligen skapad mapp som kallas sfctl_compressed_temp under den överordnade katalogen som anges i argumentet Path. Om argumentet Path exempelvis har värdet C \: /FolderA/AppPkg, läggs det komprimerade paketet till i C \: /Foldera/sfctl_compressed_temp/apppkg. |
| --avbildnings Arkiv-sträng | Mål avbildnings lager att ladda upp programpaketet till.  Standard \: infrastruktur \: avbildnings arkiv. <br><br> Om du vill överföra till en fil Sök väg startar du den här parametern med "File \: ". Annars bör värdet vara anslutnings strängen för bild arkivet, till exempel standardvärdet. |
| --Behåll-komprimerad | Huruvida det genererade komprimerade paketet ska behållas vid slutförd uppladdning. <br><br> Om den inte anges kommer de komprimerade Appaketet att tas bort vid lyckad slut för ande. Om överföringen inte lyckades lagras programpaketet alltid i utdatakatalogen för omöverföring. |
| --show-Progress | Visa fil överförings förlopp för stora paket. |
| --timeout-t | Den totala tids gränsen i sekunder. Det går inte att överföra och returnera fel efter att varaktigheten för uppladdningen har passerat. Denna timeout gäller för hela programpaketet och enskilda fil-timeoutar är samma som den återstående tids gränsen. Timeout inkluderar inte den tid som krävs för att komprimera programpaketet.  Standard \: 300. |

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
