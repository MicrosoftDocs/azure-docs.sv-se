---
title: Samla in anpassade loggar med Log Analytics-agenten i Azure Monitor
description: Azure Monitor kan samla in händelser från textfiler på både Windows-och Linux-datorer.  Den här artikeln beskriver hur du definierar en ny anpassad logg och information om de poster som de skapar i Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/21/2020
ms.openlocfilehash: 73496e350a5e40a3945343271b76c6d883991b62
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100623452"
---
# <a name="collect-custom-logs-with-log-analytics-agent-in-azure-monitor"></a>Samla in anpassade loggar med Log Analytics-agenten i Azure Monitor

Data källan för anpassade loggar för Log Analytics agenten i Azure Monitor gör att du kan samla in händelser från textfiler på både Windows-och Linux-datorer. Många program loggar information till textfiler i stället för standard loggnings tjänster som Windows-händelseloggen eller syslog. När du har samlat in kan du antingen parsa data i enskilda fält i dina frågor eller extrahera data under samlingen till enskilda fält.

> [!IMPORTANT]
> Den här artikeln beskriver hur du samlar in anpassade loggar med [Log Analytics agent](../platform/log-analytics-agent.md) som är en av de agenter som används av Azure Monitor. Andra agenter samlar in olika data och konfigureras på olika sätt. Se [Översikt över Azure Monitor agenter](../agents/agents-overview.md) för en lista över tillgängliga agenter och de data som de kan samla in.

![Anpassad logg samling](media/data-sources-custom-logs/overview.png)

De loggfiler som ska samlas in måste matcha följande kriterier.

- Loggen måste antingen ha en enda post per rad eller använda en tidstämpel som matchar något av följande format i början av varje post.

    ÅÅÅÅ-MM-DD HH: MM: SS<br>M/D/ÅÅÅÅ HH: MM: SS FM/EM<br>Mån DD, ÅÅÅÅ HH: MM: SS<br />yyMMdd HH: mm: SS<br />ddMMyy HH: mm: SS<br />MMM d hh: mm: SS<br />DD/MMM/åååå: HH: mm: SS ZZZ<br />åååå-MM-ddTHH: mm: SSL

- Logg filen får inte tillåta cirkulär loggning eller logg rotation, där filen skrivs över med nya poster.
- Logg filen måste använda ASCII-eller UTF-8-kodning.  Andra format, till exempel UTF-16, stöds inte.
- För Linux stöds inte tids zons konvertering för tidsstämplar i loggarna.

>[!NOTE]
> Om det finns dubbla poster i logg filen, Azure Monitor samla in dem. Frågeresultatet kommer dock att vara inkonsekvent där filter resultaten visar fler händelser än antalet resultat. Det är viktigt att du validerar loggen för att avgöra om programmet som skapar den orsakar detta beteende och kan åtgärda det om möjligt innan du skapar den anpassade logg samlings definitionen.  
>

>[!NOTE]
> En Log Analytics arbets yta har stöd för följande gränser:
> 
> * Endast 500 anpassade loggar kan skapas.
> * En tabell har endast stöd för upp till 500 kolumner. 
> * Det maximala antalet tecken för kolumnnamnet är 500. 
>

>[!IMPORTANT]
>För anpassad logg samling krävs att programmet skriver logg filen tömmer logg innehållet till disken med jämna mellanrum. Detta beror på att den anpassade logg samlingen förlitar sig på fil Systems ändrings meddelanden för logg filen som spåras.

## <a name="defining-a-custom-log"></a>Definiera en anpassad logg
Använd följande procedur för att definiera en anpassad loggfil.  Gå till slutet av den här artikeln för en genom gång av ett exempel på hur du lägger till en anpassad logg.

### <a name="step-1-open-the-custom-log-wizard"></a>Steg 1. Öppna guiden Anpassad logg
Guiden Anpassad logg körs i Azure Portal och gör att du kan definiera en ny anpassad logg att samla in.

1. I Azure Portal väljer du **Log Analytics arbets ytor** > din arbets yta > **Avancerade inställningar**.
2. Klicka på   >  **anpassade data loggar**.
3. Som standard flyttas alla konfigurations ändringar automatiskt till alla agenter. För Linux-agenter skickas en konfigurations fil till den insamlade data insamlaren.
4. Klicka på **Lägg till +** för att öppna guiden Anpassad logg.

### <a name="step-2-upload-and-parse-a-sample-log"></a>Steg 2. Ladda upp och parsa en exempel logg
Du börjar med att ladda upp ett exempel på den anpassade loggen.  Guiden kommer att tolka och Visa poster i den här filen som du kan validera.  Azure Monitor använder den avgränsare som du anger för att identifiera varje post.

**Ny rad** är standard avgränsaren och används för loggfiler som har en enda post per rad.  Om raden börjar med ett datum och en tid i något av de tillgängliga formaten kan du ange en avgränsare för **tidsstämpel** som stöder poster som omfattar mer än en rad.

Om en tidsbegränsare för tidsstämpel används fylls egenskapen TimeGenerated för varje post som lagras i Azure Monitor med den datum/tid som angetts för posten i logg filen.  Om en ny rad avgränsare används fylls TimeGenerated med datum och tid som Azure Monitor samlade in posten.

1. Klicka på **Bläddra** och bläddra till en exempel fil.  Observera att den här knappen kan vara märkt **Välj fil** i vissa webbläsare.
2. Klicka på **Nästa**.
3. Guiden Anpassad logg överför filen och listar de poster som den identifierar.
4. Ändra avgränsaren som används för att identifiera en ny post och välj den avgränsare som bäst identifierar posterna i logg filen.
5. Klicka på **Nästa**.

### <a name="step-3-add-log-collection-paths"></a>Steg 3. Lägg till logg samlings Sök vägar
Du måste definiera en eller flera sökvägar på agenten där den kan hitta den anpassade loggen.  Du kan antingen ange en speciell sökväg och ett namn för logg filen, eller så kan du ange en sökväg med ett jokertecken för namnet. Detta stöder program som skapar en ny fil varje dag eller när en fil når en viss storlek. Du kan också ange flera sökvägar för en enskild loggfil.

Ett program kan till exempel skapa en loggfil varje dag med datumet som ingår i namnet som i log20100316.txt. Ett mönster för en sådan logg kan vara *log \* . txt* som gäller för alla loggfiler som följer programmets namngivnings schema.

Följande tabell innehåller exempel på giltiga mönster för att ange olika loggfiler.

| Description | Sökväg |
|:--- |:--- |
| Alla filer i *: c:\Logs* med tillägget. txt i Windows-agenten |: C:\Logs \\ \* . txt |
| Alla filer i *: c:\Logs* med ett namn som börjar med log och tillägget. txt i Windows-agenten |C:\Logs\log \* . txt |
| Alla filer i */var/log/audit* med tillägget. txt i Linux-agenten |/var/log/Audit/*. txt |
| Alla filer i */var/log/audit* med ett namn som börjar med log och tillägget. txt i Linux-agenten |/var/log/audit/log \* . txt |

1. Välj Windows eller Linux för att ange vilket Sök vägs format du vill lägga till.
2. Skriv sökvägen och klicka på **+** knappen.
3. Upprepa processen för eventuella ytterligare sökvägar.

### <a name="step-4-provide-a-name-and-description-for-the-log"></a>Steg 4. Ange ett namn och en beskrivning av loggen
Det namn som du anger kommer att användas för logg typen enligt beskrivningen ovan.  Den avslutas alltid med _CL för att särskilja den som en anpassad logg.

1. Ange ett namn för loggen.  **\_ R** -suffixet anges automatiskt.
2. Lägg till en valfri **Beskrivning**.
3. Klicka på **Nästa** för att spara den anpassade logg definitionen.

### <a name="step-5-validate-that-the-custom-logs-are-being-collected"></a>Steg 5. Verifiera att de anpassade loggarna samlas in
Det kan ta upp till en timme innan inledande data från en ny anpassad logg visas i Azure Monitor.  Det börjar samla in poster från loggarna som finns i den sökväg du angav från den punkt som du definierade den anpassade loggen.  Den behåller inte de poster som du laddade upp när du skapade den anpassade loggen, men den samlar in redan befintliga poster i loggfilerna som den hittar.

När Azure Monitor börjar samla in från den anpassade loggen är dess poster tillgängliga med en logg fråga.  Använd det namn som du angav för den anpassade loggen som **typen** i frågan.

> [!NOTE]
> Om egenskapen RawData saknas i frågan kan du behöva stänga och öppna webbläsaren igen.

### <a name="step-6-parse-the-custom-log-entries"></a>Steg 6. Parsa de anpassade logg posterna
Hela logg posten kommer att lagras i en enskild egenskap som kallas **RawData**.  Du kommer förmodligen att vilja separera de olika delarna av informationen i varje post i enskilda egenskaper för varje post. Se [parsa text data i Azure Monitor](../log-query/parse-text.md) för alternativ för att parsa **RawData** i flera egenskaper.

## <a name="removing-a-custom-log"></a>Ta bort en anpassad logg
Använd följande process i Azure Portal för att ta bort en anpassad logg som du tidigare har definierat.

1. I menyn **data** i **Avancerade inställningar** för din arbets yta väljer du **anpassade loggar** för att visa en lista över alla dina anpassade loggar.
2. Klicka på **ta bort** bredvid den anpassade loggen som ska tas bort.

## <a name="data-collection"></a>Datainsamling
Azure Monitor samlar in nya poster från varje anpassad logg ungefär var femte minut.  Agenten registrerar sitt ställe i varje loggfil som den samlar in från.  Om agenten går offline under en viss tids period, kommer Azure Monitor att samla in poster från var den senast slutade, även om dessa poster skapades medan agenten var offline.

Hela innehållet i logg posten skrivs till en enskild egenskap med namnet **RawData**.  Se [parsa text data i Azure Monitor](../log-query/parse-text.md) för metoder för att parsa varje importerad loggpost till flera egenskaper.

## <a name="custom-log-record-properties"></a>Egenskaper för anpassad logg post
Anpassade logg poster har en typ med logg namnet som du anger och egenskaperna i följande tabell.

| Egenskap | Beskrivning |
|:--- |:--- |
| TimeGenerated |Datum och tid då posten samlades in av Azure Monitor.  Om loggen använder en tidsbaserad avgränsare är detta den tid som samlas in från posten. |
| SourceSystem |Typ av agent som posten samlades in från. <br> OpsManager – Windows-agent, antingen direkt anslutning eller System Center Operations Manager <br> Linux – alla Linux-agenter |
| RawData |Fullständig text för den insamlade posten. Du kommer förmodligen att vilja [parsa dessa data till enskilda egenskaper](../log-query/parse-text.md). |
| ManagementGroupName |Namnet på hanterings gruppen för System Center-åtgärder hantera agenter.  För andra agenter är detta AOI-\<workspace ID\> |


## <a name="sample-walkthrough-of-adding-a-custom-log"></a>Exempel på genom gång av hur du lägger till en anpassad logg
I följande avsnitt beskrivs ett exempel på hur du skapar en anpassad logg.  Exempel loggen som samlas in har en enda post på varje rad som börjar med ett datum och en tid och sedan kommaavgränsade fält för kod, status och meddelande.  Flera exempel poster visas nedan.

```output
2019-08-27 01:34:36 207,Success,Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
2019-08-27 01:33:33 208,Warning,Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
2019-08-27 01:35:44 209,Success,Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
2019-08-27 01:38:22 302,Error,Application could not connect to database
2019-08-27 01:31:34 303,Error,Application lost connection to database
```

### <a name="upload-and-parse-a-sample-log"></a>Ladda upp och parsa en exempel logg
Vi tillhandahåller en av loggfilerna och kan se de händelser som den kommer att samla in.  I det här fallet är den nya raden en tillräckligt avgränsare.  Om en enskild post i loggen kan sträcka sig över flera rader, måste en tids gräns för tidsstämpel användas.

![Ladda upp och parsa en exempel logg](media/data-sources-custom-logs/delimiter.png)

### <a name="add-log-collection-paths"></a>Lägg till logg samlings Sök vägar
Loggfilerna finns i *C:\MyApp\Logs*.  En ny fil skapas varje dag med ett namn som innehåller datumet i mönstret *appYYYYMMDD. log*.  Ett tillräckligt mönster för den här loggen är *C:\MyApp\Logs \\ \* . log*.

![Logg samlings Sök väg](media/data-sources-custom-logs/collection-path.png)

### <a name="provide-a-name-and-description-for-the-log"></a>Ange ett namn och en beskrivning av loggen
Vi använder ett namn på *MyApp_CL* och anger en **Beskrivning**.

![Loggnamn](media/data-sources-custom-logs/log-name.png)

### <a name="validate-that-the-custom-logs-are-being-collected"></a>Verifiera att de anpassade loggarna samlas in
Vi använder en enkel fråga för *MyApp_CL* för att returnera alla poster från den insamlade loggen.

![Logg fråga utan anpassade fält](media/data-sources-custom-logs/query-01.png)


## <a name="alternatives-to-custom-logs"></a>Alternativ till anpassade loggar
Även om anpassade loggar är användbara om dina data uppfyller de kriterier som anges ovan, finns det fall som följande där du behöver en annan strategi:

- Data passar inte den nödvändiga strukturen, till exempel att ha tidsstämpeln i ett annat format.
- Logg filen följer inte kraven, till exempel fil kodning eller en mappstruktur som inte stöds.
- Data kräver för bearbetning eller filtrering före insamling. 

I de fall där dina data inte kan samlas in med anpassade loggar bör du överväga följande alternativ strategier:

- Använd ett anpassat skript eller någon annan metod för att skriva data till [Windows-händelser](data-sources-windows-events.md) eller [syslog](data-sources-syslog.md) som samlas in av Azure Monitor. 
- Skicka data direkt till Azure Monitor med [http-API för data insamling](../platform/data-collector-api.md). 

## <a name="next-steps"></a>Nästa steg
* Se [parsa text data i Azure Monitor](../log-query/parse-text.md) för metoder för att parsa varje importerad loggpost till flera egenskaper.
* Lär dig mer om [logg frågor](../log-query/log-query-overview.md) för att analysera data som samlas in från data källor och lösningar.
