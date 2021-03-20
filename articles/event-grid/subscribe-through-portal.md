---
title: Azure Event Grid prenumerationer via portalen
description: I den här artikeln beskrivs hur du skapar Event Grid prenumerationer för källor som stöds, till exempel Azure Blob Storage, genom att använda Azure Portal.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: e80e2243c93ab38187646256f567d6ab73c40100
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95995071"
---
# <a name="subscribe-to-events-through-portal"></a>Prenumerera på händelser via portalen

Den här artikeln beskriver hur du skapar Event Grid prenumerationer via portalen.

## <a name="create-event-subscriptions"></a>Skapa händelse prenumerationer

Följ stegen nedan om du vill skapa en Event Grid-prenumeration för någon av de [händelse källor](overview.md#event-sources)som stöds. Den här artikeln visar hur du skapar en Event Grid-prenumeration för en Azure-prenumeration.

1. Välj **Alla tjänster**.

   ![Välj alla tjänster](./media/subscribe-through-portal/select-all-services.png)

1. Sök efter **Event Grid prenumerationer** och välj den från de tillgängliga alternativen.

   ![Skärm dum par visar sökningen i Azure Portal med Event Grid prenumerationer valda.](./media/subscribe-through-portal/search.png)

1. Välj **+ Händelseprenumeration**.

   ![Lägg till en prenumeration](./media/subscribe-through-portal/add-subscription.png)

1. Välj den typ av prenumeration som du vill skapa. Om du till exempel vill prenumerera på händelser för din Azure-prenumeration väljer du **Azure-prenumerationer** och mål prenumerationen.

   ![Välj Azure-prenumeration](./media/subscribe-through-portal/azure-subscription.png)

1. Om du vill prenumerera på alla händelse typer för den här händelse källan ska du se till att alternativet **Prenumerera på alla händelse typer** är markerat. Annars väljer du händelse typer för den här prenumerationen.

   ![Välj händelse typer](./media/subscribe-through-portal/select-event-types.png)

1. Ange ytterligare information om händelse prenumerationen, till exempel slut punkten för hantering av händelser och ett prenumerations namn.

   ![Skärm bild som visar avsnitten "information om slut punkts information" och "information om händelse prenumeration" med ett prenumerations namn värde angivet.](./media/subscribe-through-portal/provide-subscription-details.png)

1. Om du vill aktivera inaktive brevering och anpassa principer för återförsök väljer du **ytterligare funktioner**.

   ![Välj ytterligare funktioner](./media/subscribe-through-portal/select-additional-features.png)

1. Välj en behållare som ska användas för att lagra händelser som inte levereras och ange hur återförsök ska skickas.

   ![Aktivera obeställbara meddelanden och försök igen](./media/subscribe-through-portal/set-deadletter-retry.png)

1. Välj **Skapa** när du är klar.

## <a name="create-subscription-on-resource"></a>Skapa prenumeration på resurs

Vissa händelse källor har stöd för att skapa en händelse prenumeration via Portal gränssnittet för den resursen. Välj händelse källa och Sök efter **händelser** i det vänstra fönstret.

![Ange prenumerations information](./media/subscribe-through-portal/resource-events.png)

Portalen innehåller alternativ för att skapa en händelse prenumeration som är relevant för den källan.

## <a name="next-steps"></a>Nästa steg

* Om du vill ha information om händelse leverans och försök kan du [Event Grid meddelande leverans och försöka igen](delivery-and-retry.md).
* En introduktion till Event Grid finns i [Om Event Grid](overview.md).
* Information om hur du snabbt kommer igång med Event Grid finns i [skapa och dirigera anpassade händelser med Azure Event Grid](custom-event-quickstart.md).
