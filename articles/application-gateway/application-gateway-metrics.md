---
title: Azure Monitor mått för Application Gateway
description: Lär dig hur du använder mått för att övervaka programgatewayens prestanda
services: application-gateway
author: azhar2005
ms.service: application-gateway
ms.topic: article
ms.date: 04/19/2021
ms.author: azhussai
ms.openlocfilehash: 615db7e8d53e397755ae318d171dab1eab9ec6c6
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107727816"
---
# <a name="metrics-for-application-gateway"></a>Mått för Application Gateway

Application Gateway publicerar datapunkter, som kallas mått, [för att Azure Monitor](../azure-monitor/overview.md) prestanda för dina Application Gateway- och backend-instanser. Dessa mått är numeriska värden i en ordnad uppsättning tidsseriedata som beskriver någon aspekt av din programgateway vid en viss tidpunkt. Om det finns begäranden som flödar genom Application Gateway mäter den och skickar dess mått i 60-sekundersintervall. Om det inte finns några begäranden som passerar genom Application Gateway eller inga data för ett mått rapporteras inte måttet. Mer information finns i [Azure Monitor mått](../azure-monitor/essentials/data-platform-metrics.md).

## <a name="metrics-supported-by-application-gateway-v2-sku"></a>Mått som stöds av Application Gateway V2 SKU

### <a name="timing-metrics"></a>Tidsmått

Application Gateway har flera inbyggda tidsmått relaterade till begäran och svar, som alla mäts i millisekunder. 

:::image type="content" source="./media/application-gateway-metrics/application-gateway-metrics.png" alt-text="[Diagram över tidsmått för Application Gateway" border="false":::

> [!NOTE]
>
> Om det finns fler än en lyssnare i Application Gateway  filtrerar du alltid efter lyssnardimension och jämför olika svarstidsmått för att få meningsfull slutsatsledning.

- **Anslutningstid för backend**

  Tid som ägnats åt att upprätta en anslutning till backend-programmet. 

  Detta inkluderar nätverksfördröjningen samt den tid det tar för backend-serverns TCP-stack att upprätta nya anslutningar. Om TLS används omfattar det även den tid som krävs för att utföra handskakningen. 

- **Svarstid för första byte i backend**

  Tidsintervallet mellan början av upprättandet av en anslutning till backend-servern och mottagandet av den första byten i svarshuvudet. 

  Detta approximeras summan av tiden för serverd-anslutning, den tid det tar för begäran att nå serverservern från Application Gateway, hur lång tid det tar för serverprogrammet att svara (den tid det tog för servern att generera innehåll, potentiellt hämta databasfrågor) och den tid det tar för den första byten av svaret att nå Application Gateway från serverservern.

- **Svarstid för sista byte i backend**

  Tidsintervall mellan början av upprättandet av en anslutning till serverdelen och mottagandet av den sista byten i svarstexten. 

  Det här är den ungefärliga summan av *Svarstid för första byte från serverdelen* och dataöverföringstiden (det här talet kan variera beroende på storleken på objektet som begärs och servernätverkets svarstid).

- **Total tid för Application Gateway**

  Genomsnittlig tid det tar för en begäran att tas emot, bearbetas och dess svar skickas. 

  Det här är intervallet från den tidpunkt Application Gateway tar emot den första byten av HTTP-begäran till den tidpunkt då den sista svarsbytet har skickats till klienten. Detta inkluderar den bearbetningstid som Application Gateway, svarstiden för senaste byte från *backend,* den tid det tar för Application Gateway att skicka alla svar och *Klient-RTT*.

- **Klient-RTT**

  Genomsnittlig tur och retur-tid mellan klienter och Application Gateway.



Dessa mått kan användas för att avgöra om den observerade långsamningen beror på klientnätverket, Application Gateway-prestanda, servernätverket och serverserverns TCP-stackmättnad, serversidans programprestanda eller en stor filstorlek.

Om det till exempel finns en topp i trenden för svarstid för första byte från *backend* men tidstrenden för *backend-anslutning* är stabil, kan det här härledas att Svarstid för Application Gateway till backend och den tid det tar att upprätta anslutningen är stabil och att toppen orsakas av en ökning av svarstiden för backend-programmet. Å andra sidan, om toppen i *svarstiden* för första *byte* från server från server är associerad med en motsvarande topp i tid för server ansluter, så kan det härledas att antingen nätverket mellan Application Gateway och backend-servern eller serverserverns TCP-stack har mättnad. 

Om du märker en topp i svarstiden för senaste byte från *backend,* men svarstiden för första byte från *backend* är stabil, kan det härledas att topparna beror på att en större fil begärs.

Om den totala tiden för *Application Gateway* har en topp men svarstiden för senaste byte i *backend* är stabil kan det antingen vara ett tecken på en prestandaflaskhals vid Application Gateway eller en flaskhals i nätverket mellan klienten och Application Gateway. Om klientens *RTT* också har en motsvarande topp indikerar det dessutom att försämringen beror på nätverket mellan klienten och Application Gateway.

### <a name="application-gateway-metrics"></a>Application Gateway mått

Följande Application Gateway mått är tillgängliga för alla:

- **Mottagna byte**

   Antal byte som tagits emot av Application Gateway från klienterna

- **Skickade byte**

   Antal byte som skickats av Application Gateway till klienterna

- **TLS-klientprotokoll**

   Antal TLS- och icke-TLS-begäranden som initierats av klienten som upprättat en anslutning till Application Gateway. Om du vill visa TLS-protokolldistribution filtrerar du efter dimensionenSLS-protokoll.

- **Aktuella kapacitetsenheter**

   Antalet kapacitetsenheter som förbrukas för att belastningsutjämna trafiken. Det finns tre determinanter för kapacitetsenhet – beräkningsenhet, beständiga anslutningar och dataflöde. Varje kapacitetsenhet består som mest av: 1 beräkningsenhet, 2 500 beständiga anslutningar eller dataflöde på 2,22 Mbit/s.

- **Aktuella beräkningsenheter**

   Antal förbrukad processorkapacitet. TLS-anslutningar per sekund, beräkningar för omskrivning av webbadresser och bearbetning av WAF-regler är faktorer som påverkar beräkningsenheter. 

- **Aktuella anslutningar**

   Det totala antalet aktiva samtidiga anslutningar från klienter till Application Gateway
   
- **Uppskattade fakturerade kapacitetsenheter**

  Med v2 SKU:n drivs prissättningsmodellen av förbrukning. Kapacitetsenheter mäter förbrukningsbaserade kostnader som debiteras utöver den fasta kostnaden. *Uppskattade fakturerade kapacitetsenheter* anger antalet kapacitetsenheter som faktureringen beräknas med. Detta beräknas som det större värdet mellan *Aktuella kapacitetsenheter* (kapacitetsenheter som krävs för att belastningsutjämna trafiken) och *Fasta fakturerbara kapacitetsenheter* (lägsta antalet kapacitetsenheter som är etablerade).

- **Misslyckade begäranden**

  Antal begäranden som har Application Gateway 5xx-serverfelkoder. Detta inkluderar de 5xx-koder som genereras från Application Gateway samt de 5xx-koder som genereras från backend. Antalet begäranden kan filtreras ytterligare för att visa antalet per varje/specifik kombination av backend-pool-http-inställning.
   
- **Fasta fakturerbara kapacitetsenheter**

  Det minsta antalet kapacitetsenheter som hålls etablerade enligt inställningen *Minsta antalet skalningsenheter* (en instans är tio kapacitetsenheter) i konfigurationen för Application Gateway.
   
 - **Nya anslutningar per sekund**

   Det genomsnittliga antalet nya TCP-anslutningar per sekund som upprättats från klienter till Application Gateway och från Application Gateway till backend-medlemmarna.


- **Svarsstatus**

   HTTP-svarsstatus som returneras av Application Gateway. Distributionen av svarsstatuskod kan kategoriseras ytterligare för att visa svar i kategorierna 2xx, 3xx, 4xx och 5xx.

- **Dataflöde**

   Antal byte per sekund som Application Gateway har betjänat

- **Totalt antal begäranden**

   Antal lyckade begäranden som Application Gateway har betjänat. Antalet begäranden kan filtreras ytterligare för att visa antal per varje/specifik kombination av backend-pool-http-inställning.

### <a name="backend-metrics"></a>Backend-mått

Följande Application Gateway är tillgängliga för följande mått:

- **Svarsstatus för backend**

  Antal HTTP-svarsstatuskoder som returneras av backends. Detta omfattar inte några svarskoder som genereras av Application Gateway. Distributionen av svarsstatuskod kan kategoriseras ytterligare för att visa svar i kategorierna 2xx, 3xx, 4xx och 5xx.

- **Antal felfria värdar**

  Antalet backends som bestäms felfritt av hälsoavsökningen. Du kan filtrera per serverpool för att visa antalet felfria värdar i en specifik serverpool.

- **Antal värdar med fel**

  Antalet backends som bestäms som felaktiga av hälsoavsökningen. Du kan filtrera på en per serverpool om du vill visa antalet värdar med feltillstånd i en specifik serverpool.
  
- **Begäranden per minut per felfri värd**

  Det genomsnittliga antalet begäranden som tas emot av varje felfri medlem i en backend-pool på en minut. Du måste ange backend-poolen med hjälp av *dimensionen BackendPool HttpSettings.*  
  

## <a name="metrics-supported-by-application-gateway-v1-sku"></a>Mått som stöds av Application Gateway V1 SKU

### <a name="application-gateway-metrics"></a>Application Gateway mått

Följande Application Gateway är tillgängliga för alla:

- **CPU-användning**

  Visar användningen av de CPU:er som har allokerats till Application Gateway.  Under normala förhållanden bör CPU-användningen inte regelbundet överskrida 90 %,eftersom detta kan orsaka långsamma svarstider på de webbplatser som Application Gateway är värd för, vilket kan vara frustrerande för kunderna. Du kan indirekt styra eller förbättra CPU-användningen genom att ändra konfigurationen för Application Gateway genom att öka antalet instanser eller genom att flytta till en större SKU-storlek, eller göra båda.

- **Aktuella anslutningar**

  Antal aktuella anslutningar som upprättats med Application Gateway

- **Misslyckade begäranden**

  Antal begäranden som misslyckats på grund av anslutningsproblem. Det här antalet inkluderar begäranden som misslyckades på grund av att HTTP-inställningen "Begärande-time out" överstegs och begäranden som misslyckades på grund av anslutningsproblem mellan Application Gateway och backend. Det här antalet inkluderar inte fel på grund av att ingen felfri backend är tillgänglig. 4xx- och 5xx-svar från backend betraktas inte heller som en del av det här måttet.

- **Svarsstatus**

  HTTP-svarsstatus som returneras av Application Gateway. Fördelningen av svarsstatuskod kan kategoriseras ytterligare för att visa svar i kategorierna 2xx, 3xx, 4xx och 5xx.

- **Dataflöde**

  Antal byte per sekund som Application Gateway har betjänat

- **Totalt antal begäranden**

  Antal lyckade begäranden som Application Gateway har betjänat. Antalet begäranden kan filtreras ytterligare för att visa antalet per varje/specifik kombination av backend-pool-http-inställning.

- **Web Application Firewall antal blockerade begäranden**
- **Web Application Firewall distribution av blockerade begäranden**
- **Web Application Firewall total regelfördelning**

### <a name="backend-metrics"></a>Backend-mått

Följande Application Gateway är tillgängliga för följande mått:

- **Antal felfria värdar**

  Antalet backends som bestäms felfritt av hälsoavsökningen. Du kan filtrera per serverpool för att visa antalet felfria värdar i en specifik serverpool.

- **Antal värdar med fel**

  Antalet backends som bestäms som felaktiga av hälsoavsökningen. Du kan filtrera på en per serverpool om du vill visa antalet värdar med feltillstånd i en specifik serverpool.

## <a name="metrics-visualization"></a>Visualisering av mått

Bläddra till en programgateway under **Övervakning** och **välj Mått.** Om du vill visa de tillgängliga värdena väljer du listrutan **MÅTT**.

I följande bild visas ett exempel med tre mått som visas under de senaste 30 minuterna:

:::image type="content" source="media/application-gateway-diagnostics/figure5.png" alt-text="Måttvy." lightbox="media/application-gateway-diagnostics/figure5-lb.png":::

En aktuell lista över mått finns i Mått som stöds [med Azure Monitor](../azure-monitor/essentials/metrics-supported.md).

### <a name="alert-rules-on-metrics"></a>Aviseringsregler för mått

Du kan starta aviseringsregler baserat på mått för en resurs. En avisering kan till exempel anropa en webhook eller skicka ett e-postmeddelande till en administratör om dataflödet för programgatewayen är över, under eller vid ett tröskelvärde för en angiven period.

I följande exempel går vi igenom hur du skapar en aviseringsregel som skickar ett e-postmeddelande till en administratör när dataflödet har överträdt ett tröskelvärde:

1. välj **Lägg till måttavisering** för att öppna **sidan Lägg till** regel. Du kan också nå den här sidan från måttsidan.

   ![Knappen "Lägg till måttavisering"][6]

2. På sidan **Lägg till** regel fyller du i avsnitten namn, villkor och meddela och väljer **OK.**

   * I **villkorsväljaren** väljer du något av de fyra **värdena:** Större än **,** Större än eller lika med , **Mindre än** eller Mindre än eller **lika med**.

   * I **periodväljaren** väljer du en period från fem minuter till sex timmar.

   * Om du väljer **E-postägare, deltagare** och läsare kan e-postmeddelandet vara dynamiskt baserat på de användare som har åtkomst till resursen. Annars kan du ange en kommaavgränsad lista över användare i **rutan Ytterligare e-postadresser för** administratörer.

   ![Sidan Lägg till regel][7]

Om tröskelvärdet överträds tas ett e-postmeddelande som liknar det i följande bild emot:

![E-post för tröskelvärdet överträds][8]

En lista över aviseringar visas när du har skapat en måttavisering. Den ger en översikt över alla aviseringsregler.

![Lista över aviseringar och regler][9]

Mer information om aviseringsmeddelanden finns i Ta [emot aviseringsmeddelanden.](../azure-monitor/alerts/alerts-overview.md)

Mer information om webhooks och hur du kan använda dem med aviseringar finns i [Konfigurera en webhook för en Azure-måttavisering](../azure-monitor/alerts/alerts-webhooks.md).

## <a name="next-steps"></a>Nästa steg

* Visualisera räknar- och händelseloggar med hjälp [Azure Monitor loggar](../azure-monitor/insights/azure-networking-analytics.md).
* [Visualisera din Azure-aktivitetslogg Power BI](https://powerbi.microsoft.com/blog/monitor-azure-audit-logs-with-power-bi/) blogginlägget.
* [Visa och analysera Azure-aktivitetsloggar i Power BI och mer](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) blogginlägg.

[1]: ./media/application-gateway-diagnostics/figure1.png
[2]: ./media/application-gateway-diagnostics/figure2.png
[3]: ./media/application-gateway-diagnostics/figure3.png
[4]: ./media/application-gateway-diagnostics/figure4.png
[5]: ./media/application-gateway-diagnostics/figure5.png
[6]: ./media/application-gateway-diagnostics/figure6.png
[7]: ./media/application-gateway-diagnostics/figure7.png
[8]: ./media/application-gateway-diagnostics/figure8.png
[9]: ./media/application-gateway-diagnostics/figure9.png
[10]: ./media/application-gateway-diagnostics/figure10.png
