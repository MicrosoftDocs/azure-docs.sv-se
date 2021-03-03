---
title: Anpassade fält i Azure Monitor (förhands granskning) | Microsoft Docs
description: Med funktionen anpassade fält i Azure Monitor kan du skapa egna sökbara fält från poster i en Log Analytics arbets yta som lägger till i egenskaperna för en insamlad post.  Den här artikeln beskriver processen för att skapa ett anpassat fält och ger en detaljerad genom gång av en exempel händelse.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/23/2019
ms.openlocfilehash: f35100d703e56d1fda731aab5e6a96c791c8f0d9
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101713685"
---
# <a name="create-custom-fields-in-a-log-analytics-workspace-in-azure-monitor-preview"></a>Skapa anpassade fält i en Log Analytics arbets yta i Azure Monitor (förhands granskning)

> [!NOTE]
> Den här artikeln beskriver hur du tolkar text data i en Log Analytics arbets yta när den samlas in. Vi rekommenderar att du tolkar text data i ett frågefilter när den har samlats in efter den vägledning som beskrivs i [parsa text data i Azure Monitor](./parse-text.md). Det ger flera fördelar jämfört med att använda anpassade fält.

> [!IMPORTANT]
> Anpassade fält ökar mängden data som samlas in i arbets ytan Log Analytics som kan öka din kostnad. Mer information finns i [Hantera användning och kostnader med Azure Monitor loggar](./manage-cost-storage.md#pricing-model) .

Med funktionen **anpassade fält** i Azure Monitor kan du utöka befintliga poster i arbets ytan Log Analytics genom att lägga till egna sökbara fält.  Anpassade fält fylls i automatiskt från data som extraheras från andra egenskaper i samma post.

![Diagrammet visar en ursprunglig post som är associerad med en modifierad post i en Log Analytics arbets yta med egenskaps värde par som har lagts till i den ursprungliga egenskapen i den ändrade posten.](media/custom-fields/overview.png)

Exempel posten nedan har till exempel viktiga data täckta i händelse beskrivningen. Om du extraherar dessa data till en separat egenskap gör den tillgänglig för sådana åtgärder som sortering och filtrering.

![Exempel på utdrag](media/custom-fields/sample-extract.png)

> [!NOTE]
> I förhands granskningen är du begränsad till 100 anpassade fält i din arbets yta.  Den här gränsen kommer att utökas när den här funktionen når allmän tillgänglighet.

## <a name="creating-a-custom-field"></a>Skapa ett anpassat fält
När du skapar ett anpassat fält måste Log Analytics förstå vilka data som ska användas för att fylla i dess värde.  Den använder en teknik från Microsoft Research som kallas FlashExtract för att snabbt identifiera dessa data.  I stället för att du behöver ge explicita instruktioner Azure Monitor lär dig mer om de data som du vill extrahera från exempel som du anger.

I följande avsnitt beskrivs hur du skapar ett anpassat fält.  Längst ned i den här artikeln finns en genom gång av en exempel extrahering.

> [!NOTE]
> Det anpassade fältet fylls i när poster som matchar de angivna villkoren läggs till i Log Analytics-arbetsytan, så att det bara visas i poster som samlas in efter att det anpassade fältet har skapats.  Det anpassade fältet kommer inte att läggas till i poster som redan finns i data lagret när det skapas.
> 

### <a name="step-1--identify-records-that-will-have-the-custom-field"></a>Steg 1 – identifiera poster som ska ha det anpassade fältet
Det första steget är att identifiera de poster som kommer att hämta det anpassade fältet.  Du börjar med en [standard logg fråga](./log-query-overview.md) och väljer sedan en post som ska fungera som den modell som Azure Monitor kommer att lära sig från.  När du anger att du ska extrahera data till ett anpassat fält öppnas **guiden extrahering av fält** där du kan validera och förfina kriterierna.

1. Gå till **loggar** och Använd en [fråga för att hämta de poster](./log-query-overview.md) som ska ha det anpassade fältet.
2. Välj en post som Log Analytics använda för att agera som en modell för att extrahera data för att fylla i det anpassade fältet.  Du kommer att identifiera de data som du vill extrahera från den här posten. Log Analytics kommer att använda den här informationen för att fastställa logiken för att fylla i det anpassade fältet för alla liknande poster.
3. Expandera post egenskaperna, klicka på ellipsen till vänster om postens översta egenskap och välj **extrahera fält från**.
4. **Guiden extrahering av fält** öppnas och posten som du har valt visas i **huvud exempel** kolumnen.  Det anpassade fältet kommer att definieras för dessa poster med samma värden i de egenskaper som har marker ATS.  
5. Om markeringen inte är exakt vad du vill ha väljer du ytterligare fält för att begränsa villkoren.  Du måste avbryta och välja en annan post som matchar de kriterier som du vill använda för att ändra fältvärdena för villkoret.

### <a name="step-2---perform-initial-extract"></a>Steg 2 – utför första extrahering.
När du har identifierat de poster som ska ha det anpassade fältet kan du identifiera de data som du vill extrahera.  Log Analytics kommer att använda den här informationen för att identifiera liknande mönster i liknande poster.  I steget efter detta kommer du att kunna verifiera resultaten och ange ytterligare information för Log Analytics som ska användas i analysen.

1. Markera texten i exempel posten som du vill fylla i det anpassade fältet.  Sedan visas en dialog ruta där du kan ange ett namn och en datatyp för fältet och utföra den första extraheringen.  Tecknen **\_ CF** läggs automatiskt till.
2. Klicka på **extrahera** för att utföra en analys av insamlade poster.  
3. I avsnitten **Sammanfattning** och **Sök Resultat** visas resultatet av extraheringen, så att du kan kontrol lera dess riktighet.  **Sammanfattning** visar de kriterier som används för att identifiera poster och ett antal för varje data värde som identifieras.  **Sök resultaten** innehåller en detaljerad lista över poster som matchar kriterierna.

### <a name="step-3--verify-accuracy-of-the-extract-and-create-custom-field"></a>Steg 3 – kontrol lera att extrahera och skapa anpassat fält är korrekt
När du har genomfört det första extraherings resultatet visas Log Analytics baserat på data som redan har samlats in.  Om resultatet ser korrekt ut kan du skapa det anpassade fältet utan ytterligare arbete.  Annars kan du förfina resultaten så att Log Analytics kan förbättra sin logik.

1. Om några värden i det första extraktet inte är korrekta, klickar du på **redigerings** ikonen bredvid en felaktig post och väljer **ändra den här** markeringen för att ändra valet.
2. Posten kopieras till avsnittet **Ytterligare exempel** under **huvud exemplet**.  Du kan justera markeringen här för att hjälpa Log Analytics förstå vad det har gjort.
3. Klicka på **extrahera** för att använda den nya informationen för att utvärdera alla befintliga poster.  Resultaten kan ändras för andra poster än den som du nyss ändrade, baserat på den nya intelligensen.
4. Fortsätt att lägga till korrigeringar tills alla poster i extraheringen korrekt identifierar data som ska fyllas i det nya anpassade fältet.
5. Klicka på **Spara extrahera** när du är nöjd med resultatet.  Det anpassade fältet har nu definierats, men det kommer inte att läggas till i några poster ännu.
6. Vänta tills nya poster som matchar de angivna villkoren samlas in och kör sedan loggs ökningen igen. Nya poster ska ha det anpassade fältet.
7. Använd det anpassade fältet som vilken annan post egenskap som helst.  Du kan använda den för att aggregera och gruppera data och även använda den för att skapa nya insikter.

## <a name="viewing-custom-fields"></a>Visa anpassade fält
Du kan visa en lista över alla anpassade fält i hanterings gruppen från menyn **Avancerade inställningar** i Log Analytics arbets ytan i Azure Portal.  Välj **data** och sedan **anpassade fält** för en lista med alla anpassade fält i din arbets yta.  

![Anpassade fält](media/custom-fields/list.png)

## <a name="removing-a-custom-field"></a>Ta bort ett anpassat fält
Det finns två sätt att ta bort ett anpassat fält.  Det första alternativet är alternativet **ta bort** för varje fält när du visar den fullständiga listan enligt beskrivningen ovan.  Den andra metoden är att hämta en post och klicka på knappen till vänster om fältet.  Menyn har ett alternativ för att ta bort det anpassade fältet.

## <a name="sample-walkthrough"></a>Exempelgenomgång
I följande avsnitt går vi igenom ett komplett exempel på hur du skapar ett anpassat fält.  I det här exemplet extraheras tjänst namnet i Windows-händelser som indikerar ett tjänst förändrings tillstånd.  Detta förlitar sig på händelser som skapats av Service Control Manager under system start på Windows-datorer.  Om du vill följa det här exemplet måste du samla in [informations händelser för system loggen](../agents/data-sources-windows-events.md).

Vi anger följande fråga för att returnera alla händelser från Service Control Manager som har händelse-ID 7036, vilket är händelsen som indikerar att en tjänst startas eller stoppas.

![Skärm bild som visar en fråga för en händelse källa och ID.](media/custom-fields/query.png)

Sedan väljer och expanderar du alla poster med händelse-ID 7036.

![Käll post](media/custom-fields/source-record.png)

Vi definierar anpassade fält genom att klicka på ellipsen bredvid den översta egenskapen.

![Extrahera fält](media/custom-fields/extract-fields.png)

**Guiden extrahering av fält** öppnas och fälten **EventLog** och **EventID** har marker ATS i **huvud exempel** kolumnen.  Detta anger att det anpassade fältet ska definieras för händelser från system loggen med händelse-ID 7036.  Detta räcker så att vi inte behöver välja några andra fält.

![Huvud exempel](media/custom-fields/main-example.png)

Vi markerar namnet på tjänsten i egenskapen **RenderedDescription** och använder **tjänsten** för att identifiera tjänst namnet.  Det anpassade fältet kommer att anropas **Service_CF**. Fält typen i det här fallet är en sträng, så vi kan lämna den oförändrad.

![Fält rubrik](media/custom-fields/field-title.png)

Vi ser att tjänst namnet identifieras korrekt för vissa poster, men inte för andra.   **Sök resultatet** visar att en del av namnet på **WMI-bildskärmskortet** inte har valts.  **Sammanfattningen** visar att en post har identifierat **moduler installations program** i stället för **Windows modules installations program**.  

![Skärm bild som visar delar av tjänst namnet markerat i rutan Sök Resultat och ett felaktigt tjänst namn har marker ATS i sammanfattningen.](media/custom-fields/search-results-01.png)

Vi börjar med **WMI Performance adapter-** posten.  Vi klickar på ikonen Redigera och **ändrar sedan den här markeringen**.  

![Ändra markering](media/custom-fields/modify-highlight.png)

Vi ökar fokus för att inkludera ordet **WMI** och kör sedan extraheringen igen.  

![Ytterligare exempel](media/custom-fields/additional-example-01.png)

Vi kan se att posterna för **WMI-bildskärmskort** har korrigerats, och Log Analytics även använda den informationen för att korrigera posterna för **Windows-modulens installations program**.

![Skärm bild som visar det fullständiga tjänst namnet markerat i rutan Sök Resultat och rätt tjänst namn är markerade i sammanfattningen.](media/custom-fields/search-results-02.png)

Nu kan vi köra en fråga som verifierar **Service_CF** skapas men ännu inte har lagts till i några poster. Det beror på att det anpassade fältet inte fungerar mot befintliga poster, så vi måste vänta på att nya poster ska samlas in.

![Antal initialer](media/custom-fields/initial-count.png)

När en stund har passerat så att nya händelser samlas in, kan vi se att fältet **Service_CF** nu läggs till i poster som matchar våra kriterier.

![Slutliga resultat](media/custom-fields/final-results.png)

Vi kan nu använda det anpassade fältet som vilken annan post egenskap som helst.  Vi illustrerar detta genom att skapa en fråga som grupperas efter det nya **Service_CF** fältet för att kontrol lera vilka tjänster som är mest aktiva.

![Gruppera efter fråga](media/custom-fields/query-group.png)

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [logg frågor](./log-query-overview.md) för att bygga frågor med anpassade fält för villkor.
* Övervaka [anpassade loggfiler](../agents/data-sources-custom-logs.md) som du tolkar med anpassade fält.