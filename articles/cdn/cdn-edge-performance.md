---
title: Analysera Edge-Nodens prestanda i Azure CDN | Microsoft Docs
description: Analysera Edge-Nodens prestanda i Microsoft Azure CDN. Edge Performance Analytics tillhandahåller detaljerad informations trafik och bandbredds användning för CDN.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 8cc596a7-3e01-4f76-af7b-a05a1421517e
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: d8119c56c26ed32db136064d93775d21a71f238f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92778652"
---
# <a name="analyze-edge-node-performance-in-microsoft-azure-cdn"></a>Analysera gränsnodsprestanda i Microsoft Azure CDN
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Översikt
Edge Performance Analytics tillhandahåller detaljerad informations trafik och bandbredds användning för CDN. Den här informationen kan sedan användas för att generera trend statistik, vilket gör att du kan få information om hur dina resurser cachelagras och levereras till dina klienter. På så sätt kan du skapa en strategi för hur du optimerar leverans av ditt innehåll och avgör vilka problem som ska åtgärdas för att bättre utnyttja CDN. Det innebär att du inte bara kommer att kunna förbättra prestandan för data leveranser, men du kan också minska dina CDN-kostnader.

> [!NOTE]
> Alla rapporter använder UTC/GMT-notation när du anger ett datum/tid.
> 
> 

## <a name="reports-and-log-collection"></a>Rapporter och logg insamling
CDN-aktivitets data måste samlas in av Edge Performance Analytics-modulen innan den kan generera rapporter på den. Den här samlings processen sker en gång om dagen och den täcker den aktivitet som ägde rum under föregående dag. Det innebär att rapportens statistik representerar ett exempel på dags statistik vid den tidpunkt då den bearbetades och innehåller inte nödvändigt vis en fullständig uppsättning data för den aktuella dagen. Den primära funktionen i dessa rapporter är att utvärdera prestandan. De bör inte användas i fakturerings syfte eller med en exakt numerisk statistik.

> [!NOTE]
> Rå data som analys rapporter skapas från, är tillgängliga i minst 90 dagar.
> 
> 

## <a name="dashboard"></a>Instrumentpanel
Instrument panelen för kant prestanda analys spårar aktuell och historisk CDN-trafik via ett diagram och statistik. Använd den här instrument panelen för att upptäcka senaste och långsiktiga trender om prestanda för CDN-trafik för ditt konto.

Den här instrument panelen består av:

* Ett interaktivt diagram som möjliggör visualisering av viktiga mått och trender.
* En tids linje som ger en uppfattning om långsiktiga mönster för viktiga mått och trender.
* Viktiga Mät värden och statistisk information om hur ditt CDN-nätverk förbättrar plats trafiken som mäts av den övergripande prestandan, användningen och effektiviteten.

### <a name="accessing-the-edge-performance-dashboard"></a>Åtkomst till instrument panelen för Edge-prestanda
1. På bladet CDN-profil klickar du på knappen **Hantera** .
   
    ![Knappen Hantera för CDN-profil bladet](./media/cdn-edge-performance/cdn-manage-btn.png)
   
    Hanterings portalen för CDN öppnas.
2. Hovra över fliken **analys** och hovra över den utfällbara **prestanda analysen** .  Klicka på **instrument panelen**.
   
    Instrument panelen Edge Node Analytics visas.

### <a name="chart"></a>Diagram
Instrument panelen innehåller ett diagram som spårar ett mått under den valda tids perioden i tids linjen som visas direkt under den.  En tids linje som visar att grafer upp till de senaste två årens CDN-aktivitet visas direkt under diagrammet.

#### <a name="using-the-chart"></a>Använda diagrammet
* Som standard visas ett diagram i cachens effektivitets grad under de senaste 30 dagarna.
* Det här diagrammet genereras från data som sorteras per dag.
* Om du hovrar över en dag i linje diagrammet visas ett datum och värdet för måttet på det datumet.
* Klicka på Markera helger för att växla mellan ljusgrå lodräta staplar som representerar helger i diagrammet. Den här typen av överlägg är användbar för att identifiera trafik mönster över helger.
* Klicka på Visa ett år sedan för att växla ett överlägg för föregående års aktivitet under samma tids period till diagrammet. Den här typen av jämförelse ger insikter om användnings mönster för långsiktiga CDN. Det övre högra hörnet i diagrammet innehåller en förklaring som anger färg koden för varje linje diagram.

#### <a name="updating-the-chart"></a>Diagrammet uppdateras
* Tidsintervall: gör något av följande:
  * Välj önskad region på tids linjen. Diagrammet kommer att uppdateras med data som motsvarar den valda tids perioden.
  * Dubbelklicka på diagrammet om du vill visa alla tillgängliga historiska data upp till högst två år.
* Mått: Klicka på diagram ikonen som visas bredvid det önskade måttet. Diagrammet och tids linjen kommer att uppdateras med data för motsvarande mått.

### <a name="key-metrics-and-statistics"></a>Nyckel värden och statistik
#### <a name="efficiency-metrics"></a>Effektivitets mått
Syftet med dessa mått är att se om cache-effektiviteten kan förbättras. De främsta fördelarna som härleds från cache-effektiviteten är:

* Minskad belastning på ursprungs servern som kan leda till:
  * Bättre webb server prestanda.
  * Lägre drifts kostnader.
* Förbättrad acceleration av data leveranser eftersom fler förfrågningar kommer att betjänas direkt från CDN.

| Fält | Beskrivning |
| --- | --- |
| Cache-effektivitet |Anger procent andelen data som har överförts från cachen. Måttet mäter när en cachelagrad version av det begärda innehållet betjänades direkt från CDN (Edge-servrar) till beställare (t. ex. webbläsare) |
| Träff hastighet |Anger procent andelen begär Anden som har hanterats från cachen. Måttet mäter när en cachelagrad version av det begärda innehållet betjänades direkt från CDN (Edge-servrar) till beställare (t. ex. webbläsare). |
| % av fjär byte – ingen cache-konfiguration |Anger procent andelen trafik som har betjänats från ursprungs servrar till CDN (Edge-servrar) som inte cachelagras på grund av funktionen bypass cache (HTTP-regler motor). |
| % av fjärranslutna byte-förfallet cache |Anger procent andelen trafik som har bearbetats från ursprungs servrar till CDN (Edge-servrar) som ett resultat av inaktuellt innehåll. |

#### <a name="usage-metrics"></a>Användningsstatistik
Syftet med dessa mått är att ge insikter om följande kostnader för styckning:

* Minimera drifts kostnaderna genom CDN.
* Minska CDN-utgifter genom effektivitet och komprimering av cache.

> [!NOTE]
> Trafik volym nummer representerar trafik som användes i beräkningar av förhållande och procent och kan bara visa en del av den totala trafiken för kunder med stora volymer.
> 
> 

| Fält | Beskrivning |
| --- | --- |
| Byte ut för byte ut |Anger det genomsnittliga antalet byte som överförs för varje begäran som hanteras från CDN (Edge-servrar) till beställaren (t. ex. webbläsare). |
| Ingen byte hastighet för cache-konfiguration |Anger procent andelen trafik som hanteras från CDN (Edge-servrar) till beställaren (t. ex. webbläsare) som inte kommer att cachelagras på grund av funktionen för att kringgå cache. |
| Komprimerad byte-hastighet |Anger procent andelen trafik som skickas från CDN (Edge-servrar) till beställare (t. ex. webbläsare) i komprimerat format. |
| Byte ut |Anger mängden data, i byte, som levererades från CDN (Edge-servrar) till beställaren (t. ex. webbläsare). |
| Byte in |Anger mängden data, i byte, som skickats från beställare (t. ex. webbläsare) till CDN (Edge-servrar). |
| Byte, fjärran sluten |Anger mängden data, i byte, som skickas från CDN och kund ursprungs servrar till CDN (Edge-servrar). |

#### <a name="performance-metrics"></a>Prestandamått
Syftet med dessa mått är att spåra den totala CDN-prestandan för din trafik.

| Fält | Beskrivning |
| --- | --- |
| Överföringshastighet |Anger genomsnitts takten som innehållet överfördes från CDN till en beställare. |
| Varaktighet |Visar genomsnitts tiden, i millisekunder, som tog att leverera en till gång till en beställare (t. ex. webbläsare). |
| Komprimerad begär ande frekvens |Anger procent andelen träffar som levererades från CDN (Edge-servrar) till beställaren (t. ex. webbläsare) i komprimerat format. |
| 4xx fel frekvens |Anger procent andelen träffar som genererade status kod för 4xx. |
| 5xx fel frekvens |Anger procent andelen träffar som genererade status kod för 5xx. |
| Träffar |Anger antalet begär Anden för CDN-innehåll. |

#### <a name="secure-traffic-metrics"></a>Skydda trafik mått
Syftet med dessa mått är att spåra CDN-prestanda för HTTPS-trafik.

| Fält | Beskrivning |
| --- | --- |
| Säker cache-effektivitet |Anger procent andelen data som överförts för HTTPS-begäranden som har hanterats från cachen. Måttet mäter när en cachelagrad version av det begärda innehållet betjänades direkt från CDN (Edge-servrar) till beställare (t. ex. webbläsare) över HTTPS. |
| Säker överföringshastighet |Anger den genomsnittliga frekvens med vilken innehåll överfördes från CDN (Edge-servrar) till beställare (t. ex. webb servrar) över HTTPS. |
| Genomsnittlig säker varaktighet |Visar genomsnitts tiden, i millisekunder, som tog att leverera en till gång till en beställare (t. ex. webbläsare) över HTTPS. |
| Säkra träffar |Anger antalet HTTPS-begäranden för CDN-innehåll. |
| Säkra byte ut |Anger mängden HTTPS-trafik, i byte, som levererades från CDN (Edge-servrar) till beställaren (t. ex. webbläsare). |

## <a name="reports"></a>Rapporter
Varje rapport i den här modulen innehåller ett diagram och statistik över bandbredds-och trafik användning för olika typer av mått (t. ex. HTTP-statuskod, cache status koder, begär ande-URL osv.). Den här informationen kan användas för att fördjupa dig i hur innehållet betjänas för dina klienter och för att finjustera CDN-beteendet för att förbättra data leverans prestanda.

### <a name="accessing-the-edge-performance-reports"></a>Åtkomst till kant prestanda rapporter
1. På bladet CDN-profil klickar du på knappen **Hantera** .
   
    ![Knappen Hantera för CDN-profil bladet](./media/cdn-edge-performance/cdn-manage-btn.png)
   
    Hanterings portalen för CDN öppnas.
2. Hovra över fliken **analys** och hovra över den utfällbara **prestanda analysen** .  Klicka på ett **stort http-objekt**.
   
    Skärmen Edge nod analys rapporter visas.

| Rapport | Beskrivning |
| --- | --- |
| Daglig sammanfattning |Gör att du kan visa dagliga trafik trender under en angiven tids period. Varje stapel i diagrammet representerar ett visst datum. Storleken på stapeln anger det totala antalet träffar som inträffat på det datumet. |
| Sammanfattning per timme |Gör att du kan visa tendenser i varje timme under en angiven tids period. Varje stapel i diagrammet representerar en enda timme på ett visst datum. Storleken på stapeln anger det totala antalet träffar som inträffat under den timmen. |
| Protokoll |Visar nedbrytning av trafik mellan HTTP-och HTTPS-protokollen. Ett ring diagram anger procent andelen träffar som har inträffat för varje typ av protokoll. |
| HTTP-metoder |Gör att du kan få en snabb uppfattning om vilka HTTP-metoder som används för att begära dina data. Vanligt vis är de vanligaste HTTP-metoderna GET, HEAD och POST. Ett ring diagram anger procent andelen träffar som har inträffat för varje typ av HTTP-metod för begäran. |
| Webbadresser |Innehåller ett diagram som visar de 10 mest efterfrågade URL: erna. Ett fält visas för varje URL. Stapelns höjd anger hur många träffar som en viss URL har genererat under den tids period som rapporten avser. Statistik för de översta 100 begärda URL: erna visas direkt under det här diagrammet. |
| CNAME |Innehåller ett diagram som visar de 10 populäraste CNAME som används för att begära till gångar under rapportens tids period. Statistik för de översta 100 som begärde CNAME visas direkt under det här diagrammet. |
| Referens |Innehåller ett diagram som visar de 10 främsta CDN-eller kund ursprungs servrar från vilka till gångar har begärts under en angiven tids period. Statistik för de högsta 100 begärda CDN-eller kund ursprungs servrarna visas direkt under det här diagrammet. Kund ursprungs servrar identifieras med det namn som definierats i alternativet katalog namn. |
| Geo pop |Visar hur mycket av trafiken som dirigeras genom en viss närvaro (POP). Förkortningen med tre bokstäver representerar en POP i vårt CDN-nätverk. |
| Klienter |Innehåller ett diagram som visar de 10 främsta klienter som begärde till gångar under en angiven tids period. I den här rapporten anses alla begär Anden som härstammar från samma IP-adress vara från samma klient. Statistik för de översta 100 klienterna visas direkt under det här diagrammet. Den här rapporten är användbar när du ska bestämma mönster för hämtnings aktiviteter för dina främsta klienter. |
| Cache status |Ger en detaljerad nedbrytning av cache-beteende, som kan visa metoder för att förbättra den övergripande slut användar upplevelsen. Eftersom den snabbaste prestandan kommer från cacheträffar kan du optimera data leverans hastigheten genom att minimera Cachemissar och utgångna cacheträffar. |
| INGEN information |Innehåller ett diagram som visar de 10 populära URL: erna för till gångar för vilka cache-innehåll inte har kontroller ATS under en angiven tids period. Statistik för de översta 100-URL: erna för dessa typer av till gångar visas direkt under grafen. |
| CONFIG_NOCACHE information |Innehåller ett diagram som visar de 10 populäraste URL: erna för till gångar som inte cachelagrades på grund av kundens CDN-konfiguration. Dessa typer av till gångar betjänades direkt från ursprungs servern. Statistik för de översta 100-URL: erna för dessa typer av till gångar visas direkt under grafen. |
| Information som inte går att CACHELAGRA |Innehåller ett diagram som visar de 10 populära URL: erna för till gångar som inte kunde cachelagras på grund av begär ande huvud data. Statistik för de översta 100-URL: erna för dessa typer av till gångar visas direkt under grafen. |
| TCP_HIT information |Innehåller ett diagram som visar de 10 populära URL: erna för till gångar som betjänas direkt från cachen. Statistik för de översta 100-URL: erna för dessa typer av till gångar visas direkt under grafen. |
| TCP_MISS information |Innehåller ett diagram som visar de 10 populära URL: erna för till gångar som har cache-status TCP_MISS. Statistik för de översta 100-URL: erna för dessa typer av till gångar visas direkt under grafen. |
| TCP_EXPIRED_HIT information |Innehåller ett diagram som visar de 10 populäraste URL: erna för inaktuella till gångar som har hanterats direkt från POP. Statistik för de översta 100-URL: erna för dessa typer av till gångar visas direkt under grafen. |
| TCP_EXPIRED_MISS information |Innehåller ett diagram som visar de 10 populäraste URL: erna för inaktuella till gångar som en ny version måste hämtas från ursprungs servern. Statistik för de översta 100-URL: erna för dessa typer av till gångar visas direkt under grafen. |
| TCP_CLIENT_REFRESH_MISS information |Innehåller ett stapeldiagram som visar de 10 översta URL: erna för till gångar som hämtats från en ursprungs server på grund av en begäran om cachelagring från klienten. Statistik för de översta 100-URL: erna för dessa typer av begär Anden visas direkt under det här diagrammet. |
| Typer av klient förfrågningar |Anger vilken typ av begär Anden som gjorts av HTTP-klienter (t. ex. webbläsare). Den här rapporten innehåller ett ring diagram som ger en uppfattning om hur förfrågningar hanteras. Bandbredds-och trafik information för varje typ av begäran visas under diagrammet. |
| Användar agent |Innehåller ett stapeldiagram som visar de 10 främsta användar agenterna för att begära innehåll via vårt CDN. Vanligt vis är en användar agent en webbläsare, Media Player eller en mobil telefon webbläsare. Statistik för de översta 100 användar agenterna visas direkt under det här diagrammet. |
| Referenter |Innehåller ett stapeldiagram som visar de 10 främsta referenterna till innehåll som nås via vårt CDN. Vanligt vis är en referent webb adressen till den webb sida eller resurs som länkar till ditt innehåll. Detaljerad information finns under grafen för de översta 100-referenterna. |
| Komprimerings typer |Innehåller ett ring diagram som delar upp de begärda till gångarna genom att de har komprimerats av våra Edge-servrar. Procent andelen komprimerade till gångar delas upp av den typ av komprimering som används. Detaljerad information anges under grafen för varje komprimerings typ och status. |
| Filtyper |Innehåller ett stapeldiagram som visar de 10 viktigaste filtyper som har begärts via vårt CDN för ditt konto. För den här rapporten definieras en filtyp av till gångens fil namns tillägg och Internet medie typ (t. ex. html- \[ text/HTML \] ,. htm- \[ text/HTML \] ,. aspx- \[ text/html, \] osv.). Detaljerad information finns under grafen för de översta 100-filtyperna. |
| Unika filer |Innehåller ett diagram som visar det totala antalet unika till gångar som har begärts på en viss dag under en angiven tids period. |
| Sammanfattning av token auth |Innehåller ett cirkel diagram som ger en snabb översikt över om de begärda till gångarna skyddas av Token-Based autentisering. Skyddade till gångar visas i diagrammet enligt resultatet av deras autentiseringsförsök. |
| Neka information om token auth |Innehåller ett stapeldiagram som gör att du kan visa de 10 främsta förfrågningar som nekats på grund av Token-Based autentisering. |
| HTTP-svars koder |Innehåller en uppdelning av HTTP-statuskod (t. ex. 200 OK, 403 förbjuden, 404 ej påträffat) som levererades till dina HTTP-klienter av våra Edge-servrar. Med ett cirkel diagram kan du snabbt bedöma hur dina till gångar betjänades. Detaljerade statistiska data tillhandahålls för varje svarskod under grafen. |
| 404 fel |Innehåller ett stapeldiagram som gör att du kan visa de 10 främsta förfrågningar som resulterade i en 404 som inte hittade svars koden. |
| 403 fel |Innehåller ett stapeldiagram som gör att du kan visa de 10 främsta förfrågningar som resulterade i en 403-förbjuden svarskod. En 403 förbjuden svarskod inträffar när en begäran nekas av en kund ursprungs Server eller en Edge-Server på vår POP. |
| 4xx-fel |Innehåller ett stapeldiagram som gör att du kan visa de 10 främsta förfrågningar som resulterade i en svarskod i intervallet 400. Exkluderad från den här rapporten går inte att hitta 403 och 404 förbjudna svars koder. Normalt inträffar en 4xx-svarskod när en begäran nekas till följd av ett klient fel. |
| 504 fel |Innehåller ett stapeldiagram som gör att du kan visa de 10 främsta förfrågningar som resulterade i en 504 Gateway-tidsgräns. En svars kod på 504 Gateway-timeout inträffar när en timeout inträffar när en HTTP-proxy försöker kommunicera med en annan server. Om det finns ett CDN-svar med en tids gräns för 504 Gateway inträffar vanligt vis när en Edge-Server inte kan upprätta kommunikation med en kund ursprungs Server. |
| 502 fel |Innehåller ett stapeldiagram som gör att du kan visa de 10 främsta förfrågningar som resulterade i en 502 Felaktig gateway-svarskod. En 502 Felaktig gateway-svarskod inträffar när ett HTTP-protokollfel uppstår mellan en server och en HTTP-proxy. I fallet med ett CDN uppstår en 502 Felaktig gateway-svarskod när en kund ursprungs server returnerar ett ogiltigt svar på en Edge-Server. Ett svar är ogiltigt om det inte går att parsa eller om det är ofullständigt. |
| 5xx-fel |Innehåller ett stapeldiagram som gör att du kan visa de 10 främsta förfrågningar som resulterade i en svarskod i intervallet 500.  Undantaget från den här rapporten är 502 Felaktiga gateway-och 504 Gateway-svars koder. |

## <a name="see-also"></a>Se även
* [Översikt över Azure CDN](cdn-overview.md)
* [Real tids statistik i Microsoft Azure CDN](cdn-real-time-stats.md)
* [Åsidosätta standard-HTTP-beteendet med hjälp av regel motorn](./cdn-verizon-premium-rules-engine.md)
* [Avancerade HTTP-rapporter](cdn-advanced-http-reports.md)