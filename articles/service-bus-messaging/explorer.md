---
title: Använd Azure Service Bus Explorer för att utföra data åtgärder på Service Bus (förhands granskning)
description: Den här artikeln innehåller information om hur du använder den portalbaserade Azure Service Bus Explorer för att få åtkomst till Azure Service Bus data.
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: 0b5274c492a1dfb2523c52d7aea2b7ebf8eae675
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101738964"
---
# <a name="use-service-bus-explorer-to-perform-data-operations-on-service-bus-preview"></a>Använd Service Bus Explorer för att utföra data åtgärder på Service Bus (förhands granskning)

## <a name="overview"></a>Översikt

Azure Service Bus tillåter att avsändare och mottagar klient program frikopplas affärs logiken med hjälp av välbekanta punkt-till-punkt (kö) och Publishing-prenumeration (ämnes-Subscription) semantik.

Åtgärder som utförs på ett Azure Service Bus-namnområde är av två typer 
   * Hanterings åtgärder – skapa, uppdatera, ta bort Service Bus namnrymd, köer, ämnen och prenumerationer.
   * Data åtgärder – skicka till och ta emot meddelanden från köer, ämnen och prenumerationer.

Azure Service Bus Explorer utökar Portal funktionerna utöver hanterings åtgärderna för att ge stöd för data åtgärder (skicka, ta emot, granska) i köer, ämnen och prenumerationer (och underentiteter i död brev) – direkt från själva Azure Portal.

> [!NOTE]
> I den här artikeln beskrivs funktionerna i Azure Service Bus Explorer som finns på Azure Portal.
>
> Verktyget Azure Service Bus Explorer är ***inte*** community-verktyget som ägs av oss [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer).
>

## <a name="prerequisites"></a>Förutsättningar

Om du vill använda verktyget Service Bus Explorer måste du utföra följande uppgifter: 

- Etablera ett Azure Service Bus-namnområde.
- Skapa en kö för att skicka och ta emot meddelanden från eller ett ämne med en prenumeration för att testa funktionen. Information om hur du skapar köer, ämnen och prenumerationer finns i följande artiklar: 
    - [Snabb start – skapa köer](service-bus-quickstart-portal.md)
    - [Snabb start – skapa ämnen](service-bus-quickstart-topics-subscriptions-portal.md)
- Se till att du är medlem i någon av följande roller i namn området: 
    - [Service Bus data ägare](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner) 
    - [Deltagare](../role-based-access-control/built-in-roles.md#contributor) 
    - [Ägare](../role-based-access-control/built-in-roles.md#owner)


## <a name="using-the-service-bus-explorer"></a>Använda Service Bus Explorer

Om du vill använda Azure Service Bus Explorer måste du gå till det Service Bus namn område där du vill utföra sändnings-, gransknings-och mottagnings åtgärder.

Om du vill utföra åtgärder mot en kö väljer du **köer** från navigerings menyn. Om du vill utföra åtgärder mot ett ämne (och dess relaterade prenumerationer), väljer du **ämnen**. 

:::image type="content" source="./media/service-bus-explorer/queue-topics-left-navigation.png" alt-text="Välj entitet":::

När du har valt **"köer"** eller **"ämnen"** väljer du den specifika kön eller ämnet.

Välj **Service Bus Explorer (för hands version)** på den vänstra navigerings menyn

:::image type="content" source="./media/service-bus-explorer/left-navigation-menu-selected.png" alt-text="Den vänstra navigerings menyn i SB Explorer":::

### <a name="sending-a-message-to-a-queue-or-topic"></a>Skicka ett meddelande till en kö eller ett ämne

Om du vill skicka ett meddelande till en **kö** eller ett **ämne** klickar du på fliken **_Skicka_** i Service Bus Explorer.

Så här skapar du ett meddelande 

1. Välj **innehålls typen** som antingen text/plain, Application/XML eller Application/JSON.
2. Lägg till meddelandets **innehåll**. Se till att den matchar den angivna **innehålls typen** tidigare.
3. Ange **avancerade egenskaper** (valfritt) – dessa inkluderar KORRELATIONS-ID, meddelande-ID, etikett, ReplyTo, Time to Live (TTL) och tids period (för schemalagda meddelanden).
4. Ange **anpassade egenskaper** – kan vara alla användar egenskaper som anges mot en ord lista.

När meddelandet har sammanställts trycker du på Skicka.

:::image type="content" source="./media/service-bus-explorer/send-experience.png" alt-text="Skriv meddelande":::

När sändnings åtgärden har slutförts korrekt 

* Om du skickar till kön ökar måtten för **aktiva meddelanden** mått.

    :::image type="content" source="./media/service-bus-explorer/queue-after-send-metrics.png" alt-text="QueueAfterSendMetrics":::

* Om du skickar till ämnet, kommer räknaren för **aktiva meddelanden** att öka i den prenumeration där meddelandet skickades.

    :::image type="content" source="./media/service-bus-explorer/topic-after-send-metrics.png" alt-text="TopicAfterSendMetrics":::

### <a name="receiving-a-message-from-a-queue"></a>Ta emot ett meddelande från en kö

Funktionen Receive i Service Bus Explorer tillåter att ett enskilt meddelande tas emot i taget. Mottagnings åtgärden utförs med **ReceiveAndDelete** -läget.

> [!IMPORTANT]
> Observera att mottagnings åtgärden som utförs av Service Bus Explorer är ett ***destruktivt mottagnings*** meddelande, d.v.s. att meddelandet tas bort från kön när det visas i Service Bus Explorer-verktyget.
>
> Om du vill bläddra i meddelanden utan att ta bort dem från kön kan du överväga att använda ***gransknings*** funktionen.
>

Ta emot ett meddelande från en kö (eller dess obeställbara meddelanden kön-underkö) 

1. Klicka på fliken ***ta emot*** i Service Bus Explorer.
2. Kontrol lera måtten för att se om det finns **aktiva meddelanden** eller **meddelanden om obeställbara** meddelanden som ska tas emot.

    :::image type="content" source="./media/service-bus-explorer/queue-after-send-metrics.png" alt-text="QueueAfterSendMetrics":::

3. Välj mellan underkön ***Queue** _ eller _ *_obeställbara meddelanden kön_**.

    :::image type="content" source="./media/service-bus-explorer/queue-or-deadletter.png" alt-text="QueueOrDeadletter":::

4. Klicka på knappen ***ta emot** _ följt av _ *_Ja_** för att bekräfta åtgärden ta emot och ta bort.


När mottagnings åtgärden lyckas visas meddelande informationen i rutnätet enligt nedan. Du kan välja meddelandet från rutnätet för att visa dess information.

:::image type="content" source="./media/service-bus-explorer/receive-message-from-queue-2.png" alt-text="Skärm bild av fönstret köer i Azure Service Bus Explorer med meddelande information som visas för ett aktivt meddelande som väljs i kön.":::


### <a name="peeking-a-message-from-a-queue"></a>Granska ett meddelande från en kö

Med Peek-funktionen kan du använda Service Bus Explorer för att visa de översta 32 meddelandena i en kö eller i obeställbara meddelanden kön-kön.

1. Granska meddelandet i en kö genom att klicka på fliken ***Granska*** i Service Bus Explorer.

    :::image type="content" source="./media/service-bus-explorer/peek-tab-selected.png" alt-text="PeekTab":::

2. Kontrol lera måtten för att se om det finns **aktiva meddelanden** eller **meddelanden med obeställbara** meddelanden som ska granskas.

    :::image type="content" source="./media/service-bus-explorer/queue-after-send-metrics.png" alt-text="QueueAfterSendMetrics":::

3. Välj sedan mellan underkön ***kö** _ eller _ *_obeställbara meddelanden kön_**.

    :::image type="content" source="./media/service-bus-explorer/queue-or-deadletter.png" alt-text="QueueOrDeadletter":::

4. Klicka på knappen ***Granska*** . 

När gransknings åtgärden har slutförts visas upp till 32 meddelanden i rutnätet enligt nedan. Om du vill visa information om ett visst meddelande väljer du det från rutnätet. 

:::image type="content" source="./media/service-bus-explorer/peek-message-from-queue-2.png" alt-text="PeekMessageFromQueue":::

> [!NOTE]
>
> Eftersom Peek inte är en destruktiv åtgärd tas **inte** meddelandet bort från kön.
>

### <a name="receiving-a-message-from-a-subscription"></a>Ta emot ett meddelande från en prenumeration

Precis som med en kö kan ***Receive*** -åtgärden utföras mot en prenumeration (eller dess obeställbara meddelanden kön entitet). Men eftersom en prenumeration är i sammanhanget för ämnet utförs mottagnings åtgärden genom att gå till Service Bus Explorer för ett specifikt avsnitt.

> [!IMPORTANT]
> Observera att mottagnings åtgärden som utförs av Service Bus Explorer är ett ***destruktivt mottagnings*** meddelande, d.v.s. att meddelandet tas bort från kön när det visas i Service Bus Explorer-verktyget.
>
> Om du vill bläddra i meddelanden utan att ta bort dem från kön kan du överväga att använda ***gransknings*** funktionen.
>

1. Klicka på fliken ***ta emot** _ och välj den aktuella _ *_prenumerationen_** i list rutan.

    :::image type="content" source="./media/service-bus-explorer/receive-subscription-tab-selected.png" alt-text="ReceiveTabSelected":::

2. Välj mellan ***prenumerationen** _ eller _ *_obeställbara meddelanden kön_** underentiteten.

    :::image type="content" source="./media/service-bus-explorer/subscription-or-deadletter.png" alt-text="SubscriptionOrDeadletter":::

3. Klicka på knappen ***ta emot** _ följt av _ *_Ja_** för att bekräfta åtgärden ta emot och ta bort.

När Receive-åtgärden lyckas visas det mottagna meddelandet i rutnätet enligt nedan. Klicka på meddelandet för att visa information om meddelandet.

:::image type="content" source="./media/service-bus-explorer/receive-message-from-subscription.png" alt-text="Skärm bild av fliken ta emot i Azure Service Bus Explorer med meddelande information som visas för ett aktivt meddelande som har tagits emot.":::

### <a name="peeking-a-message-from-a-subscription"></a>Granska ett meddelande från en prenumeration

Om du bara vill söka efter meddelanden på en prenumeration eller dess obeställbara meddelanden kön-underentitet, kan du även använda ***Peek*** -funktionerna i prenumerationen.

1. Klicka på fliken ***granskning** _ och välj den aktuella _ *_prenumerationen_** i list rutan.

    :::image type="content" source="./media/service-bus-explorer/peek-subscription-tab-selected.png" alt-text="PeekTabSelected":::

2. Välj mellan underentiteten ***Subscription** _ eller _ *_obeställbara meddelanden kön_**.

    :::image type="content" source="./media/service-bus-explorer/subscription-or-deadletter.png" alt-text="SubscriptionOrDeadletter":::

3. Klicka på knappen ***Granska*** .

När gransknings åtgärden har slutförts visas upp till 32 meddelanden i rutnätet enligt nedan. Om du vill visa information om ett visst meddelande väljer du det från rutnätet. 

:::image type="content" source="./media/service-bus-explorer/peek-message-from-subscription.png" alt-text="PeekMessageFromSubscription":::

> [!NOTE]
>
> Eftersom Peek inte är en destruktiv åtgärd tas **inte** meddelandet bort från kön.
>

## <a name="next-steps"></a>Nästa steg

   * Läs mer om Service Bus [köer](service-bus-queues-topics-subscriptions.md#queues) och [ämnen](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions)
   * Läs mer om hur [du skapar Service Bus köer via Azure Portal](service-bus-quickstart-portal.md)
   * Lär dig mer om [att skapa Service Bus ämnen och prenumerationer via Azure Portal](service-bus-quickstart-topics-subscriptions-portal.md)