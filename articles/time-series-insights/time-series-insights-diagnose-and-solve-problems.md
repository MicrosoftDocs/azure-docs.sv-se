---
title: Diagnostisera, Felsök och lös problem – Azure Time Series Insights
description: Den här artikeln beskriver hur du diagnostiserar, felsöker och löser vanliga problem i din Azure Time Series Insightss miljö.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.reviewer: v-mamcge
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 09/29/2020
ms.custom: seodec18
ms.openlocfilehash: 15f2ff5aaa1d731c13125d0a3ab4ac32acb9276c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "95023283"
---
# <a name="diagnose-and-solve-issues-in-your-azure-time-series-insights-gen1-environment"></a>Diagnostisera och lösa problem i din Azure Time Series Insights gen1-miljö

> [!CAUTION]
> Det här är en gen1-artikel.

Den här artikeln beskriver problem som kan uppstå i din Azure Time Series Insightss miljö. Artikeln innehåller möjliga orsaker och lösningar för lösning.

## <a name="video"></a>Video

### <a name="learn-about-common-azure-time-series-insights-challenges-and-mitigationsbr"></a>Läs om vanliga Azure Time Series Insights utmaningar och begränsningar</br>

> [!VIDEO https://www.youtube.com/embed/7U0SwxAVSKw]

## <a name="problem-no-data-is-shown"></a>Problem: inga data visas

Om inga data visas i [Azure Time Series Insights Explorer](https://insights.timeseries.azure.com)bör du tänka på dessa vanliga orsaker.

### <a name="cause-a-event-source-data-isnt-in-json-format"></a>Orsak till: händelse källans data är inte i JSON-format

Azure Time Series Insights stöder endast JSON-data. JSON-exempel finns i [JSON-former som stöds](./concepts-json-flattening-escaping-rules.md).

### <a name="cause-b-the-event-source-key-is-missing-a-required-permission"></a>Orsak B: händelse käll nyckeln saknar en nödvändig behörighet

* För en IoT-hubb i Azure IoT Hub måste du ange den nyckel som har behörighet för tjänst anslutning. Välj antingen **iothubowner** eller **tjänst** princip. Båda har behörigheter för tjänst anslutning.

   [![IoT Hub service Connect-behörigheter](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png)](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png#lightbox)

* För en Event Hub i Azure Event Hubs måste du ange den nyckel som har avlyssnings behörigheter. Principerna för att **läsa** och **Hantera** fungerar både eftersom båda har avlyssnings behörighet.

   [![Lyssnar behörigheter för Event Hub](media/diagnose-and-solve-problems/eventhub-listen-permissions.png)](media/diagnose-and-solve-problems/eventhub-listen-permissions.png#lightbox)

### <a name="cause-c-the-provided-consumer-group-isnt-exclusive-to-azure-time-series-insights"></a>Orsak C: den angivna konsument gruppen är inte exklusiv för Azure Time Series Insights

När du registrerar en IoT-hubb eller en händelsehubben är det viktigt att ange den konsument grupp som du vill använda för att läsa data. Det *går inte att dela* den här konsument gruppen. Om konsument gruppen delas, kommer den underliggande IoT-hubben eller händelsehubben att automatiskt koppla bort en av läsarna. Ange en unik konsument grupp för Azure Time Series Insights att läsa från.

### <a name="cause-d-the-environment-has-just-been-provisioned"></a>Orsak D: miljön har precis etablerats

Data visas i Azure Time Series Insights Explorer inom några minuter efter det att miljön och dess data först har skapats.

## <a name="problem-some-data-is-shown-but-data-is-missing"></a>Problem: vissa data visas, men data saknas

När data endast visas delvis och data verkar vara störningar, bör du tänka på dessa möjliga problem.

### <a name="cause-a-your-environment-is-being-throttled"></a>Orsak A: din miljö begränsas

[Begränsning](time-series-insights-environment-mitigate-latency.md) är ett vanligt problem när miljöer är etablerade när du har skapat en händelse källa som innehåller data. Azure IoT Hub-och Azure Events-hubbar lagrar data i upp till sju dagar. Azure Time Series Insights börjar alltid med den äldsta händelsen i händelse källan (först in, först ut eller *FIFO*).

Om du till exempel har 5 000 000 händelser i en händelse källa när du ansluter till en enhet Azure Time Series Insights miljö i S1, Azure Time Series Insights läser cirka 1 000 000 händelser per dag. Det kan se ut som Azure Time Series Insights har fem dagars latens. Men vad som händer är att miljön begränsas.

Om du har gamla händelser i din händelse källa kan du använda begränsning på något av följande sätt:

* Ändra händelse källans kvarhållningsintervall för att hjälpa till att ta bort gamla händelser som du inte vill ska visas i Azure Time Series Insights.
* Etablera en större miljö storlek (antal enheter) för att öka data flödet för gamla händelser. I föregående exempel, om du ökar samma S1-miljö till fem enheter under en dag, bör miljön fångas upp inom en dag. Om din stabila händelse produktion är 1 000 000 eller färre händelser per dag kan du minska händelse kapaciteten till en enhet när Azure Time Series Insights har fångats upp.

Den tvingade begränsnings gränsen baseras på miljöns SKU-typ och kapacitet. Alla händelse källor i miljön delar den här kapaciteten. Om händelse källan för din IoT-hubb eller händelsehubben skickar data bortom de tvingade gränserna får du en begränsning och en fördröjning.

Följande bild visar en Azure Time Series Insights miljö som har en SKU av S1 och en kapacitet på 3. Det kan intränga 3 000 000 händelser per dag.

[![Miljö kapacitet](media/diagnose-and-solve-problems/environment-sku-current-capacity.png)](media/diagnose-and-solve-problems/environment-sku-current-capacity.png#lightbox)

Föreställ dig en miljö som matar in meddelanden från en händelsehubben. Den har en daglig ingångs frekvens på cirka 67 000 meddelanden. Den här hastigheten översätter cirka 46 meddelanden varje minut.

* Om varje Event Hub-meddelande utplattas till ett enda Azure Time Series Insights-händelse inträffar inte begränsningen.
* Om varje Event Hub-meddelande utplattas till 100 Azure Time Series Insights händelser ska 4 600-händelser matas in varje minut.

En S1 SKU-miljö som har en kapacitet på 3 kan bara intränga 2 100 händelser varje minut (1 000 000 händelser per dag = 700 händelser per minut vid tre enheter = 2 100 händelser per minut).

Se [JSON-former som stöds](./concepts-json-flattening-escaping-rules.md)för att få en övergripande förståelse för hur förenkling av logik fungerar.

#### <a name="recommended-resolutions-for-excessive-throttling"></a>Rekommenderade lösningar för överdriven begränsning

Om du vill åtgärda fördröjningen ökar du SKU-kapaciteten för din miljö. Mer information finns [i skala din Azure Time Series Insightss miljö](time-series-insights-how-to-scale-your-environment.md).

### <a name="cause-b-initial-ingestion-of-historical-data-slows-ingress"></a>Orsak B: inledande inmatning av historiska data går långsamt

Om du ansluter en befintlig händelse källa är det troligt att din IoT-hubb eller händelsehubben redan innehåller data. Miljön börjar hämta data från början av händelse källans kvarhållning av meddelanden. Den här standard bearbetningen kan inte åsidosättas. Du kan engagera begränsning. Begränsningen kan ta en stund att fånga upp när den matar in historiska data.

#### <a name="recommended-resolutions-for-large-initial-ingestion"></a>Rekommenderade lösningar för stor inledande inmatning

Så här åtgärdar du fördröjningen:

1. Öka SKU-kapaciteten till det högsta tillåtna värdet (10, i det här fallet). När du har ökat kapaciteten börjar ingångs processen att fånga upp mycket snabbare. Du debiteras för den ökade kapaciteten. Om du vill visualisera hur snabbt du får upp kan du Visa tillgänglighets diagrammet i [Azure Time Series Insights Explorer](https://insights.timeseries.azure.com).

2. När fördröjningen har fångats upp minskar du SKU-kapaciteten till din normala ingångs taxa.

## <a name="problem-data-was-showing-previously-but-is-no-longer-showing"></a>Problem: data visades tidigare men visas inte längre

Om Azure Time Series Insights inte längre matar in data, men händelser strömmas direkt till IoT Hub eller Event Hub, bör du tänka på den här potentiella orsaken.

### <a name="cause-a-your-hub-access-key-was-regenerated-and-your-environment-needs-to-be-updated"></a>Orsak: din åtkomst nyckel för navet har återskapats och din miljö måste uppdateras

Det här problemet uppstår när den nyckel som angavs när du skapade din händelse källa inte längre är giltig. Du ser telemetri i hubben men inga ingångs meddelanden tas emot i Azure Time Series Insights. Om du inte är säker på om nyckeln har återskapats kan du söka i Event Hub-aktivitets loggen efter "skapa eller uppdatera auktoriseringsregler för namn områden". För en IoT-hubb söker du efter "skapa eller uppdatera IotHub-resurs".

Om du vill uppdatera Azure Time Series Insights miljön med den nya nyckeln öppnar du nav resursen i Azure Portal och kopierar den nya nyckeln. Gå till Azure Time Series Insights resurs och välj **händelse källor**:

   [![Välj händelse källor](media/diagnose-and-solve-problems/update-hub-key-step-1.png)](media/diagnose-and-solve-problems/update-hub-key-step-1.png#lightbox)

Välj händelse källan eller käll källorna där inmatningen har stoppats, klistra in den nya nyckeln och välj sedan **Spara**:

   [![Klistra in den nya nyckeln](media/diagnose-and-solve-problems/update-hub-key-step-2.png)](media/diagnose-and-solve-problems/update-hub-key-step-2.png#lightbox)

## <a name="problem-the-event-sources-timestamp-property-name-setting-doesnt-work"></a>Problem: händelse källans egenskaps namn för tidsstämpel fungerar inte

Se till att värdet för tidsstämpel-egenskapen som kommer från din händelse källa som en JSON-sträng har formatet _ÅÅÅÅ-MM-ddTHH: mm: SS. FFFFFFFK_. Här är ett exempel: **2008-04-12T12:53Z**.

Tänk på att egenskaps namnet för tidsstämpeln är Skift läges känsligt.

Det enklaste sättet att se till att namnet på Tidsstämpelns egenskap är infångat och fungerar korrekt är att använda Azure Time Series Insights Explorer. I Azure Time Series Insights Explorer, med hjälp av diagrammet, väljer du en tids period när du har angett namnet på Tidsstämpelns egenskap. Högerklicka på markeringen och välj sedan **utforska händelser**.

Den första kolumn rubriken ska vara namnet på din timestamp-egenskap. Vid **tidsstämpeln** för Word visas **($TS)** .

Följande värden visas inte:

* *(ABC)*: anger att Azure Time Series Insights läser data värden som strängar.
* *Kalender ikon*: anger att Azure Time Series Insights läser data värden som datetime-värden.
* *#*: Anger att Azure Time Series Insights läser data värden som heltal.

## <a name="next-steps"></a>Nästa steg

* Läs om [hur du minimerar svars tiden i Azure Time Series Insights](time-series-insights-environment-mitigate-latency.md).

* Lär dig [hur du skalar din Azure Time Series Insightss miljö](time-series-insights-how-to-scale-your-environment.md).