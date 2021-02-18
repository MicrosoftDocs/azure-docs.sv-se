---
title: Konfigurera aviseringar
titleSuffix: Azure Digital Twins
description: Se hur du aktiverar aviseringar på digitala Azure-mått.
author: baanders
ms.author: baanders
ms.date: 7/28/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 461d7a82854ce62ee99eef1227c13c7a8f0371e2
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100594876"
---
# <a name="troubleshooting-azure-digital-twins-alerts"></a>Felsöka Azure Digitals dubbla: aviseringar

Azure Digitals flätar samlar in [Mät värden](troubleshoot-metrics.md) för din tjänst instans som ger information om statusen för dina resurser. Du kan använda dessa mått för att utvärdera den övergripande hälso statusen för Azure Digital-tjänster och de resurser som är anslutna till den.

**Aviseringar** proaktivt meddela dig när viktiga villkor finns i dina Mät data. De gör att du kan identifiera och åtgärda problem innan användarna av systemet ser dem. Du kan läsa mer om aviseringar i [*Översikt över aviseringar i Microsoft Azure*](../azure-monitor/alerts/alerts-overview.md).

## <a name="turn-on-alerts"></a>Aktivera aviseringar

Så här aktiverar du aviseringar för din Azure Digital-instansen:

1. Logga in på [Azure Portal](https://portal.azure.com) och gå till din Azure Digital-instansen. Du kan hitta det genom att skriva dess namn i portalens sökfält. 

2. Välj **aviseringar** på menyn och sedan **+ ny varnings regel**.

3. På sidan *skapa aviserings regel* som följer kan du följa anvisningarna för att definiera villkor, åtgärder som ska utlösas och aviserings information.     
    * **Omfattnings** information ska automatiskt fylla i med information om din instans
    * Du definierar **villkor** och **Åtgärds grupps** information för att anpassa aviserings utlösare och svar
    * I avsnittet **aviserings regel information** anger du ett namn och en valfri beskrivning av regeln. Du kan markera kryss rutan _Aktivera aviserings regel vid skapande_ om du vill att aviseringen ska bli aktiv så fort den skapas.
        - Det är också där du väljer en _resurs grupp_ och en _allvarlighets_ grad.

4. Klicka på knappen _skapa aviserings regel_ för att skapa din aviserings regel.

:::image type="content" source="media/troubleshoot-alerts/create-alert-rule.png" alt-text="Skärm bild som visar sidan Skapa aviserings regel med avsnitt för omfattning, villkor, åtgärds grupp och aviserings regel information" lightbox="media/troubleshoot-alerts/create-alert-rule.png":::

En guidad genom gång av hur du fyller i de här fälten finns i [*Översikt över aviseringar i Microsoft Azure*](../azure-monitor/alerts/alerts-overview.md). Nedan visas några exempel på hur stegen kommer att se ut för Azure Digital-dubbla.

### <a name="select-conditions"></a>Välj villkor

Här är ett utdrag från *Select condition* -processen som illustrerar vilka typer av aviserings signaler som är tillgängliga för Azure Digital-meddelanden. På den här sidan kan du filtrera typ av signal och välja den signal som du vill använda i en lista.

:::image type="content" source="media/troubleshoot-alerts/configure-signal-logic.png" alt-text="Skärm bild som visar den första sidan för att konfigurera signal logik. Det finns en markering i rutan signal typ för att välja mått eller aktivitets loggar, samt en lista över mått under som kan väljas":::

När du har valt en signal uppmanas du att konfigurera logiken för aviseringen. Du kan filtrera på en dimension, ange ett tröskelvärde för aviseringen och ange frekvensen för kontrollerna för villkoret. Här är ett exempel på hur du ställer in en avisering för när måttet för den genomsnittliga routing-felfrekvensen hamnar över 5%.

:::image type="content" source="media/troubleshoot-alerts/configure-signal-logic-2.png" alt-text="Skärm bild som visar den andra sidan för att konfigurera signal logik. Sidan visar mått historiken, har ett område för att filtrera på en dimension som Event Grid åtgärder och ett avsnitt för att definiera varnings logik som till exempel genomsnittet är större än 5":::

### <a name="verify-success"></a>Verifieringen lyckades

När du har ställt in aviseringar visas de upp på *aviserings* sidan för din instans.
 
:::image type="content" source="media/troubleshoot-alerts/alerts-post.png" alt-text="Skärm bild som visar sidan aviseringar och knapp att lägga till. En varning har kon figurer ATS" lightbox="media/troubleshoot-alerts/alerts-post.png":::

## <a name="next-steps"></a>Nästa steg

* Mer information om aviseringar med Azure Monitor finns i [*Översikt över aviseringar i Microsoft Azure*](../azure-monitor/alerts/alerts-overview.md).
* Information om Azure Digitals dubbla mått finns i [*fel sökning: Visa mått med Azure Monitor*](troubleshoot-metrics.md).
* Information om hur du aktiverar diagnostikloggning för dina mått finns i [*fel sökning: Konfigurera diagnostik*](troubleshoot-diagnostics.md).
