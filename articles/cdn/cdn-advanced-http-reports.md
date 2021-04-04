---
title: Analysera användnings statistik med Azure CDN avancerade HTTP-rapporter | Microsoft Docs
description: Lär dig hur du skapar avancerade HTTP-rapporter i Microsoft Azure CDN. De här rapporterna innehåller detaljerad information om CDN-aktiviteter.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: ef90adc1-580e-4955-8ff1-bde3f3cafc5d
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: e2ef1367cbbb1fb71159c4520c3d1481904c1769
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92779330"
---
# <a name="analyze-usage-statistics-with-azure-cdn-advanced-http-reports"></a>Analysera förbrukningsstatistik med avancerade HTTP-rapporter i Azure CDN
## <a name="overview"></a>Översikt
Det här dokumentet beskriver avancerad HTTP-rapportering i Microsoft Azure CDN. De här rapporterna innehåller detaljerad information om CDN-aktiviteter.

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="accessing-advanced-http-reports"></a>Åtkomst till avancerade HTTP-rapporter
1. På bladet CDN-profil klickar du på knappen **Hantera** .
   
    ![Knappen Hantera för CDN-profil bladet](./media/cdn-advanced-http-reports/cdn-manage-btn.png)
   
    Hanterings portalen för CDN öppnas.
2. Hovra över fliken **analys** och hovra sedan över de **avancerade http-rapporter** som utfälls.  Klicka på **http stor plattform**.
   
    ![Hanterings Portal för CDN – menyn avancerade rapporter](./media/cdn-advanced-http-reports/cdn-advanced-reports.png)
   
    Rapport alternativ visas.

## <a name="geography-reports-map-based"></a>Geografi rapporter (mappnings-baserade)
Det finns fem rapporter som drar nytta av en karta för att visa de regioner från vilka innehållet begärs. Dessa rapporter är världs karta, USA karta, Kanada Map, Europa karta och Asien och stillahavsområdet karta.

Varje kart-baserad rapport rangordnar geografiska entiteter (t. ex. länder/regioner, en karta som hjälper dig att visualisera de platser som ditt innehåll begärs från. Det kan du göra genom att färgkoda varje region enligt den mängd efter frågan som har inträffat i regionen. Ljusare skuggade regioner indikerar lägre efter frågan på ditt innehåll, medan mörkare regioner visar högre nivåer av efter frågan för ditt innehåll.

Detaljerad trafik-och bandbredds information för varje region anges direkt under kartan. På så sätt kan du Visa det totala antalet träffar, procent andelen träffar, den totala mängden data som överförs (i GB) och procent andelen data som överförs för varje region. Visa en beskrivning av vart och ett av dessa mått. När du hovrar över en region (t. ex. land/region, delstat eller provins) visas namnet och procent andelen träffar som inträffade i regionen som en knapp beskrivning.

En kort beskrivning anges nedan för varje typ av kart-baserad geografi rapport.

| Rapportnamn | Beskrivning |
| --- | --- |
| Världs karta |Med den här rapporten kan du Visa den globala efter frågan för ditt CDN-innehåll. Varje land/region är färgkodad på världs kartan för att Visa procent andelen träffar som kommer från den regionen. |
| USA karta |Med den här rapporten kan du Visa efter frågan på ditt CDN-innehåll i USA. Varje tillstånd är färgkodat på den här kartan för att visa den procent andel av träffarna som kommer från den regionen. |
| Kanada-karta |Med den här rapporten kan du Visa efter frågan på ditt CDN-innehåll i Kanada. Varje provins är färgkodad på den här kartan för att visa den procent andel av träffarna som kommer från den regionen. |
| Europe-karta |Med den här rapporten kan du Visa efter frågan på ditt CDN-innehåll i Europa. Varje land/region är färgkodad på den här kartan för att Visa procent andelen träffar som kommer från den regionen. |
| Asien och stillahavsområdet karta |Med den här rapporten kan du Visa efter frågan på ditt CDN-innehåll i Asien. Varje land/region är färgkodad på den här kartan för att Visa procent andelen träffar som kommer från den regionen. |

## <a name="geography-reports-bar-charts"></a>Geografi rapporter (stapeldiagram)
Det finns två ytterligare rapporter som ger statistisk information enligt geografi, som är främsta städer och främsta länder. Dessa rapporter rangordnar orter och länder/regioner enligt antalet träffar som kommer från dessa länder/regioner. När du skapar den här typen av rapport visar ett stapeldiagram de 10 främsta städerna eller länder/regioner som begärt innehåll över en angiven plattform. Med det här stapeldiagrammet kan du snabbt utvärdera de regioner som genererar det högsta antalet begär Anden för ditt innehåll.

Den vänstra sidan av diagrammet (y-axeln) anger hur många träffar som har inträffat i den angivna regionen. Direkt under grafen (x-axeln) hittar du en etikett för var och en av de översta 10 regionerna.

### <a name="using-the-bar-charts"></a>Använda stapeldiagram
* Om du hovrar över en stapel visas namnet och det totala antalet träffar som inträffat i regionen som en knapp beskrivning.
* Knapp beskrivningen för den främsta städer-rapporten identifierar en ort efter dess namn, region och region/regions förkortning.
* Om den ort eller region (t. ex. stat/provins) som en begäran kommer ifrån inte kunde fastställas, indikerar det att de är okända. Om landet/regionen är okänd visas två frågetecken (t. ex.??).
* En rapport kan innehålla mått för "Europa" eller "Asien/Stilla havs området". Dessa objekt är inte avsedda att ge statistisk information om alla IP-adresser i dessa regioner. De gäller istället endast för begär Anden som kommer från IP-adresser som sprids ut över Europa eller Asien/Stilla havs området i stället för till en speciell ort eller ett land/en region.

De data som användes för att generera stapeldiagrammet kan visas under det. Där hittar du det totala antalet träffar, procent andelen träffar, mängden data som överförs (i GB) och procent andelen data som överförts för de översta 250 regionerna. Visa en beskrivning av vart och ett av dessa mått.

En kort beskrivning anges för båda typerna av rapporter nedan.

| Rapportnamn | Beskrivning |
| --- | --- |
| Främsta städer |Den här rapporten rangordnar städer enligt antalet träffar som kommer från den regionen. |
| Främsta länder |I den här rapporten rangordnas länder/regioner utifrån antalet träffar som kommer från landet/regionen. |

## <a name="daily-summary"></a>Daglig sammanfattning
I rapporten daglig sammanfattning kan du Visa det totala antalet träffar och data som överförs över en viss plattform per dag. Den här informationen kan användas för att snabbt fram CDN-aktivitets mönster. Den här rapporten kan till exempel hjälpa dig att identifiera vilka dagar som har högre eller lägre än den förväntade trafiken.

När du skapar den här typen av rapport ger ett stapeldiagram ett visuellt tecken på hur mycket plattformsspecifik efter frågan som du har haft på daglig basis under den tids period som rapporten gäller. Det gör du genom att visa en stapel för varje dag i rapporten. Om du till exempel väljer den tids period som kallas "förra veckan" skapas ett stapeldiagram med sju staplar. Varje stapel visar det totala antalet träffar som har påträffats den dagen.

Den vänstra sidan av diagrammet (y-axeln) anger hur många träffar som har inträffat på det angivna datumet. Direkt under grafen (x-axeln) hittar du en etikett som anger datumet (format: ÅÅÅÅ-MM-DD) för varje dag som ingår i rapporten.

> [!TIP]
> Om du hovrar över en stapel visas det totala antalet träffar som har inträffat på det datumet som en knapp beskrivning.
> 
> 

De data som användes för att generera stapeldiagrammet kan visas under det. Där kan du se det totala antalet träffar och mängden data som överförs (i GB) för varje dag som omfattas av rapporten.

## <a name="by-hour"></a>Per timme
Med rapporten per timme kan du Visa det totala antalet träffar och överförda data över en viss plattform per timme. Den här informationen kan användas för att snabbt fram CDN-aktivitets mönster. Den här rapporten kan till exempel hjälpa dig att identifiera de tids perioder under dagen som upplever högre eller lägre än den förväntade trafiken.

När du skapar den här typen av rapport ger ett stapeldiagram ett visuellt tecken på hur mycket plattformsspecifik efter frågan som har inträffat per timme under den tids period som rapporten gäller. Det gör du genom att visa en stapel för varje timme som omfattas av rapporten. Om du till exempel väljer en tids period på 24 timmar skapas ett stapeldiagram med tjugo fyra staplar. Varje stapel visar det totala antalet träffar som uppstått under den timmen.

Den vänstra sidan av diagrammet (y-axeln) anger hur många träffar som har inträffat på den angivna timmen. Direkt under grafen (x-axeln) hittar du en etikett som anger datum/tid (format: ÅÅÅÅ-MM-DD hh: mm) för varje timme som ingår i rapporten. Tiden rapporteras med 24-timmarsformat och anges med UTC/GMT-tidszonen.

> [!TIP]
> Om du hovrar över en stapel visas det totala antalet träffar som inträffat under den timmen som en knapp beskrivning.
> 
> 

De data som användes för att generera stapeldiagrammet kan visas under det. Där kan du se det totala antalet träffar och mängden data som överförs (i GB) för varje timme som omfattas av rapporten.

## <a name="by-file"></a>Efter fil
Med hjälp av rapporten per fil kan du Visa mängden efter frågan och trafiken som påförts över en viss plattform för de mest efterfrågade resurserna. När du skapar den här typen av rapport kommer ett stapeldiagram att genereras på de 10 mest efterfrågade till gångarna under den angivna tids perioden.

> [!NOTE]
> I den här rapporten konverteras kantens CNAME-URL: er till motsvarande CDN-URL: er. Detta gör det möjligt att räkna med det totala antalet träffar som är kopplade till en till gång, oavsett vilken CDN-eller Edge CNAME-URL som används för att begära den.
> 
> 

Den vänstra sidan av diagrammet (y-axeln) anger antalet begär Anden för varje till gång under den angivna tids perioden. Direkt under grafen (x-axeln) hittar du en etikett som anger fil namnet för var och en av de 10 mest efterfrågade till gångarna.

De data som användes för att generera stapeldiagrammet kan visas under det. Där hittar du följande information för var och en av de översta 250 begärda till gångarna: relativ sökväg, det totala antalet träffar, procent andel träffar, mängden data som överförs (i GB) och procent andelen data som överförs.

## <a name="by-file-detail"></a>Efter fil information
Med detalj rapporten per fil kan du Visa mängden efter frågan och trafiken som påförts över en viss plattform för en specifik till gång. Överst i den här rapporten finns alternativet fil information för. Det här alternativet innehåller en lista över dina mest efterfrågade till gångar på den valda plattformen. För att kunna generera en fil informations rapport måste du välja önskad till gång i alternativet fil information för. Därefter indikerar ett stapeldiagram hur mycket daglig efter frågan som genererats under den angivna tids perioden.

Den vänstra sidan av diagrammet (y-axeln) anger det totala antalet begär Anden som en till gång har haft på en viss dag. Direkt under grafen (x-axeln) hittar du en etikett som anger det datum (format: ÅÅÅÅ-MM-DD) för vilken CDN-begäran för till gången rapporterades.

De data som användes för att generera stapeldiagrammet kan visas under det. Där kan du se det totala antalet träffar och mängden data som överförs (i GB) för varje dag som omfattas av rapporten.

## <a name="by-file-type"></a>Efter filtyp
Med hjälp av rapporten filtyp kan du se hur mycket efter frågan och vilken trafik som uppkommer efter filtyp. När du skapar den här typen av rapport visar ett ring diagram procent andelen träffar som har genererats av de översta 10 fil typerna.

> [!TIP]
> Om du hovrar över en sektor i ring diagrammet visas Internet medie typen för den filtypen som en knapp beskrivning.
> 
> 

De data som användes för att generera ring diagrammet kan visas under det. Där hittar du fil namns tillägget/Internet medie typen, det totala antalet träffar, procent andelen träffar, mängden data som överförs (i GB) och procent andelen data som överförts för var och en av de översta 250-filtyperna.

## <a name="by-directory"></a>Efter katalog
Med hjälp av rapporten per katalog kan du Visa mängden efter frågan och trafiken som påförts över en viss plattform för innehåll från en viss katalog. När du skapar den här typen av rapport visar ett stapeldiagram det totala antalet träffar som har genererats av innehållet i de översta 10 katalogerna.

### <a name="using-the-bar-chart"></a>Använda stapeldiagrammet
* Hovra över en stapel för att visa den relativa sökvägen till motsvarande katalog.
* Innehåll som lagras i en undermapp i en katalog räknas inte när du beräknar efter frågan efter katalog. Den här beräkningen baseras enbart på antalet begär Anden som skapats för innehåll som lagras i den faktiska katalogen.
* I den här rapporten konverteras kantens CNAME-URL: er till motsvarande CDN-URL: er. Detta ger en korrekt uppfattning för all statistik som är kopplad till en till gång oavsett vilken CDN-eller Edge CNAME-URL som används för att begära den.

Den vänstra sidan av diagrammet (y-axeln) anger det totala antalet begär Anden för det innehåll som lagras i de 10 populära katalogerna. Varje stapel i diagrammet representerar en katalog. Använd färgkodnings schema för att matcha en stapel till en katalog som listas i det översta avsnittet om 250 fullständiga kataloger.

De data som användes för att generera stapeldiagrammet kan visas under det. Där hittar du följande information för var och en av de översta 250 katalogerna: relativ sökväg, det totala antalet träffar, procent andelen träffar, mängden data som överförs (i GB) och procent andelen data som överförs.

## <a name="by-browser"></a>Efter webbläsare
Med rapporten per webbläsare kan du se vilka webbläsare som användes för att begära innehåll. När du skapar den här typen av rapport visar ett cirkel diagram procent andelen förfrågningar som hanteras av de 10 populära webbläsarna.

### <a name="using-the-pie-chart"></a>Använda cirkel diagrammet
* Hovra över en sektor i cirkel diagrammet om du vill visa en webbläsares namn och version.
* I den här rapporten betraktas varje unik kombination av webbläsare/version som en annan webbläsare.
* Sektorn kallas "Övrigt" anger procent andelen förfrågningar som hanteras av alla andra webbläsare och versioner.

De data som användes för att generera cirkel diagrammet kan visas under det. Där hittar du webbläsarens typ/versions nummer, det totala antalet träffar och procent andelen träffar för var och en av de översta 250-webbläsarna.

## <a name="by-referrer"></a>Efter referent
Med hjälp av referent-rapporten kan du Visa de främsta referenterna för innehåll på den valda plattformen. En referent anger det värdnamn som en begäran genererades från. När du skapar den här typen av rapport anger ett stapeldiagram hur mycket efter frågan (t. ex. träffar) som genererats av de 10 främsta referenterna.

Den vänstra sidan av diagrammet (y-axeln) anger det totala antalet begär Anden som en till gång har haft för varje referent. Varje stapel i diagrammet representerar en referent. Använd färg kodnings schema för att matcha en stapel till en referent som anges i det 250 översta referent-avsnittet.

De data som användes för att generera stapeldiagrammet kan visas under det. Där hittar du URL: en, det totala antalet träffar och procent andelen träffar som har genererats från var och en av de översta 250-referenterna.

## <a name="by-download"></a>Genom att ladda ned
Genom att hämta rapporten kan du analysera hämtnings mönster för ditt mest efterfrågade innehåll. Rapportens överkant innehåller ett stapeldiagram som jämför försök med hämtningar med slutförda hämtningar för de 10 mest efterfrågade till gångarna. Varje stapel färgkodas enligt om det är ett försök att hämta (blå) eller en slutförd nedladdning (grön).

> [!NOTE]
> I den här rapporten konverteras kantens CNAME-URL: er till motsvarande CDN-URL: er. Detta ger en korrekt uppfattning för all statistik som är kopplad till en till gång oavsett vilken CDN-eller Edge CNAME-URL som används för att begära den.
> 
> 

Den vänstra sidan av diagrammet (y-axeln) anger fil namnet för var och en av de 10 mest efterfrågade till gångarna. Direkt under grafen (x-axeln) hittar du etiketter som anger det totala antalet hämtningar som har gjorts/slutförts.

Direkt under stapeldiagrammet visas följande information för de översta 250 begärda till gångarna: relativ sökväg (inklusive fil namn), antalet gånger som den har hämtats till slutförd, antalet gånger som den begärdes och procent andelen förfrågningar som resulterade i en fullständig hämtning.

> [!TIP]
> Vår CDN informeras inte av en HTTP-klient (d.v.s. webbläsare) när en till gång har laddats ned helt. Därför måste vi beräkna om en till gång har laddats ned helt i enlighet med status koder och begär Anden om byte-intervall. Det första vi letar efter när du gör den här beräkningen är om begäran resulterar i en status kod på 200 OK. I så fall tittar vi på begäran om byte-intervall för att säkerställa att de täcker hela till gången. Slutligen jämför vi mängden data som överförs till den begärda till gångens storlek. Om data som överförs är lika med eller större än fil storleken och de byte intervall begär Anden är lämpliga för den till gången räknas träffen som en fullständig nedladdning.
> 
> På grund av den här rapportens tolknings egenskaper bör du tänka på följande saker som kan ändra konsekvensen och noggrannheten i rapporten.
> 
> * Trafik mönster kan inte vara korrekt förväntade när användar agenter beter sig på olika sätt. Detta kan producera slutförda nedladdnings resultat som är större än 100%.
> * Till gångar som utnyttjar HTTP progressiv nedladdning kanske inte korrekt representeras av den här rapporten. Detta beror på användare som söker på olika platser i en video.
> 
> 

## <a name="by-404-errors"></a>Med 404 fel
I rapporten med 404 fel kan du identifiera vilken typ av innehåll som genererar flest 404-koder som inte hittas. Rapportens överkant innehåller ett stapeldiagram för de 10 främsta till gångar som en 404 inte hittade status kod returnerade. I det här stapeldiagrammet jämförs det totala antalet begär Anden med begär Anden som resulterade i en 404 som inte hittades i status koden för dessa till gångar. Varje stapel är färgkodad. En gul stapel används för att ange att begäran resulterade i en 404 som inte hittades. En röd stapel används för att ange det totala antalet begär Anden för till gången.

> [!NOTE]
> Observera följande i den här rapporten:
> 
> * En träff representerar en begäran för en till gång oavsett status kod.
> * Kant-CNAME-URL: er konverteras till motsvarande CDN-URL: er. Detta ger en korrekt uppfattning för all statistik som är kopplad till en till gång oavsett vilken CDN-eller Edge CNAME-URL som används för att begära den.
> 
> 

Den vänstra sidan av diagrammet (y-axeln) anger fil namnet för var och en av de 10 mest efterfrågade till gångarna som resulterade i en 404 som inte hittades i status koden. Direkt under grafen (x-axeln) hittar du etiketter som anger det totala antalet begär Anden och antalet begär Anden som resulterade i en 404 som inte hittades i status koden.

Direkt under stapeldiagrammet visas följande information för de översta 250 begärda till gångarna: relativ sökväg (inklusive fil namn), antalet begär Anden som resulterade i en 404 inte hittade status kod, det totala antalet gånger som till gången begärdes och procent andelen begär Anden som resulterade i en 404 som inte hittade status kod.

## <a name="see-also"></a>Se även
* [Översikt över Azure CDN](cdn-overview.md)
* [Real tids statistik i Microsoft Azure CDN](cdn-real-time-stats.md)
* [Åsidosätta standard-HTTP-beteendet med hjälp av regel motorn](./cdn-verizon-premium-rules-engine.md)
* [Analysera Edge-prestanda](cdn-edge-performance.md)