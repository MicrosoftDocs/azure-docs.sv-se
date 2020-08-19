---
title: Visualisera Azure NSG Flow-loggar – Power BI
titleSuffix: Azure Network Watcher
description: Lär dig hur du använder Power BI för att visualisera flödes loggar för nätverks säkerhets grupper så att du kan visa information om IP-trafik i Azure Network Watcher.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: a4c97b8baaf5265f984622e2dc47e6c8c714172a
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2020
ms.locfileid: "88272645"
---
# <a name="visualizing-network-security-group-flow-logs-with-power-bi"></a>Visualisering av flödes loggar för nätverks säkerhets grupper med Power BI

Flödes loggar för nätverks säkerhets grupper gör att du kan visa information om inkommande och utgående IP-trafik på nätverks säkerhets grupper. I dessa flödes loggar visas utgående och inkommande flöden per regel, vilket nätverkskort flödet avser, 5-tuple-information om flödet (käll-/mål-IP, käll-och mål Port, protokoll) och om trafiken tillåts eller nekas.

Det kan vara svårt att få insikter om flödes loggnings data genom att manuellt söka igenom loggfilerna. I den här artikeln tillhandahåller vi en lösning för att visualisera dina senaste flödes loggar och lär dig mer om trafik i nätverket.

> [!Warning]  
> Följande steg fungerar med Flow-loggar version 1. Mer information finns i [Introduktion till flödes loggning för nätverks säkerhets grupper](network-watcher-nsg-flow-logging-overview.md). Följande instruktioner fungerar inte med version 2 av loggfilerna utan ändringar.

## <a name="scenario"></a>Scenario

I följande scenario ansluter vi Power BI Station ära Skriv bordet till lagrings kontot som vi har konfigurerat som mottagare för våra NSG flödes loggnings data. När vi ansluter till vårt lagrings konto kan Power BI Ladda ned och parsa loggarna för att tillhandahålla en visuell representation av den trafik som loggas av nätverks säkerhets grupper.

Med hjälp av de visualiseringar som anges i mallen kan du undersöka:

* Främsta pratare
* Tids serie flödes data efter riktning och regel beslut
* Flöden efter nätverks gränssnittets MAC-adress
* Flöden efter NSG och regel
* Flöden efter mål Port

Den angivna mallen kan redige ras så att du kan ändra den för att lägga till nya data, visuella objekt eller redigera frågor som passar dina behov.

## <a name="setup"></a>Installation

Innan du börjar måste du ha nätverks säkerhets gruppens flödes loggning aktiverat på en eller flera nätverks säkerhets grupper i ditt konto. Anvisningar om hur du aktiverar nätverks säkerhets flödes loggar finns i följande artikel: [Introduktion till flödes loggning för nätverks säkerhets grupper](network-watcher-nsg-flow-logging-overview.md).

Du måste också ha Power BI Desktop-klienten installerad på datorn och tillräckligt med ledigt utrymme på datorn för att ladda ned och läsa in de loggdata som finns i ditt lagrings konto.

![Visio-diagram][1]

### <a name="steps"></a>Steg

1. Ladda ned och öppna följande Power BI mall i mallen för Power BI Desktop program [Network Watcher PowerBI Flow-loggar](https://aka.ms/networkwatcherpowerbiflowlogstemplate)
1. Ange de frågeparametrar som krävs
   1. **StorageAccountName** – anger namnet på det lagrings konto som innehåller de NSG-flödes loggar som du vill läsa in och visualisera.
   1. **NumberOfLogFiles** – anger antalet loggfiler som du vill hämta och visualisera i Power BI. Om till exempel 50 anges, de senaste loggfilerna för 50. Om vi har 2 NSG: er-aktiverade och konfigurerade för att skicka NSG Flow-loggar till det här kontot, kan de senaste 25 timmarna med loggar visas.

      ![Power BI-huvud][2]

1. Ange åtkomst nyckeln för ditt lagrings konto. Du kan hitta giltiga åtkomst nycklar genom att gå till ditt lagrings konto i Azure Portal och välja **åtkomst nycklar** på menyn Inställningar. Klicka på **Anslut** och Verkställ ändringarna.

    ![åtkomst nycklar][3]

    ![åtkomst nyckel 2][4]

4. Loggarna hämtas och tolkas och du kan nu använda de färdiga visuella objekten.

## <a name="understanding-the-visuals"></a>Förstå visualiseringarna

Anges i mallen är en uppsättning visuella objekt som hjälper dig att förstå NSG Flow-loggdata. Följande bilder visar ett exempel på hur instrument panelen ser ut när den fylls med data. Nedan granskar vi varje visuellt objekt i större detalj 

![powerbi][5]
 
De främsta Pratarna visar de IP-adresser som har initierat de flesta anslutningar under den angivna perioden. Storleken på rutorna motsvarar det relativa antalet anslutningar. 

![toptalkers][6]

I följande tids serie diagram visas antalet flöden under perioden. Den övre grafen segmenteras av flödes riktningen och det lägre värdet segmenteras av det beslut som fattas (Tillåt eller neka). Med det här visuella objektet kan du undersöka dina trafik trender över tid och var onormala toppar eller neka trafik eller trafik segmentering.

![flowsoverperiod][7]

I följande diagram visas flödena per nätverks gränssnitt, med de övre segmenten efter flödes riktning och de lägre segment som fattas. Med den här informationen kan du få insikter om vilka av dina virtuella datorer som kommunicerar mest i förhållande till andra och om trafik till en angiven virtuell dator tillåts eller nekas.

![flowspernic][8]

Följande ring diagram visar en nedbrytning av flöden per målport. Med den här informationen kan du Visa de vanligaste portarna som används inom den angivna perioden.

![toroid][9]

Följande stapeldiagram visar flödet efter NSG och regel. Med den här informationen kan du se NSG: er som ansvarar för den högsta trafiken och nedbrytning av trafik på en NSG enligt regel.

![barchart][10]
 
I följande informations diagram visas information om NSG: er som finns i loggarna, antalet flöden som har tagits emot under perioden och datum för den tidigaste loggen. Den här informationen ger dig en uppfattning om vilka NSG: er som loggas och datum intervallet för flöden.

![infochart1][11]

![infochart2][12]

Den här mallen innehåller följande utsnitt så att du endast kan se de data som du är mest intresse rad av. Du kan filtrera efter resurs grupper, NSG: er och regler. Du kan också filtrera på 5-tuple-information, beslut och tidpunkten då loggen skrevs.

![utsnitt][13]

## <a name="conclusion"></a>Slutsats

Vi visade i det här fallet genom att använda flödes loggar för nätverks säkerhets grupper som tillhandahålls av Network Watcher och Power BI, och vi kan visualisera och förstå trafiken. Med den angivna mallen kan Power BI Ladda ned loggarna direkt från lagringen och bearbeta dem lokalt. Hur lång tid det tar att läsa in mallen beror på antalet begärda filer och den totala storleken på hämtade filer.

Du kan anpassa den här mallen efter dina behov. Det finns många olika sätt som du kan använda för Power BI med flödes loggar för nätverks säkerhets grupper. 

## <a name="notes"></a>Anteckningar

* Loggar som standard lagras i `https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/`

    * Om det finns andra data i en annan katalog, så måste de frågor som krävs för att hämta och bearbeta data ändras.

* Den angivna mallen rekommenderas inte för användning med mer än 1 GB loggar.

* Om du har en stor mängd loggar rekommenderar vi att du undersöker en lösning med hjälp av ett annat data lager som Data Lake eller SQL Server.

## <a name="next-steps"></a>Efterföljande moment

Lär dig hur du visualiserar dina NSG Flow-loggar med den elastiska stacken genom att besöka [visualisera Azure Network WATCHER NSG flödes loggar med verktyg för öppen källkod](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)

[1]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure1.png
[2]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure2.png
[3]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure3.png
[4]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure4.png
[5]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure5.png
[6]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure6.png
[7]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure7.png
[8]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure8.png
[9]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure9.png
[10]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure10.png
[11]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure11.png
[12]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure12.png
[13]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure13.png
