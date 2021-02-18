---
title: Visa Azure Event Grid mått och ange aviseringar
description: Den här artikeln beskriver hur du använder Azure Portal för att visa mått för Azure Event Grid ämnen och prenumerationer och skapa aviseringar på dem.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 8f8d7e15475ce74dc1af55dc7f6116d5d8b79cc8
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100577408"
---
# <a name="monitor-event-grid-message-delivery"></a>Övervaka Event Grid meddelande leverans 
Den här artikeln beskriver hur du använder portalen för att se mått för Event Grid ämnen och prenumerationer och hur du skapar aviseringar. 

## <a name="metrics"></a>Mått

Portalen visar mått för statusen för att leverera händelse meddelanden.

Här följer några av måtten för ämnen:

* **Publiceringen lyckades**: händelsen har skickats till ämnet och bearbetats med ett 2xx-svar.
* **Publiceringen misslyckades**: händelsen skickades till ämnet men avvisades med en felkod.
* **Omatchad**: händelsen har publicerats till ämnet, men inte matchad med en händelse prenumeration. Händelsen släpptes.

Här är några av måtten för prenumerationer:

* **Leveransen lyckades**: händelsen har levererats till prenumerationens slut punkt och ett 2xx-svar togs emot.
* **Leveransen misslyckades**: varje gång tjänsten försöker leverera och händelse hanteraren inte returnerar en lyckad 2xx-kod ökar den **misslyckade leverans** räknaren. Om vi försöker leverera samma händelse flera gånger och Miss lyckas, ökar den **misslyckade leverans** räknaren för varje fel.
* **Utgångna händelser**: händelsen levererades inte och alla nya återförsök har skickats. Händelsen släpptes.
* **Matchade händelser**: händelsen i ämnet matchades av händelse prenumerationen.

    > [!NOTE]
    > En fullständig lista över mått finns i [mått som stöds av Azure Event Grid](metrics.md).

## <a name="view-custom-topic-metrics"></a>Visa mått för anpassade ämnen

Om du har publicerat ett anpassat ämne kan du visa måtten för det. 

1. Logga in på [Azure Portal](https://portal.azure.com/).
2. I Sök fältet i avsnittet skriver du **Event Grid ämnen** och väljer **Event Grid ämnen** i list rutan. 

    :::image type="content" source="./media/custom-event-quickstart-portal/select-event-grid-topics.png" alt-text="Sök efter och välj Event Grid ämnen":::
3. Välj ditt anpassade ämne i listan med ämnen. 

    :::image type="content" source="./media/monitor-event-delivery/select-custom-topic.png" alt-text="Välj ditt anpassade ämne":::
4. Visa måtten för avsnittet anpassad händelse på sidan **Event Grid ämne** . I följande bild minimeras avsnittet **Essentials** som visar resurs gruppen, prenumerationen osv. 

    :::image type="content" source="./media/monitor-event-delivery/custom-topic-metrics.png" alt-text="Visa händelse mått":::

Du kan skapa diagram med mått som stöds med hjälp av fliken **mått** på sidan **Event Grid ämne** .

:::image type="content" source="./media/monitor-event-delivery/topics-metrics-page.png" alt-text="Ämne – mått Sidan":::

Mer information om mått finns [i mått i Azure Monitor](../azure-monitor/essentials/data-platform-metrics.md)

Se till exempel mått diagrammet för måttet **publicerade händelser** .

:::image type="content" source="./media/monitor-event-delivery/custom-topic-metrics-example.png" alt-text="Mått för publicerade händelser":::


## <a name="view-subscription-metrics"></a>Visa prenumerations mått
1. Gå till sidan **Event Grid ämne** genom att följa stegen i föregående avsnitt. 
2. Välj prenumerationen i det nedre fönstret, som du ser i följande exempel. 

    :::image type="content" source="./media/monitor-event-delivery/select-event-subscription.png" alt-text="Välj händelse prenumeration":::    

    Du kan också söka efter **Event Grid prenumerationer** i Sök fältet i Azure Portal, Välj **ämnes typ**, **prenumeration** och **plats** för att se en händelse prenumeration. 

    :::image type="content" source="./media/monitor-event-delivery/event-subscriptions-page.png" alt-text="Sidan Välj händelse prenumeration från Event Grid prenumerationer":::        

    För anpassade ämnen väljer du **Event Grid ämnen** som **typ av ämne**. I system avsnitt väljer du typ av Azure-resurs, till exempel **lagrings konton (BLOB, GPv2)**. 
3. Se prenumerations måtten på Start sidan för prenumerationen i ett diagram. Du kan se **allmänna**, **fel**, **svars tider** och värden för **obeställbara meddelanden** under de senaste 1 timmarna, 6 timmar, 12 timmar, 1 dag, 7 dagar eller 30 dagar. 

    :::image type="content" source="./media/monitor-event-delivery/subscription-home-page-metrics.png" alt-text="Mått på Start sidan för prenumerationen":::    

## <a name="view-system-topic-metrics"></a>Visa statistik över system ämnen

1. Logga in på [Azure Portal](https://portal.azure.com/).
2. I Sök fältet i avsnittet skriver du **Event Grid system ämnen** och väljer sedan **Event Grid system ämnen** i list rutan. 

    :::image type="content" source="./media/monitor-event-delivery/search-system-topics.png" alt-text="Sök efter och välj Event Grid system ämnen":::
3. Välj system ämne i listan med ämnen. 

    :::image type="content" source="./media/monitor-event-delivery/select-system-topic.png" alt-text="Välj ditt system ämne":::
4. Visa måtten för system-avsnittet på ämnes sidan för **Event Grid systemet** . I följande bild minimeras avsnittet **Essentials** som visar resurs gruppen, prenumerationen osv. 

    :::image type="content" source="./media/monitor-event-delivery/system-topic-overview-metrics.png" alt-text="Visa statistik för system ämnen på sidan Översikt":::

Du kan skapa diagram med mått som stöds med hjälp av fliken **mått** på sidan **Event Grid ämne** .

:::image type="content" source="./media/monitor-event-delivery/system-topic-metrics-page.png" alt-text="System ämne – mått Sidan":::

Mer information om mått finns [i mått i Azure Monitor](../azure-monitor/essentials/data-platform-metrics.md)


## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

- Information om hur du skapar aviseringar för mått och aktivitets logg åtgärder finns i [Ange aviseringar](set-alerts.md).
- Om du vill ha information om händelse leverans och försök kan du [Event Grid meddelande leverans och försöka igen](delivery-and-retry.md).
