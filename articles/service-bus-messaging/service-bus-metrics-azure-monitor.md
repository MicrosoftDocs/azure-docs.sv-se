---
title: Azure Service Bus mått i Azure Monitor | Microsoft Docs
description: Den här artikeln förklarar hur du använder Azure Monitor för att övervaka Service Bus entiteter (köer, ämnen och prenumerationer).
ms.topic: article
ms.date: 02/12/2021
ms.openlocfilehash: 1e57def2ee7409ae62cdbe065c36e8a53a140e18
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100570568"
---
# <a name="azure-service-bus-metrics-in-azure-monitor"></a>Azure Service Bus mått i Azure Monitor

Service Bus mått ger dig tillstånd för resurser i din Azure-prenumeration. Med en omfattande uppsättning Mät data kan du utvärdera den övergripande hälsan för dina Service Bus-resurser, inte bara på namn områdes nivå, utan även på enhets nivå. Den här statistiken kan vara viktig när de hjälper dig att övervaka status för Service Bus. Mått kan också hjälpa till att felsöka rotor Saks problem utan att behöva kontakta Azure-supporten.

Azure Monitor tillhandahåller enhetliga användar gränssnitt för övervakning i olika Azure-tjänster. Mer information finns i [övervakning i Microsoft Azure](../azure-monitor/overview.md) och [Hämta Azure Monitor mått med .net](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) -exempel på GitHub.

> [!IMPORTANT]
> När det inte har varit någon interaktion med en entitet i 2 timmar börjar måtten Visa "0" som ett värde tills entiteten inte längre är inaktiv.

## <a name="access-metrics"></a>Åtkomst mått

Azure Monitor ger till gång till mått på flera sätt. Du kan antingen komma åt mått via [Azure Portal](https://portal.azure.com)eller använda Azure Monitor-API: er (rest och .net) och analys lösningar som Azure Monitor loggar och Event Hubs. Mer information finns i [mått i Azure Monitor](../azure-monitor/essentials/data-platform-metrics.md).

Mått är aktiverade som standard och du kan komma åt de senaste 30 dagarna med data. Om du behöver lagra data under en längre tids period kan du arkivera mått data till ett Azure Storage konto. Det här värdet konfigureras i [diagnostikinställningar](../azure-monitor/essentials/diagnostic-settings.md) i Azure Monitor.

## <a name="access-metrics-in-the-portal"></a>Åtkomst mått i portalen

Du kan övervaka mått över tid i [Azure Portal](https://portal.azure.com). I följande exempel visas hur du visar lyckade förfrågningar och inkommande begär Anden på konto nivå:

![Skärm bild av sidan övervaka – mått (för hands version) i Azure Portal.][1]

Du kan också komma åt mått direkt via namn området. Det gör du genom att markera ditt namn område och sedan välja **mått**. Om du vill visa mått som filtrerats till entitetens omfång väljer du entiteten och väljer sedan **mått**.

![Skärm bild av sidan övervakaren-Metrics (för hands version) filtrerad till entitetens omfång.][2]

För mått som stöder dimensioner måste du filtrera med det önskade dimension svärdet.

## <a name="billing"></a>Fakturering

Mått och aviseringar på Azure Monitor debiteras per avisering. Dessa avgifter bör vara tillgängliga på portalen när aviseringen har kon figurer ATS och innan den sparas. 

Ytterligare lösningar som inhämtar mått data debiteras direkt av dessa lösningar. Du debiteras till exempel av Azure Storage om du arkiverar mått data till ett Azure Storage konto. du debiteras också av Log Analytics om du strömmar mått data till Log Analytics för avancerad analys.

Följande mått ger en översikt över hälso tillståndet för din tjänst. 

> [!NOTE]
> Vi har föråldrat flera mått när de flyttas under ett annat namn. Detta kan kräva att du uppdaterar dina referenser. Mått som har marker ATS med nyckelordet "inaktuell" kommer inte att stödjas framåt.

Alla mått värden skickas till Azure Monitor varje minut. Tids kornig het definierar tidsintervallet för vilka mått värden presenteras. Det tidsintervall som stöds för alla Service Bus mått är 1 minut.

## <a name="request-metrics"></a>Begär mått

Räknar antalet data-och hanterings åtgärder som begärs.

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
| Inkommande förfrågningar| Antalet begär Anden som gjorts till tjänsten Service Bus under en angiven period. <br/><br/> Enhet: antal <br/> Sammansättnings typ: totalt <br/> Dimension: entitetsnamn|
|Slutförda förfrågningar|Antalet lyckade förfrågningar som gjorts till tjänsten Service Bus under en angiven period.<br/><br/> Enhet: antal <br/> Sammansättnings typ: totalt <br/> Dimension: entitetsnamn|
|Server fel|Antalet begär Anden som inte behandlats på grund av ett fel i tjänsten Service Bus under en angiven period.<br/><br/> Enhet: antal <br/> Sammansättnings typ: totalt <br/> Dimension: entitetsnamn|
|Användar fel (se följande underavsnitt)|Antalet begär Anden som inte bearbetas på grund av användar fel under en angiven period.<br/><br/> Enhet: antal <br/> Sammansättnings typ: totalt <br/> Dimension: entitetsnamn|
|Begränsade begär Anden|Antalet begär Anden som har begränsats eftersom användningen överskreds.<br/><br/> Enhet: antal <br/> Sammansättnings typ: totalt <br/> Dimension: entitetsnamn|

### <a name="user-errors"></a>Användar fel

Följande två typer av fel klassificeras som användar fel:

1. Fel på klient sidan (i HTTP som är 400-fel).
2. Fel som inträffar under bearbetning av meddelanden, till exempel [MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception).


## <a name="message-metrics"></a>Meddelande mått

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
|Inkommande meddelanden|Antalet händelser eller meddelanden som skickats till Service Bus under en angiven period. Det här måttet inkluderar inte meddelanden som är automatiskt vidarebefordrade.<br/><br/> Enhet: antal <br/> Sammansättnings typ: totalt <br/> Dimension: entitetsnamn|
|Utgående meddelanden|Antalet händelser eller meddelanden som har tagits emot från Service Bus under en angiven period.<br/><br/> Enhet: antal <br/> Sammansättnings typ: totalt <br/> Dimension: entitetsnamn|
| Meddelanden| Antal meddelanden i en kö/ett ämne. <br/><br/> Enhet: antal <br/> Sammansättnings typ: genomsnitt <br/> Dimension: entitetsnamn |
| Aktiva meddelanden| Antal aktiva meddelanden i en kö/ett ämne. <br/><br/> Enhet: antal <br/> Sammansättnings typ: genomsnitt <br/> Dimension: entitetsnamn |
| Meddelanden med obeställbara meddelanden| Antal meddelanden om obeställbara meddelanden i en kö/ett ämne. <br/><br/> Enhet: antal <br/> Sammansättnings typ: genomsnitt <br/>Dimension: entitetsnamn |
| Schemalagda meddelanden| Antal schemalagda meddelanden i en kö/ett ämne. <br/><br/> Enhet: antal <br/> Sammansättnings typ: genomsnitt  <br/> Dimension: entitetsnamn |
| Slutförda meddelanden| Antal slutförda meddelanden i en kö/ett ämne. <br/><br/> Enhet: antal <br/> Sammansättnings typ: genomsnitt <br/> Dimension: entitetsnamn |
| Övergivna meddelanden| Antal övergivna meddelanden i en kö/ett ämne. <br/><br/> Enhet: antal <br/> Sammansättnings typ: genomsnitt <br/> Dimension: entitetsnamn |
| Storlek | Storleken på en entitet (kö eller ämne) i byte. <br/><br/>Enhet: antal <br/>Sammansättnings typ: genomsnitt <br/>Dimension: entitetsnamn | 

> [!NOTE]
> Värden för meddelanden, aktiva, obeställbara meddelanden, schemalagda, avslutade och övergivna meddelanden är tidpunkts värden. Inkommande meddelanden som förbrukas direkt efter den tidpunkten kanske inte återspeglas i dessa mått. 

## <a name="connection-metrics"></a>Anslutnings mått

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
|Aktiva anslutningar|Antalet aktiva anslutningar i ett namn område och på en entitet i namn området. Värdet för det här måttet är ett värde för tidpunkt. Anslutningar som var aktiva direkt efter den tidpunkten kanske inte återspeglas i måttet.<br/><br/> Enhet: antal <br/> Sammansättnings typ: totalt <br/> Dimension: entitetsnamn|
|Öppna anslutningar |Antalet öppna anslutningar.<br/><br/> Enhet: antal <br/> Sammansättnings typ: totalt <br/> Dimension: entitetsnamn|
|Stängda anslutningar |Antalet stängda anslutningar.<br/><br/> Enhet: antal <br/> Sammansättnings typ: totalt <br/> Dimension: entitetsnamn|

## <a name="resource-usage-metrics"></a>Användnings statistik för resursanvändningen

> [!NOTE] 
> Följande mått är bara tillgängliga med **Premium** nivån. 
> 
> De viktiga måtten för att övervaka några avbrott för ett namn område på Premium-nivån är: **processor användning per namnrymd** och **minnes storlek per namnrymd**. [Konfigurera aviseringar](../azure-monitor/alerts/alerts-metric.md) för dessa mått med hjälp av Azure Monitor.
> 
> Det andra måttet du kan övervaka är: **begränsade begär Anden**. Det får inte vara ett problem, så länge namn området ligger inom gränserna för minne, CPU och Brokered Connections. Mer information finns i [begränsning i Azure Service Bus Premium-nivån](service-bus-throttling.md#throttling-in-azure-service-bus-premium-tier)

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
|CPU-användning per namnrymd|Procent andel CPU-användning för namn området.<br/><br/> Enhet: procent <br/> Sammansättnings typ: högsta <br/> Dimension: entitetsnamn|
|Minnes storleks användning per namnrymd|Den procentuella minnes användningen för namn området.<br/><br/> Enhet: procent <br/> Sammansättnings typ: högsta <br/> Dimension: entitetsnamn|

## <a name="metrics-dimensions"></a>Mått dimensioner

Azure Service Bus stöder följande dimensioner för mått i Azure Monitor. Det är valfritt att lägga till dimensioner i måtten. Om du inte lägger till dimensioner anges måtten på namn områdes nivån. 

|Dimensions namn|Beskrivning|
| ------------------- | ----------------- |
|Entitetsnamn| Service Bus stöder meddelande enheter under namn området.|

## <a name="set-up-alerts-on-metrics"></a>Konfigurera aviseringar för mått

1. På fliken **mått** på sidan **Service Bus namn område** väljer du **Konfigurera aviseringar**. 

    ![Sidan mått – konfigurera aviseringar-menyn](./media/service-bus-metrics-azure-monitor/metrics-page-configure-alerts-menu.png)
2. Välj alternativet **Välj mål** och utför följande åtgärder på sidan **Välj en resurs** : 
    1. Välj **Service Bus namnrum** för fältet **Filtrera efter resurs typ** . 
    2. Välj din prenumeration för fältet **Filtrera efter prenumeration** .
    3. Välj **Service Bus-namnområdet** i listan. 
    4. Välj **Klar**. 
    
        ![Välj namnrymd](./media/service-bus-metrics-azure-monitor/select-namespace.png)
1. Välj **Lägg till kriterier** och utför följande åtgärder på sidan **Konfigurera signal logik** :
    1. Välj **mått** för **signal typ**. 
    2. Välj en signal. Till exempel: **tjänst fel**. 

        ![Välj server fel](./media/service-bus-metrics-azure-monitor/select-server-errors.png)
    1. Välj **större än** för **villkor**.
    2. Välj **Total** för **tids agg regering**. 
    3. Ange **5** som **tröskelvärde**. 
    4. Välj **Klar**.    

        ![Ange villkor](./media/service-bus-metrics-azure-monitor/specify-condition.png)    
1. På sidan **Skapa regel** expanderar du **definiera aviserings information** och utför följande åtgärder:
    1. Ange ett **namn** för aviseringen. 
    2. Ange en **Beskrivning** av aviseringen.
    3. Välj **allvarlighets grad** för aviseringen. 

        ![Skärm bild av sidan Skapa regel. Definiera aviserings information har expanderats och fälten för aviserings regelns namn, beskrivning och allvarlighets grad är markerade.](./media/service-bus-metrics-azure-monitor/alert-details.png)
1. På sidan **Skapa regel** expanderar du **definiera åtgärds grupp**, väljer **ny åtgärds grupp** och utför följande åtgärder på **sidan Lägg till åtgärds grupp**. 
    1. Ange ett namn på åtgärds gruppen.
    2. Ange ett kort namn för åtgärds gruppen. 
    3. Välj din prenumeration. 
    4. Välj en resursgrupp. 
    5. I den här genom gången anger du **skicka e-post** som **Åtgärds namn**.
    6. Välj **e-post/SMS/push/röst** som **Åtgärds typ**. 
    7. Välj **Redigera information**. 
    8. På sidan **e-post/SMS/push/Voice** gör du följande:
        1. Välj **E-post**. 
        2. Ange **e-postadressen**. 
        3. Välj **OK**.

            ![Skärm bild av sidan Lägg till åtgärds grupp. En åtgärd med namnet "skicka e-post" med åtgärds typ e-post/SMS/push/röst läggs till i gruppen.](./media/service-bus-metrics-azure-monitor/add-action-group.png)
        4. På sidan **Lägg till åtgärds grupp** väljer du **OK**. 
1. På sidan **Skapa regel** väljer du **skapa aviserings regel**. 

    ![Knappen Skapa aviserings regel](./media/service-bus-metrics-azure-monitor/create-alert-rule.png)

## <a name="next-steps"></a>Nästa steg

Se [Azure Monitor översikt](../azure-monitor/overview.md).

[1]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor1.png
[2]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor2.png