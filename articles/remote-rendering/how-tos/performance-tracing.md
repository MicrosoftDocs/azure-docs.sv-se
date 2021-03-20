---
title: Samla in klientsidans prestandavarningar
description: Metod tips för prestanda profilering på klient sidan med hjälp av WPF
author: florianborn71
ms.author: flborn
ms.date: 12/11/2019
ms.topic: conceptual
ms.openlocfilehash: 1d4ce68bdda5fbc3dfdb7396141289a58dab5bd1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92204103"
---
# <a name="create-client-side-performance-traces"></a>Samla in klientsidans prestandavarningar

Det finns många orsaker till varför prestandan för Azure-fjärrrendering kanske inte är lika lämplig som du vill. Förutom den rena åter givnings prestandan på moln servern, särskilt kvaliteten på nätverks anslutningen, har en betydande inverkan på upplevelsen. Information om hur du profilerar serverns prestanda finns i avsnittet [prestanda frågor för Server sidan](../overview/features/performance-queries.md).

Det här kapitlet fokuserar på hur du identifierar potentiella Flask halsar på klient sidan via *:::no-loc text="performance traces":::* .

## <a name="getting-started"></a>Komma igång

Om du är nybörjare på Windows :::no-loc text="performance tracing"::: -funktionen kommer det här avsnittet att nämna de mest grundläggande villkoren och programmen för att komma igång.

### <a name="installation"></a>Installation

Programmen som används för att spåra med ARR är generella syftes verktyg som kan användas för all Windows-utveckling. De tillhandahålls via [Windows Performance Toolkit](/windows-hardware/test/wpt/). Hämta verktygs uppsättningen för [Windows Assessment and Deployment Kit](/windows-hardware/get-started/adk-install).

### <a name="terminology"></a>Terminologi

När du söker efter information om prestanda spårningar kommer du oundvikligen att komma över ett intervall av villkor. De viktigaste är:

* `ETW`
* `ETL`
* `WPR`
* `WPA`

**ETW** står för [ **E**- Windows](/windows/win32/etw/about-event-tracing). Det är helt enkelt det övergripande namnet för den effektiva spårnings funktionen på kernel-nivå som är inbyggd i Windows. Den kallas *händelse* spårning, eftersom program som stöder ETW genererar händelser för att logga åtgärder som kan hjälpa till att spåra prestanda problem. Som standard genererar operativ systemet händelser för till exempel disk åtkomst, aktivitets växlar och sådana. Program som ARR genererar dessutom anpassade händelser, till exempel om släppta ramar, nätverks fördröjning osv.

**ETL** står för **E**-ventilation **t**. ex. **L** ogging. Det innebär bara att en spårning har samlats in (loggats) och vanligt vis används som fil namns tillägg för filer som lagrar spårnings data. När du gör en spårning får du därför vanligt vis en \* . etl-fil efteråt.

**WPR** står för [ **W** Windows **P** erformance **R** ecorder](/windows-hardware/test/wpt/windows-performance-recorder) och är namnet på programmet som startar och stoppar inspelningen av händelse spår. WPR tar en profil fil ( \* . wprp) som konfigurerar vilka exakta händelser som ska loggas. En sådan `wprp` fil ingår i arr SDK. När du spårar på en stationär dator kan du starta WPR direkt. När du utför en spårning på HoloLens går du normalt igenom webb gränssnittet i stället.

**WPA** står för [ **b** Windows **P** erformance **A** nalyzer](/windows-hardware/test/wpt/windows-performance-analyzer) och är namnet på det GUI-program som används för att öppna \* . etl-filer och gå igenom data för att identifiera prestanda problem. Med WPA kan du sortera data efter olika villkor, Visa data på flera sätt, få mer information och korrelera information.

Även om ETL-spårningar kan skapas på en Windows-enhet (lokal dator, HoloLens, moln server osv.), sparas de vanligt vis på disk och analyseras med WPA på en stationär dator. ETL-filer kan skickas till andra utvecklare så att de får en titt. Tänk på att känslig information, t. ex. fil Sök vägar och IP-adresser, kan fångas in i ETL-spår, även om. Du kan använda ETW på två sätt: om du vill spela in spår eller analysera spår. Inspelnings spårningar är rakt framåt och kräver minimal konfiguration. Analys av spår å andra sidan kräver en vettigt förståelse av både WPA-verktyget och det problem som du undersöker. Allmänt material för Learning WPA anges nedan, samt rikt linjer för hur du tolkar ARR-specifikt spår.

## <a name="recording-a-trace-on-a-local-pc"></a>Spela in en spårning på en lokal dator

För att identifiera problem med ARR-prestanda bör du prioritera en spårning direkt på en HoloLens, eftersom det är det enda sättet att få en ögonblicks bild av de sanna prestanda egenskaperna. Men om du vill göra en spårning utan prestanda begränsningar för HoloLens eller om du bara vill lära dig hur du använder WPA och inte behöver en realistisk spårning, så gör du så här.

### <a name="wpr-configuration"></a>WPR-konfiguration

1. Starta [:::no-loc text="Windows Performance Recorder":::](/windows-hardware/test/wpt/windows-performance-recorder) från *Start-menyn*.
1. Expandera **fler alternativ**
1. Klicka på **Lägg till profiler...**
1. Välj filen *AzureRemoteRenderingNetworkProfiling. wprp*. Du hittar den här filen i ARR SDK under *Tools/ETLProfiles*.
   Profilen visas nu i WPR under *anpassade mätningar*. Se till att det är den enda aktiverade profilen.
1. Expandera *prioritering på första nivån*:
    * **Inaktivera** det här alternativet om du vill göra en snabb spårning av arr-nätverkets händelser.
    * Om du behöver korrelera nätverks händelser med andra system egenskaper, till exempel processor-eller minnes användning, **aktiverar** du det här alternativet.
    * Om du aktiverar det här alternativet kommer spårningen troligen att vara flera gigabyte i storlek och ta lång tid att spara och öppna i WPA.

Därefter bör din WPR-konfiguration se ut så här:

![WPR-konfiguration](./media/wpr.png)

### <a name="recording"></a>Inspelning

Klicka på **Starta** för att börja spela in en spårning. Du kan starta och stoppa inspelningen när som helst. du behöver inte stänga ditt program innan du gör det. Som du kan se behöver du inte ange vilket program som ska spåras, eftersom ETW alltid registrerar en spårning för hela systemet. `wprp`Filen anger vilka typer av händelser som ska registreras.

Klicka på **Spara** för att stoppa inspelningen och ange var ETL-filen ska lagras.

Nu har du en ETL-fil som du antingen kan öppna direkt i WPA eller skicka till någon annan.

## <a name="recording-a-trace-on-a-hololens"></a>Spela in en spårning på en HoloLens

Om du vill registrera en spårning på en HoloLens startar du enheten och anger dess IP-adress i en webbläsare för att öppna *enhets portalen*.

![Enhets Portal](./media/wpr-hl.png)

1. Till vänster navigerar du till *prestanda > prestanda spårning*.
1. Välj **anpassade profiler**
1. Markera **:::no-loc text="Browse...":::**
1. Välj filen *AzureRemoteRenderingNetworkProfiling. wprp*. Du hittar den här filen i ARR SDK under *Tools/ETLProfiles*.
1. Klicka på **Starta spårning**
1. HoloLens registrerar nu en spårning. Se till att utlösa de prestanda problem som du vill undersöka. Klicka sedan på **Stoppa spårning**.
1. Spårningen visas längst ned på webb sidan. Klicka på disk ikonen till höger för att ladda ned ETL-filen.

Nu har du en ETL-fil som du antingen kan öppna direkt i WPA eller skicka till någon annan.

## <a name="analyzing-traces-with-wpa"></a>Analysera spår med WPA

### <a name="wpa-basics"></a>Grundläggande om WPA

Windows Performance Analyzer är standard verktyget för att öppna ETL-filer och granska spåren. En förklaring om att WPA fungerar utanför räckvidden för den här artikeln. Kom igång genom att ta en titt på dessa resurser:

* Titta på de [inledande videorna](/windows-hardware/test/wpt/windows-performance-analyzer) för en första översikt.
* WPA har en *komma igång* flik, som förklarar vanliga steg. Titta på de tillgängliga ämnena. I synnerhet under "Visa data" får du en snabb introduktion till hur du skapar grafer för vissa data.
* Det finns mycket bra information [på den här webbplatsen](https://randomascii.wordpress.com/2015/09/24/etw-central/), men alla är inte relevanta för nybörjare.

### <a name="graphing-data"></a>Diagram data

För att komma igång med ARR-spårning är följande pjäser lämpliga att känna till.

![Prestanda diagram](./media/wpa-graph.png)

Bilden ovan visar en tabell med spårnings data och en diagram representation av samma data.

I tabellen längst ned noterar du det gula (gyllene) fältet och det blå fältet. Du kan dra staplarna och placera dem var som helst.

Alla **kolumner till vänster om det gula fältet** tolkas som **nycklar**. Nycklar används för att strukturera trädet i det övre vänstra fönstret. Här har vi två *nyckel* kolumner, "providernamn" och "uppgifts namn". Därför är träd strukturen i det övre vänstra fönstret två nivåer djup. Om du ändrar ordning på kolumnerna eller lägger till eller tar bort kolumner från nyckel arean ändras strukturen i trädvyn.

**Kolumner till höger om det blå fältet** används för **diagrammets visning** i det övre högra fönstret. De flesta av tiden är bara den första kolumnen som används, men vissa diagram lägen kräver flera kolumner med data. För att linje diagram ska fungera måste *samlings läget* i den kolumnen anges. Använd "AVG" eller "Max". Samlings läget används för att fastställa diagrammets värde vid en bestämd pixel, när en pixel täcker ett intervall med flera händelser. Detta kan observeras genom att ställa in agg regering till sum och sedan zooma in och ut.

Kolumnerna i mitten har ingen särskild betydelse.

![Vyn händelser](./media/wpa-event-view.png)

I *visnings redigeraren för allmänna händelser* kan du konfigurera alla kolumner som ska visas, agg regerings läge, sortering och vilka kolumner som används som nycklar eller för diagram. I exemplet ovan är **fält 2** aktiverat och fält 3-6 är inaktiverade. Fält 2 är vanligt vis det första *anpassade data* fältet för en ETW-händelse och därmed för arr-händelser (FrameStatistics) som representerar ett visst värde för nätverks fördröjning. Aktivera andra fält kolumner om du vill se ytterligare värden för den här händelsen.

### <a name="presets"></a>Hands

Om du vill analysera en spårning korrekt måste du ta reda på ditt eget arbets flöde och önskad data visning. Men för att kunna få en snabb översikt över de ARR-speciella händelserna inkluderar vi Windows Software Protection Platform-profil och för inställningar filer i mappen *verktyg/ETLProfiles*. Om du vill läsa in en fullständig profil väljer du *profiler > tillämpa...* från meny raden i WPA eller öppnar panelen *Mina för hands inställningar* (*fönster > mina för inställningar*) och väljer *Importera*. Den tidigare konfigurationen kommer att konfigurera en fullständig WPA-konfiguration som i bilden nedan. Den senare kommer bara att göra för inställningar för de olika visnings konfigurationerna som är tillgängliga och gör att du snabbt kan öppna en vy för att titta på en viss del av händelse data för ARR.

![Hands](./media/wpa-arr-trace.png)

Bilden ovan visar vyer för olika ARR-speciella händelser plus en vy över den totala processor användningen.

## <a name="next-steps"></a>Nästa steg

* [Prestandafrågor på serversidan](../overview/features/performance-queries.md)