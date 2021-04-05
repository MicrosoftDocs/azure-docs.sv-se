---
title: Diagnostisera och felsöka en Gen2-miljö – Azure Time Series Insights | Microsoft Docs
description: Lär dig hur du diagnostiserar och felsöker en Azure Time Series Insights Gen2-miljö.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/01/2020
ms.custom: seodec18
ms.openlocfilehash: d9dd07e3a35d83ff6bd9c7c493768d1197667c39
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98108797"
---
# <a name="diagnose-and-troubleshoot-an-azure-time-series-insights-gen2-environment"></a>Diagnostisera och felsöka en Azure Time Series Insights Gen2-miljö

I den här artikeln sammanfattas flera vanliga problem som kan uppstå när du arbetar med din Azure Time Series Insights Gen2-miljö. Artikeln beskriver också möjliga orsaker och lösningar för varje problem.

## <a name="problem-i-cant-find-my-environment-in-the-gen2-explorer"></a>Problem: Jag kan inte hitta min miljö i Gen2 Explorer

Det här problemet kan inträffa om du inte har behörighet att komma åt Time Series Insightss miljön. Användare behöver en åtkomst roll på läsare-nivå för att visa sin Time Series Insightss miljö. Om du vill verifiera de aktuella åtkomst nivåerna och bevilja ytterligare åtkomst går du till avsnittet **data åtkomst principer** på Time Series Insights resursen i [Azure Portal](https://portal.azure.com/).

  [![Kontrol lera principer för data åtkomst.](media/preview-troubleshoot/verify-data-access-policies.png)](media/preview-troubleshoot/verify-data-access-policies.png#lightbox)

## <a name="problem-no-data-is-seen-in-the-gen2-explorer"></a>Problem: inga data visas i Gen2 Explorer

Det finns flera vanliga orsaker till varför dina data kanske inte visas i [Azure Time Series Insights Gen2 Explorer](https://insights.timeseries.azure.com/preview).

- Din händelse källa kanske inte tar emot data.

    Kontrol lera att händelse källan, som är en händelsehubben eller IoT Hub, tar emot data från taggar eller instanser. Verifiera genom att gå till översikts sidan för resursen i Azure Portal.

    [![Granska instrument panelens mått översikt.](media/preview-troubleshoot/verify-dashboard-metrics.png)](media/preview-troubleshoot/verify-dashboard-metrics.png#lightbox)

- Händelsens källdata är inte i JSON-format.

    Time Series Insights stöder endast JSON-data. För JSON-exempel kan du läsa [JSON-former som stöds](./concepts-json-flattening-escaping-rules.md).

- Din händelse käll nyckel saknar en nödvändig behörighet.

  - För en IoT-hubb måste du ange den nyckel som har behörighet för **tjänst anslutning** .

    [![Verifiera IoT Hub-behörigheter.](media/preview-troubleshoot/verify-correct-permissions.png)](media/preview-troubleshoot/verify-correct-permissions.png#lightbox)

    - Båda principerna **iothubowner** och **service** fungerar eftersom de har behörighet för **tjänst anslutning** .

  - För en Event Hub måste du ange den nyckel som har behörigheten **Lyssna** .
  
    [![Granska händelse hubbens behörigheter.](media/preview-troubleshoot/verify-eh-permissions.png)](media/preview-troubleshoot/verify-eh-permissions.png#lightbox)

    - Både **Läs** -och **hanterings** principerna fungerar eftersom de har behörigheten **Lyssna** .

- Den angivna konsument gruppen är inte exklusiv för Time Series Insights.

    Under registreringen av en IoT-hubb eller Event Hub anger du den konsument grupp som används för att läsa data. Den här konsument gruppen måste vara unik per miljö. Om konsument gruppen delas, kopplas den underliggande händelsehubben automatiskt till en av läsarna. Ange en unik konsument grupp för Time Series Insights att läsa från.

- Din Time Series ID-egenskap som angavs vid tidpunkten för etableringen är felaktig, saknas eller är null.

    Det här problemet kan inträffa om egenskapen Time Series ID är felaktigt konfigurerad vid tidpunkten för etablering av miljön. Mer information finns [i metod tips för att välja ett Time Series-ID](./how-to-select-tsid.md). För tillfället kan du inte uppdatera en befintlig Time Series Insights miljö för att använda ett annat tids serie-ID.

## <a name="problem-some-data-shows-but-some-is-missing"></a>Problem: vissa data visas, men vissa saknas

Du kan skicka data utan tids serie-ID.

- Det här problemet kan uppstå när du skickar händelser utan fältet Time Series ID i nytto lasten. Mer information finns i [JSON-former som stöds](./concepts-json-flattening-escaping-rules.md).
- Det här problemet kan bero på att din miljö är begränsad.

    > [!NOTE]
    > För tillfället har Time Series Insights stöd för en maximal inmatnings hastighet på 1 Mbit/s.

## <a name="problem-data-was-showing-but-now-ingestion-has-stopped"></a>Problem: data visades, men nu har inmatningen stoppats

- Din händelse käll nyckel kan ha återskapats och din Gen2-miljö behöver den nya händelse käll nyckeln.

Det här problemet uppstår när den nyckel som angavs när du skapade din händelse källa inte längre är giltig. Du ser telemetri i hubben men inga ingångs meddelanden tas emot i Time Series Insights. Om du är osäker på om nyckeln har återskapats kan du söka i Event Hubs "aktivitets logg för" skapa eller uppdatera namn områdes auktoriseringsregler "eller söka i" skapa eller uppdatera IotHub resurs "för IoT Hub.

Om du vill uppdatera din Time Series Insights Gen2-miljö med den nya nyckeln öppnar du Hub-resursen i Azure Portal och kopierar den nya nyckeln. Navigera till din TSD-resurs och klicka på händelse källor.

   [![Skärm bild som visar T S-resurser med meny alternativet för händelse källor som anropats.](media/preview-troubleshoot/update-hub-key-step-1.png)](media/preview-troubleshoot/update-hub-key-step-1.png#lightbox)

Välj de händelse källor där inmatningen har stoppats, klistra in den nya nyckeln och klicka på Spara.

   [![Skärm bild som visar T. ex. en resurs med T. ex. princip nyckeln för hubben.](media/preview-troubleshoot/update-hub-key-step-2.png)](media/preview-troubleshoot/update-hub-key-step-2.png#lightbox)

## <a name="problem-my-event-sources-timestamp-property-name-doesnt-work"></a>Problem: min händelse källas egenskaps namn för tidsstämpel fungerar inte

Kontrol lera att namnet och värdet följer följande regler:

- Egenskaps namnet för tidsstämpeln är Skift läges känsligt.
- Värdet för egenskapen tidsstämpel som kommer från din händelse källa som en JSON-sträng har formatet `yyyy-MM-ddTHH:mm:ss.FFFFFFFK` . Ett exempel på en sådan sträng är `"2008-04-12T12:53Z"` .

Det enklaste sättet att se till att namnet på Tidsstämpelns egenskap är infångat och fungerar korrekt är att använda Time Series Insights Gen2 Explorer. I Time Series Insights Gen2 Explorer använder du diagrammet för att välja en tids period när du har angett namnet på Tidsstämpelns egenskap. Högerklicka på markeringen och välj alternativet för att **utforska händelser** . Den första kolumn rubriken är namnet på Tidsstämpelns egenskap. Den bör ha `($ts)` intill ordet `Timestamp` , i stället för att:

- `(abc)`, som anger att Time Series Insights läser data värden som strängar.
- **Kalender** ikonen, som anger att Time Series Insights läser datavärdet som DateTime.
- `#`, som anger att Time Series Insights läser data värden som ett heltal.

Om egenskapen timestamp inte uttryckligen anges används en händelses IoT Hub-eller Event Hub-tid som standard tids stämpling.

## <a name="problem-i-cant-view-data-from-my-warm-store-in-the-explorer"></a>Problem: Jag kan inte visa data från min varma lagrings plats i Utforskaren

- Du kanske har samlat in ditt varmt Arkiv nyligen och data flödar fortfarande.
- Du kanske har tagit bort din varma lagrings plats, vilket innebär att du skulle ha förlorat data.

## <a name="problem-i-cant-view-or-edit-my-time-series-model"></a>Problem: Jag kan inte Visa eller redigera min tids serie modell

- Du kanske använder en Time Series Insights S1-eller S2-miljö.

   Tids serie modeller stöds bara i "betala per användning"-miljöer. Mer information om hur du kommer åt din S1-eller S2-miljö från Time Series Insights Gen2 Explorer finns [i visualisera data i Utforskaren](./concepts-ux-panels.md).

   [![Inga händelser i miljön.](media/preview-troubleshoot/troubleshoot-no-events.png)](media/preview-troubleshoot/troubleshoot-no-events.png#lightbox)

- Du kanske inte har behörighet att visa och redigera modellen.

   Användare behöver åtkomst på deltagar nivå för att redigera och visa sin tids serie modell. Om du vill kontrol lera de aktuella åtkomst nivåerna och bevilja ytterligare åtkomst går du till avsnittet **data åtkomst principer** i Time Series Insights resursen i Azure Portal.

## <a name="problem-all-my-instances-in-the-gen2-explorer-lack-a-parent"></a>Problem: alla mina instanser i Gen2 Explorer saknar överordnad

Det här problemet kan inträffa om din miljö inte har en definierad tids serie modell hierarki. Mer information finns i hur du [arbetar med tids serie modeller](./time-series-insights-overview.md).

  [![För inöverordnade instanser visas en varning.](media/preview-troubleshoot/unparented-instances.png)](media/preview-troubleshoot/unparented-instances.png#lightbox)

## <a name="next-steps"></a>Nästa steg

- Läs om hur du [arbetar med tids serie modeller](./time-series-insights-overview.md).

- Lär dig mer om [JSON-former som stöds](./concepts-json-flattening-escaping-rules.md).

- Granska [planering och gränser](./how-to-plan-your-environment.md) i Azure Time Series Insights Gen2.