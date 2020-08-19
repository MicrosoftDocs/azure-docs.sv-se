---
title: Förstå Azure App konfigurations nyckel-värde lager
description: Förstå nyckel värdes lagring i Azure App konfiguration, som lagrar konfigurations data som nyckel värden. Nyckel värden är en representation av program inställningar.
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/19/2020
ms.openlocfilehash: b1998532c3d9e4272d91280d57d9ea2f6e7a262c
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88586400"
---
# <a name="keys-and-values"></a>Nycklar och värden

Azure App konfiguration lagrar konfigurations data som nyckel värden. Nyckel värden är en enkel och flexibel representation av program inställningar som används av utvecklare.

## <a name="keys"></a>Nycklar

Nycklar fungerar som identifierare för nyckel värden och används för att lagra och hämta motsvarande värden. Det är en vanlig metod för att ordna nycklar i ett hierarkiskt namn område med hjälp av en tecken avgränsare, till exempel `/` eller `:` . Använd en konvention som passar bäst för ditt program. App Configuration behandlar nycklar som en helhet. Det går inte att parsa nycklar för att ta reda på hur deras namn är strukturerade eller tillämpa någon regel på dem.

Här är ett exempel på nyckel namn som är strukturerade i en hierarki baserat på komponent tjänster:

```aspx
    AppName:Service1:ApiEndpoint
    AppName:Service2:ApiEndpoint
```

Användningen av konfigurations data i Application Framework kan diktera specifika namngivnings scheman för nyckel värden. Till exempel definierar Javas moln ramverk `Environment` resurser som tillhandahåller inställningar för ett våren-program.  Dessa parametrar är parameterstyrda av variabler som innehåller *program namn* och *profil*. Nycklar för vår moln-relaterade konfigurations data börjar normalt med dessa två element, avgränsade med en avgränsare.

Nycklar som lagras i App Configuration är skiftlägeskänsliga, Unicode-baserade strängar. Nycklarna *APP1* och *APP1* är distinkta i ett konfigurations Arkiv för appar. Tänk på detta när du använder konfigurations inställningar i ett program eftersom vissa ramverk hanterar konfigurations nycklar SKIFT läges okänsligt. Vi rekommenderar inte att du använder SKIFT läge för att särskilja nycklar.

Du kan använda valfritt Unicode-tecken i nyckel namn förutom för `%` . Ett nyckel namn får inte vara `.` eller något av dem `..` . Det finns en sammanlagd storleks gräns på 10 KB på ett nyckel värde. Den här gränsen omfattar alla tecken i nyckeln, dess värde och alla tillhör ande valfria attribut. Inom den här gränsen kan du ha många hierarkiska nivåer för nycklar.

### <a name="design-key-namespaces"></a>Design nyckel namn rymder

Det finns två generella metoder för namngivning av nycklar som används för konfigurationsdata: platt eller hierarkisk. Dessa metoder liknar ett program användnings synpunkt, men hierarkiska namn erbjuder ett antal fördelar:

* Enklare att läsa. Avgränsare i ett hierarkiskt nyckel namn fungerar som blank steg i en mening. De ger också naturliga brytningar mellan ord.
* Enklare att hantera. En nyckelnamnshierarki representerar logiska grupper med konfigurationsdata.
* Enklare att använda. Det är enklare att skriva en fråga som mönstermatchar nycklar i en hierarkisk struktur och bara hämtar en del av konfigurationsdata. Dessutom har många nyare programmerings ramverk inbyggda stöd för hierarkiska konfigurations data, så att ditt program kan använda specifika uppsättningar konfiguration.

Du kan organisera nycklar i App Configuration hierarkiskt på många sätt. Tänk på sådana nycklar som [URI: er](https://en.wikipedia.org/wiki/Uniform_Resource_Identifier). Varje hierarkisk nyckel är en resurs *Sök väg* bestående av en eller flera komponenter som kopplas samman med avgränsare. Välj vilket tecken som ska användas som avgränsare baserat på vad programmet, programmeringsspråket eller ramverket behöver. Använd flera avgränsare för olika nycklar i app-konfigurationen.

### <a name="label-keys"></a>Etikett nycklar

Som tillval kan nyckelvärden i App Configuration ha attributet label (etikett). Etiketter används för att skilja mellan nyckelvärden med samma nyckel. En viktig *APP1* med etiketter *A* och *B* formar två separata nycklar i ett konfigurations lager för appar. Som standard har ett nyckel värde ingen etikett. Om du explicit vill referera till ett nyckel värde utan en etikett använder du `\0` (URL kodad som `%00` ).

Label är ett bekvämt sätt att skapa varianter av en nyckel. En vanlig användning av etiketter är att ange flera miljöer för samma nyckel:

```
    Key = AppName:DbEndpoint & Label = Test
    Key = AppName:DbEndpoint & Label = Staging
    Key = AppName:DbEndpoint & Label = Production
```

### <a name="version-key-values"></a>Versions nyckel-värden

Använd etiketter som ett sätt att skapa flera versioner av ett nyckel värde. Exempelvis kan du ange ett programversionsnummer eller ett Git-inchecknings-ID i etiketter för att identifiera nyckelvärden som är associerade med en viss programvaruversion.

> [!NOTE]
> Om du letar efter ändrings versioner behåller app-konfigurationen alla ändringar av ett nyckel värde som har inträffat tidigare under en viss tids period automatiskt. Mer information finns i [ögonblicks bilder](./concept-point-time-snapshot.md) av tidpunkt.

### <a name="query-key-values"></a>Fråga efter nyckel värden

Varje nyckelvärde identifieras unikt genom sin nyckel plus en etikett som kan vara `\0`. Du frågar efter ett konfigurations lager för nyckel värden genom att ange ett mönster. Konfigurations arkivet för appar returnerar alla nyckel värden som matchar mönstret, inklusive motsvarande värden och attribut. Använd följande nyckel mönster i REST API anrop till app-konfiguration:

| Nyckel | Description |
|---|---|
| `key` utelämnas eller `key=*` | Matchar alla nycklar |
| `key=abc` | Matchar nyckel namn **ABC** exakt |
| `key=abc*` | Matchar nyckelnamn som börjar med **abc** |
| `key=abc,xyz` | Matchar nyckel namn **ABC** eller **XYZ**. Begränsad till fem CSV: er |

Du kan också inkludera följande etikett mönster:

| Etikett | Description |
|---|---|
| `label` utelämnas eller `label=*` | Matchar alla etiketter, som innehåller `\0` |
| `label=%00` | Matchar `\0` etikett |
| `label=1.0.0` | Matchar etiketten **1.0.0** exakt |
| `label=1.0.*` | Matchar etiketter som börjar med **1.0.** |
| `label=%00,1.0.0` | Matchar etiketter `\0` eller **1.0.0**, begränsade till fem CSV: er |

> [!NOTE]
> `*`, `,` och `\` är reserverade tecken i frågor. Om ett reserverat tecken används i dina nyckel namn eller etiketter måste du kringgå det genom att använda `\{Reserved Character}` i frågor.

## <a name="values"></a>Värden

Värden som tilldelas till nycklar är också Unicode-strängar. Du kan använda alla Unicode-tecken för värden.

### <a name="use-content-type"></a>Använd innehålls typ
Varje nyckel värde i app-konfigurationen har ett attribut för innehålls typ. Du kan också använda det här attributet för att lagra information om typen av värde i ett nyckel värde som hjälper ditt program att bearbeta det korrekt. Du kan använda valfritt format för innehålls typen. Konfiguration av appar använder [medie typer]( https://www.iana.org/assignments/media-types/media-types.xhtml) (även kallade MIME-typer) för inbyggda data typer, till exempel funktions flaggor, Key Vault referenser och JSON-nyckel värden.

## <a name="next-steps"></a>Nästa steg

* [Tidpunktsbaserad ögonblicksbild](./concept-point-time-snapshot.md)
* [Funktionshantering](./concept-feature-management.md)
* [Händelsehantering](./concept-app-configuration-event.md)
