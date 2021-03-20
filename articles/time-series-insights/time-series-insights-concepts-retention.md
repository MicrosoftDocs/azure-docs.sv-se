---
title: Förstå data kvarhållning i din miljö – Azure Time Series Insight | Microsoft Docs
description: I den här artikeln beskrivs två inställningar som styr datakvarhållning i Azure Time Series Insightss miljön.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.reviewer: jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/29/2020
ms.custom: seodec18
ms.openlocfilehash: 4f236679d0662df852581a6a8408ed6bc0d4e3fe
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91535697"
---
# <a name="understand-data-retention-in-azure-time-series-insights-gen1"></a>Förstå data kvarhållning i Azure Time Series Insights gen1

> [!CAUTION]
> Det här är en gen1-artikel.

I den här artikeln beskrivs två primära inställningar som påverkar datakvarhållning i Azure Time Series Insightss miljön.

## <a name="video"></a>Video

### <a name="the-following-video-summarizes-azure-time-series-insights-data-retention-and-how-to-plan-for-itbr"></a>Följande video sammanfattar Azure Time Series Insights datakvarhållning och hur du planerar för den.</br>

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

Var och en av dina Azure Time Series Insights miljöer har en inställning som styr **tiden för data lagring**. Värdet sträcker sig från 1 till 400 dagar. Data tas bort baserat på miljöns lagrings kapacitet eller varaktigheten för kvarhållning, beroende på vilket som kommer först.

Dessutom har Azure Time Series Insights miljön en gräns för **lagrings gränsen överskreds** . Den styr ingångs-och rensnings beteende när den maximala kapaciteten för en miljö nås. Du kan välja mellan två beteenden när du konfigurerar den:

- **Rensa gamla data** (standard)  
- **Pausa ingress**

> [!NOTE]
> Som standard konfigureras kvarhållning för att **Rensa gamla data** när du skapar en ny miljö. Den här inställningen kan växlas efter behov efter att du skapat den Azure Portal, på sidan **Konfigurera** i Azure Time Series Insightss miljön.
>
> - Information om hur du konfigurerar bevarande principer finns [i Konfigurera kvarhållning i Azure Time Series Insights](time-series-insights-how-to-configure-retention.md).

Båda principerna för data lagring beskrivs mer detaljerat nedan.

## <a name="purge-old-data"></a>Rensa gamla data

- **Rensa gamla data** är standardinställningen för Azure Time Series Insights miljöer.  
- **Rensa gamla data** rekommenderas om användarna alltid vill ha sina *senaste data* i sin Azure Time Series Insightss miljö.
- Inställningen **Rensa gammal data** *rensar* data när miljöns gränser (kvarhållning, storlek eller antal, beroende på vad som kommer först) har nåtts. Kvarhållning är inställt på 30 dagar som standard.
- De äldsta inmatade data rensas först (metoden "först in först ut").

### <a name="example-one"></a>Exempel en

Överväg en exempel miljö med kvarhållning, Fortsätt ingångs- **och rensa gamla data**:

**Tiden för datakvarhållning** anges till 400 dagar. **Kapaciteten** anges till S1-enhet, som innehåller 30 GB total kapacitet. Vi antar att inkommande data ackumuleras till 500 MB varje dag i genomsnitt. Den här miljön kan bara behålla 60 dagar av data som har fått frekvensen av inkommande data, eftersom den maximala kapaciteten uppnås med 60 dagar. Inkommande data ackumuleras som: 500 MB varje dag x 60 dagar = 30 GB.

På 61st dag visar miljön de nyaste data, men rensar de äldsta data som är äldre än 60 dagar. Rensningen gör plats för den nya data strömningen i, så att nya data kan fortsätta att utforskas. Om användaren vill behålla data längre kan de öka storleken på miljön genom att lägga till ytterligare enheter eller så kan de skicka mindre data.  

### <a name="example-two"></a>Exempel två

Betrakta en miljö som även konfigurerat kvarhållning, Fortsätt ingångs- **och rensa gamla data**. I det här exemplet anges **data kvarhållning** till ett lägre värde på 180 dagar. **Kapaciteten** anges till S1-enhet, som innehåller 30 GB total kapacitet. För att kunna lagra data i hela 180 dagar kan de dagliga ingångarna inte överstiga 0,166 GB (166 MB) per dag.  

När den här miljöns dagliga ingångs hastighet överskrider 0,166 GB per dag, kan data inte lagras i 180 dagar, eftersom vissa data rensas. Överväg samma miljö under tiden som tiden är upptagen. Anta att miljöns ingångs frekvens kan öka till en genomsnittlig 0,189 GB per dag. I den upptagna tids perioden behålls cirka 158 dagars data (30 GB/0.189 = 158,73 dagars kvarhållning). Den här tiden är kortare än den önskade tids perioden för datakvarhållning.

## <a name="pause-ingress"></a>Pausa ingress

- Inställningen **pausa** inaktivitet är utformad för att säkerställa att data inte rensas om storlek och antal gränser uppnås före deras kvarhållningsperiod.  
- **Pausa** ingångar ger ytterligare tid för användarna att öka kapaciteten för miljön innan data rensas på grund av överträdelse av kvarhållningsperioden.
- Det hjälper dig att skydda dig mot data förlust, men du kan skapa en möjlighet att förlora dina senaste data om ingressen pausas bortom händelse källans kvarhållningsperiod.
- När en Miljös maximala kapacitet har nåtts pausar dock miljön data ingångs tiden tills följande ytterligare åtgärder inträffar:

  - Du ökar miljöns maximala kapacitet för att lägga till fler skalnings enheter enligt beskrivningen i [så här skalar du Azure Time Series Insightss miljön](time-series-insights-how-to-scale-your-environment.md).
  - Data lagrings perioden har nåtts och data rensas, vilket gör miljön lägre än den maximala kapaciteten.

### <a name="example-three"></a>Exempel tre

Överväg en miljö med kvarhållning som kon figurer ATS för att **pausa** ingångar. I det här exemplet är **datakvarhållning-perioden** inställd på 60 dagar. **Kapaciteten** anges till tre (3) enheter av S1. Anta att den här miljön har ingress av data på 2 GB varje dag. I den här miljön pausas ingressen när den maximala kapaciteten har nåtts.

Vid detta tillfälle visar miljön samma data uppsättning tills ingressen återupptas eller tills **fortsatt** inaktivitet är aktiverat (vilket skulle rensa äldre data för att göra plats för nya data).

När ingress återupptas:

- Data flödar i den ordning som den togs emot av händelse källan
- Händelserna indexeras baserat på deras tidsstämpel, om du inte har överskridit bevarande principerna för händelse källan. För ytterligare information om konfiguration av händelse källans kvarhållning, [Event Hubs vanliga frågor och svar](../event-hubs/event-hubs-faq.md)

> [!IMPORTANT]
> Du bör ange aviseringar för att meddela att du undviker ingångs avbrott. Data förlust är möjlig eftersom standard kvarhållning är 1 dag för Azures händelse källor. När ingångarna har pausats förlorar du förmodligen de senaste data om inte ytterligare åtgärder vidtas. Du måste öka kapacitets-eller växel beteendet för att **Rensa gamla data** för att undvika risken för data förlust.

I den påverkade Event Hubs bör du överväga att justera egenskapen för **kvarhållning av meddelanden** för att minimera data förlust när paus inträffar i Azure Time Series Insights.

[![Kvarhållning av Event Hub-meddelande.](media/time-series-insights-concepts-retention/event-hub-retention.png)](media/time-series-insights-concepts-retention/event-hub-retention.png#lightbox)

Om inga egenskaper har kon figurer ATS på händelse källan ( `timeStampPropertyName` ) Azure Time Series Insights standardvärdet för tidsstämpeln för ankomst till händelsehubben som X-axeln. Om `timeStampPropertyName` har kon figurer ATS att vara något annat söker miljön efter den konfigurerade `timeStampPropertyName` i data paketet när händelserna parsas.

Läs [hur du skalar din Azure Time Series Insights-miljö](time-series-insights-how-to-scale-your-environment.md) för att skala din miljö för att hantera ytterligare kapacitet eller öka längden på kvarhållning.

## <a name="next-steps"></a>Nästa steg

- Information om hur du konfigurerar eller ändrar inställningar för datakvarhållning finns [i Konfigurera kvarhållning i Azure Time Series Insights](time-series-insights-how-to-configure-retention.md).

- Läs mer om hur du [minimerar svars tiden i Azure Time Series Insights](time-series-insights-environment-mitigate-latency.md).
