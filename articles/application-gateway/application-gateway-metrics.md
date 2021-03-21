---
title: Azure Monitor mått för Application Gateway
description: Lär dig hur du använder mått för att övervaka prestanda för Application Gateway
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: article
ms.date: 06/06/2020
ms.author: surmb
ms.openlocfilehash: 9faa3a284aa7151880526c1ee70cfadc3dbf3089
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100576120"
---
# <a name="metrics-for-application-gateway"></a>Mått för Application Gateway

Application Gateway publicerar data punkter, som kallas mått, för att [Azure Monitor](../azure-monitor/overview.md) för prestanda för dina Application Gateway-och Server dels instanser. Dessa mått är numeriska värden i en ordnad uppsättning av Time Series-data som beskriver någon aspekt av programgatewayen vid en viss tidpunkt. Om det finns begär Anden som flödar genom Application Gateway, mäter den och skickar sina mått i intervall om 60 sekunder. Om det inte finns några begär Anden som flödar genom Application Gateway eller inga data för ett mått, rapporteras inte måttet. Mer information finns i [Azure Monitor mått](../azure-monitor/essentials/data-platform-metrics.md).

## <a name="metrics-supported-by-application-gateway-v2-sku"></a>Mått som stöds av Application Gateway v2 SKU

### <a name="timing-metrics"></a>Tids mått

Application Gateway innehåller flera inbyggda tids mått för begäran och svar, som är mätt i millisekunder. 

![Diagram över tids mått för Application Gateway.](./media/application-gateway-metrics/application-gateway-metrics.png)

> [!NOTE]
>
> Om det finns fler än en lyssnare i Application Gateway ska du alltid filtrera efter *Listener* -dimension samtidigt som du jämför olika svars tids mått för att få en meningsfull härledning.

- **Server dels anslutnings tid**

  Åtgången tid för att upprätta en anslutning till backend-programmet. 

  Detta omfattar nätverks svars tiden och den tid det tar för backend-serverns TCP-stack att upprätta nya anslutningar. Om TLS används omfattar det även den tid som krävs för att utföra handskakningen. 

- **Svars tid för första byte för Server del**

  Tidsintervall mellan början av att upprätta en anslutning till backend-servern och ta emot den första byten i svars huvudet. 

  Detta uppskattar summan av *Server dels anslutnings tiden*, tid det tar för en begäran att komma åt Server delen från Application Gateway, tid det tar för backend-program att svara (den tid det tog att generera innehåll, potentiellt hämtning av databas frågor) och den tid det tar för den första byten av svaret att uppnå Application Gateway från Server delen.

- **Svars tid för senaste byte för Server delen**

  Tidsintervall mellan början av att upprätta en anslutning till backend-servern och ta emot den sista byten i svars texten. 

  Det här är den ungefärliga summan av *Svarstid för första byte från serverdelen* och dataöverföringstiden (det här talet kan variera beroende på storleken på objektet som begärs och servernätverkets svarstid).

- **Total tid för Application Gateway**

  Genomsnittlig tid det tar för en begäran att tas emot, bearbetas och dess svar skickas. 

  Detta är intervallet från den tidpunkt då Application Gateway tar emot den första byten i HTTP-begäran till den tidpunkt då den senaste svars byte har skickats till klienten. Detta inkluderar bearbetnings tiden som det tar Application Gateway, *Server delens svars tid för senaste byte*, tid det tar för Application Gateway att skicka alla svar och den aktuella *klienten*.

- **Klient-/klient**

  Genomsnittlig fördröjning mellan klienter och Application Gateway.



Dessa mått kan användas för att avgöra om den observerade minskningen beror på klient nätverket, Application Gateway prestanda, backend-nätverket och Server dels serverns TCP-stack-mättnad, prestanda för backend-program eller stor fil storlek.

Om det till exempel finns en topp i den *första byte tiden för svars tid* , men tiden för *anslutnings tiden för Server delen* är stabil, kan det härledas att programgatewayen till Server dels svars tiden och den tid det tar att upprätta anslutningen är stabil och insamling orsakas av en ökning av svars tiden för backend-programmet. Å andra sidan, om inökningen i *svars tiden för första byte-bytet* är kopplad till en motsvarande insamling i *Server dels anslutnings tiden*, kan det härledas att antingen nätverket mellan Application Gateway-och backend-servern eller Server dels serverns TCP-stack har mätt. 

Om du märker att det finns en topp i *svars tiden för sista byte i Server delen* , men *svars tiden för första byte-databytet* är stabil, kan det härledas att insamling är på grund av en större fil som begärs.

På samma sätt, om den *totala tiden för programgatewayen* har en topp men *svars tiden för Server delens svars tid* är stabil, kan det antingen vara ett tecken på en prestanda Flask hals i Application Gateway eller en Flask hals i nätverket mellan klient och Application Gateway. Dessutom, om *klientens* sökklass också har en motsvarande insamling, anger det att försämringen beror på nätverket mellan klient och Application Gateway.

### <a name="application-gateway-metrics"></a>Application Gateway mått

För Application Gateway är följande mått tillgängliga:

- **Mottagna byte**

   Antal byte som tagits emot av Application Gateway från klienterna

- **Skickade byte**

   Antal byte som har skickats av Application Gateway till klienterna

- **Klientens TLS-protokoll**

   Antal TLS-och icke-TLS-begäranden som initieras av klienten som upprättade anslutning till Application Gateway. Om du vill visa TLS-protokollets distribution filtrerar du efter dimension TLS-protokollet.

- **Aktuella kapacitetsenheter**

   Antalet kapacitetsenheter som förbrukas för att belastningsutjämna trafiken. Det finns tre faktorer för kapacitets enhets beräknings enhet, beständiga anslutningar och data flöde. Varje kapacitets enhet består av högst: 1 beräknings enhet eller 2500 beständiga anslutningar eller 2,22 – Mbps-dataflöde.

- **Aktuella beräknings enheter**

   Antal förbrukade processor kapacitet. TLS-anslutningar per sekund, beräkningar för omskrivning av webbadresser och bearbetning av WAF-regler är faktorer som påverkar beräkningsenheter. 

- **Aktuella anslutningar**

   Det totala antalet samtidiga anslutningar som är aktiva från klienter till Application Gateway
   
- **Uppskattade fakturerade kapacitetsenheter**

  Med v2 SKU:n drivs prissättningsmodellen av förbrukning. Kapacitetsenheter mäter förbrukningsbaserade kostnader som debiteras utöver den fasta kostnaden. *Uppskattade enheter för fakturerings kapacitet* anger antalet kapacitets enheter som faktureringen beräknas med. Detta beräknas som det större värdet mellan *Aktuella kapacitetsenheter* (kapacitetsenheter som krävs för att belastningsutjämna trafiken) och *Fasta fakturerbara kapacitetsenheter* (lägsta antalet kapacitetsenheter som är etablerade).

- **Misslyckade förfrågningar**

  Antal begär Anden som Application Gateway har hanterat med 5xx Server-felkoder. Detta inkluderar de 5xx-koder som genereras från Application Gateway samt de 5xx-koder som genereras från Server delen. Antalet begär Anden kan filtreras ytterligare för att visa antal per/specifika Server dels pool – http-inställnings kombination.
   
- **Fasta fakturerbara kapacitetsenheter**

  Det minsta antalet kapacitetsenheter som hålls etablerade enligt inställningen *Minsta antalet skalningsenheter* (en instans är tio kapacitetsenheter) i konfigurationen för Application Gateway.
   
 - **Nya anslutningar per sekund**

   Genomsnittligt antal nya TCP-anslutningar per sekund som upprättats från klienter till Application Gateway och från Application Gateway till Server dels medlemmar.


- **Svars status**

   HTTP-svarets status returnerades av Application Gateway. Svars status kod distributionen kan kategoriseras ytterligare för att Visa svar i 2xx-, 3xx-, 4xx-och 5xx-kategorier.

- **Dataflöde**

   Antal byte per sekund som Application Gateway har betjänat

- **Totalt antal förfrågningar**

   Antal lyckade förfrågningar som Application Gateway har betjänat. Antalet begär Anden kan filtreras ytterligare för att visa antal per/specifika Server dels pool – http-inställnings kombination.

### <a name="backend-metrics"></a>Server dels mått

För Application Gateway är följande mått tillgängliga:

- **Svars status för Server del**

  Antal HTTP-svars status koder som returneras av Server delarna. Detta omfattar inte några svars koder som genereras av Application Gateway. Svars status kod distributionen kan kategoriseras ytterligare för att Visa svar i 2xx-, 3xx-, 4xx-och 5xx-kategorier.

- **Antal felfria värdar**

  Antalet Server delar som bedöms felfritt av hälso avsökningen. Du kan filtrera efter en pool per server del för att visa antalet felfria värdar i en viss backend-pool.

- **Antal värdar med fel**

  Antalet Server delar som avvisats i hälso avsökningen. Du kan filtrera per server dels pool för att visa antalet felaktiga värdar i en viss backend-pool.
  
- **Begär Anden per minut per felfri värd**

  Det genomsnittliga antalet förfrågningar som tagits emot av varje felfritt medlem i en backend-pool på en minut. Du måste ange backend-poolen med hjälp av *BackendPool HttpSettings* -dimensionen.  
  

## <a name="metrics-supported-by-application-gateway-v1-sku"></a>Mått som stöds av Application Gateway v1 SKU

### <a name="application-gateway-metrics"></a>Application Gateway mått

För Application Gateway är följande mått tillgängliga:

- **CPU-användning**

  Visar användningen av de CPU:er som har allokerats till Application Gateway.  Under normala förhållanden bör CPU-användningen inte regelbundet överskrida 90 %,eftersom detta kan orsaka långsamma svarstider på de webbplatser som Application Gateway är värd för, vilket kan vara frustrerande för kunderna. Du kan indirekt styra eller förbättra CPU-användningen genom att ändra konfigurationen för Application Gateway genom att öka antalet instanser eller genom att flytta till en större SKU-storlek, eller göra båda.

- **Aktuella anslutningar**

  Antal aktuella anslutningar som upprättats med Application Gateway

- **Misslyckade förfrågningar**

  Antal begär Anden som misslyckades på grund av anslutnings problem. Det här antalet inkluderar begär Anden som har misslyckats på grund av att HTTP-inställningen "begär timeout" överskrids och begär Anden som misslyckats på grund av anslutnings problem mellan Application Gateway och Server del. Det här antalet inkluderar inte fel på grund av att det inte finns någon felfri Server. 4xx-och 5XX-svar från Server delen betraktas också inte som en del av det här måttet.

- **Svars status**

  HTTP-svarets status returnerades av Application Gateway. Svars status kod distributionen kan kategoriseras ytterligare för att Visa svar i 2xx-, 3xx-, 4xx-och 5xx-kategorier.

- **Dataflöde**

  Antal byte per sekund som Application Gateway har betjänat

- **Totalt antal förfrågningar**

  Antal lyckade förfrågningar som Application Gateway har betjänat. Antalet begär Anden kan filtreras ytterligare för att visa antal per/specifika Server dels pool – http-inställnings kombination.

- **Antal blockerade förfrågningar för webb program brand vägg**
- **Webb program brand vägg för blockerade begär Anden**
- **Brand vägg för total regel distribution i webb program**

### <a name="backend-metrics"></a>Server dels mått

För Application Gateway är följande mått tillgängliga:

- **Antal felfria värdar**

  Antalet Server delar som bedöms felfritt av hälso avsökningen. Du kan filtrera efter en pool per server del för att visa antalet felfria värdar i en viss backend-pool.

- **Antal värdar med fel**

  Antalet Server delar som avvisats i hälso avsökningen. Du kan filtrera per server dels pool för att visa antalet felaktiga värdar i en viss backend-pool.

## <a name="metrics-visualization"></a>Mått visualisering

Bläddra till en Application Gateway, under **övervakning** Välj **mått**. Om du vill visa de tillgängliga värdena väljer du listrutan **MÅTT**.

I följande bild visas ett exempel med tre mått som visas under de senaste 30 minuterna:

:::image type="content" source="media/application-gateway-diagnostics/figure5.png" alt-text="Metric-vy." lightbox="media/application-gateway-diagnostics/figure5-lb.png":::

Om du vill se en aktuell lista över mått, se [mått som stöds med Azure Monitor](../azure-monitor/essentials/metrics-supported.md).

### <a name="alert-rules-on-metrics"></a>Aviserings regler för mått

Du kan starta aviserings regler baserat på mått för en resurs. En avisering kan t. ex. anropa en webhook eller skicka en e-post till en administratör om data flödet för programgatewayen är över, under eller vid ett tröskelvärde under en angiven period.

Följande exempel visar hur du skapar en varnings regel som skickar ett e-postmeddelande till en administratör när data flödet har överträtt ett tröskelvärde:

1. Välj **Lägg till mått avisering** för att öppna sidan **Lägg till regel** . Du kan också komma åt den här sidan från sidan mått.

   ![Knappen Lägg till mått avisering][6]

2. På sidan **Lägg till regel** fyller du i avsnitten namn, villkor och meddelande och väljer **OK**.

   * I **villkors** väljaren väljer du något av de fyra värdena: **större än**, **större än eller lika med**, **mindre än** eller **mindre än eller lika** med.

   * I **period** väljaren väljer du en period på fem minuter till sex timmar.

   * Om du väljer **e-postägare, deltagare och läsare** kan e-postmeddelandet vara dynamiskt baserat på de användare som har åtkomst till resursen. Annars kan du ange en kommaavgränsad lista med användare i rutan **ytterligare administratörs e-post (er)** .

   ![Sidan Lägg till regel][7]

Om tröskelvärdet överskrids kommer ett e-postmeddelande som liknar det som finns i följande bild att bli:

![E-postmeddelande för överträtt tröskel][8]

En lista med aviseringar visas när du har skapat en mått-avisering. Den ger en översikt över alla aviserings regler.

![Lista över aviseringar och regler][9]

Mer information om aviserings aviseringar finns i [ta emot aviseringar](../azure-monitor/alerts/alerts-overview.md).

Om du vill veta mer om webhookar och hur du kan använda dem med aviseringar kan du gå till [Konfigurera en webhook på en Azure Metric-avisering](../azure-monitor/alerts/alerts-webhooks.md).

## <a name="next-steps"></a>Nästa steg

* Visualisera räknare och händelse loggar med hjälp av [Azure Monitor loggar](../azure-monitor/insights/azure-networking-analytics.md).
* [Visualisera din Azure aktivitets logg med Power BI](https://powerbi.microsoft.com/blog/monitor-azure-audit-logs-with-power-bi/) blogg inlägg.
* [Visa och analysera Azure aktivitets loggar i Power BI och fler](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) blogg inlägg.

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