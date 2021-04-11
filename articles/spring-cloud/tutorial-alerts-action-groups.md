---
title: 'Självstudie: övervaka Azure våren Cloud-resurser med hjälp av aviseringar och åtgärds grupper | Microsoft Docs'
description: Lär dig hur du använder aviseringar för våren Cloud.
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 12/29/2019
ms.custom: devx-track-java
ms.openlocfilehash: d12a48729616a5181f019f84f19779390e736cb4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104879201"
---
# <a name="tutorial-monitor-spring-cloud-resources-using-alerts-and-action-groups"></a>Självstudie: övervaka våren moln resurser med hjälp av aviseringar och åtgärds grupper

**Den här artikeln gäller för:** ✔️ Java ✔️ C #

Azure våren Cloud-aviseringar stöder övervaknings resurser baserat på villkor som tillgängligt lagrings utrymme, antal begär Anden eller data användning. En avisering skickar ett meddelande när priser eller villkor uppfyller de definierade specifikationerna.

Det finns två steg för att konfigurera en aviserings pipeline: 
1. Konfigurera en åtgärds grupp med åtgärder som ska vidtas när en avisering utlöses, till exempel e-post, SMS, Runbook eller webhook. Åtgärds grupper kan återanvändas mellan olika aviseringar.
2. Ställ in aviserings regler. Reglerna binder mått mönster med åtgärds grupper baserat på mål resurs, mått, villkor, tids agg regering osv.

## <a name="prerequisites"></a>Förutsättningar

Förutom Azures våren-krav fungerar procedurerna i den här självstudien med en distribuerad Azure våren Cloud-instans.  Kom igång genom att följa en [snabb start](spring-cloud-quickstart.md) .

Följande procedurer initierar både **Åtgärds gruppen** och **aviseringen** från alternativet **aviseringar** i det vänstra navigerings fönstret i en våren-moln instans. (Proceduren kan också starta från sidan **övervaka översikt** i Azure Portal.) 

Navigera från en resurs grupp till vår moln instans. Välj **aviseringar** i det vänstra fönstret och välj sedan **Hantera åtgärder**:

![Sida med resurs grupp för skärm bilds portalen](media/alerts-action-groups/action-1-a.png)

## <a name="set-up-action-group"></a>Ställ in åtgärds grupp

Starta proceduren för att initiera en ny **Åtgärds grupp** genom att välja **+ Lägg till åtgärds grupp**.

![Skärm bilds portalen Lägg till åtgärds grupp](media/alerts-action-groups/action-1.png)

På sidan **Lägg till åtgärds grupp** :

 1. Ange ett **namn på en åtgärds grupp** och ett **kort namn**.

 1. Ange **prenumeration** och **resurs grupp**.

 1. Ange **Åtgärds namn**.

 1. Välj **Åtgärds typ**.  Då öppnas ett annat fönster till höger för att definiera den åtgärd som ska vidtas vid aktivering.

 1. Definiera åtgärden med hjälp av alternativen i den högra rutan.  Det här fallet använder e-postavisering.

 1. Klicka på **OK** i åtgärds fönstret till höger.

 1. Klicka på **OK** i dialog rutan **Lägg till åtgärds grupp** . 

  ![Skärm bilds portalen definiera åtgärd](media/alerts-action-groups/action-2.png)

## <a name="set-up-alert"></a>Konfigurera avisering 

Föregående steg skapade en **Åtgärds grupp** som använder e-post. Du kan också använda telefon meddelande, Webhooks, Azure Functions och så vidare. Följande steg konfigurerar en **avisering**.

1. gå tillbaka till sidan **aviseringar** och klicka på **Hantera aviserings regler**.

   ![Skärm bilds portalen definiera avisering](media/alerts-action-groups/alerts-2.png)

1. Välj **resursen** för aviseringen.

1. Klicka på **+ ny varnings regel**.

   ![Skärm bilds portalen ny varnings regel](media/alerts-action-groups/alerts-3.png)

1. På sidan **Skapa regel** anger du **resursen**.

1. **Villkors** inställningen innehåller många alternativ för att övervaka dina **våren-moln** resurser.  Klicka på **Lägg till** för att öppna fönstret **Konfigurera signal logik** .

1. Välj ett villkor. I det här exemplet används **system processor användning i procent**.

   ![Skärm bilds portalen ny varnings regel 2](media/alerts-action-groups/alerts-3-1.png)

1. Rulla nedåt i fönstret **Konfigurera signal logik** för att ange det **tröskelvärde** som ska övervakas.

   ![Skärm bilds portalen ny varnings regel 3](media/alerts-action-groups/alerts-3-2.png)

1. Klicka på **Klar**.

   Mer information om de villkor som är tillgängliga för övervakning finns i [alternativ för användar Portal mått](spring-cloud-concept-metrics.md#user-metrics-options).

1. Under **åtgärder** klickar du på **Välj åtgärds grupp**. I fönstret **åtgärder** väljer du den tidigare definierade **Åtgärds gruppen**.

   ![Skärm bilds portalen ny varnings regel 4](media/alerts-action-groups/alerts-3-3.png) 

1. Rulla nedåt och ge aviserings regeln under **aviserings information**.

1. Ange **allvarlighets grad**.

1. Klicka på **Skapa aviseringsregel**.

   ![Skärm bilds portalen ny varnings regel 5](media/alerts-action-groups/alerts-3-4.png)

1. Kontrol lera att den nya varnings regeln har Aktiver ATS.

   ![Skärm bilds portalen ny aviserings regel 6](media/alerts-action-groups/alerts-4.png)

Du kan också skapa en regel med hjälp av sidan **mått** :

![Skärm bilds portalen ny varnings regel 7](media/alerts-action-groups/alerts-5.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du konfigurerar aviseringar och åtgärds grupper för ett Azure våren Cloud-program. Mer information om åtgärds grupper finns i:

> [!div class="nextstepaction"]
> [Skapa och hantera åtgärdsgrupper i Azure-portalen](../azure-monitor/alerts/action-groups.md)

> [!div class="nextstepaction"]
> [Aviserings beteende i SMS i åtgärds grupper](../azure-monitor/alerts/alerts-sms-behavior.md)