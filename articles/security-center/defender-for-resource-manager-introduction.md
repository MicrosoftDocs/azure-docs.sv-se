---
title: Azure Defender för Resource Manager – fördelar och funktioner
description: Lär dig mer om fördelarna och funktionerna i Azure Defender för Resource Manager
author: memildin
ms.author: memildin
ms.date: 12/07/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 9591dae058a19cea73d88513b7c4ff4ab8f88045
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2021
ms.locfileid: "98797756"
---
# <a name="introduction-to-azure-defender-for-resource-manager"></a>Introduktion till Azure Defender för Resource Manager

[Azure Resource Manager](../azure-resource-manager/management/overview.md) distributions-och hanterings tjänsten för Azure. Den ger dig ett hanteringslager där du kan skapa, uppdatera och ta bort resurser i ditt Azure-konto. Du kan använda hanteringsfunktioner som åtkomstkontroll, lås och taggar till att skydda och organisera dina resurser efter distributionen.

Moln hanterings lagret är en viktig tjänst som är ansluten till alla moln resurser. Därför är det också ett potentiellt mål för angripare. Därför rekommenderar vi säkerhets åtgärds teamen att övervaka resurs hanterings nivån nära. 

Azure Defender för Resource Manager övervakar automatiskt resurs hanterings åtgärderna i din organisation, oavsett om de utförs via Azure Portal, Azure REST-API: er, Azure CLI eller andra Azure-programmatiska klienter. Azure Defender kör avancerade säkerhets analyser för att identifiera hot och varna dig om misstänkt aktivitet.

## <a name="availability"></a>Tillgänglighet

|Aspekt|Information|
|----|:----|
|Versions tillstånd:|Förhandsgranskning<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)] |
|Priset|**Azure Defender för Resource Manager** faktureras enligt [pris sidan](security-center-pricing.md)|
|Moln|![Ja](./media/icons/yes-icon.png) Kommersiella moln<br>![Nej](./media/icons/no-icon.png) National/suverän (US Gov, Kina gov, andra gov)|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-resource-manager"></a>Vilka är fördelarna med Azure Defender för Resource Manager?

Azure Defender för Resource Manager skyddar mot problem, inklusive:

- **Misstänkta resurs hanterings åtgärder**, till exempel åtgärder från misstänkta IP-adresser, inaktiverar program mot skadlig kod och misstänkta skript som körs i VM-tillägg
- **Användning av** användnings verktyg som mikroburst eller PowerZure
- **Lateral förflyttning** från Azures hanterings lager till data planet för Azure-resurser

:::image type="content" source="media/defender-for-resource-manager-introduction/consistent-management-layer-with-defender.png" alt-text="Azure Resource Manager översikts diagram":::

En fullständig lista över de aviseringar som tillhandahålls av Azure Defender för Resource Manager finns på [referens sidan aviseringar](alerts-reference.md#alerts-resourcemanager).


 ## <a name="how-to-investigate-alerts-from-azure-defender-for-resource-manager"></a>Så här undersöker du aviseringar från Azure Defender för Resource Manager

Säkerhets aviseringar från Azure Defender för Resource Manager baseras på hot som upptäckts av övervaknings Azure Resource Manager åtgärder. Azure Defender använder interna logg källor för Azure Resource Manager och Azure aktivitets logg, en plattforms logg i Azure som ger inblick i händelser på prenumerations nivå.

Läs mer om [Azure aktivitets logg](../azure-monitor/platform/activity-log.md).

Så här undersöker du säkerhets aviseringar från Azure Defender för Resource Manager:

1. Öppna Azure aktivitets logg.

    :::image type="content" source="media/defender-for-resource-manager-introduction/opening-azure-activity-log.png" alt-text="Så här öppnar du Azure aktivitets logg":::

1. Filtrera händelserna till:
    - Prenumerationen som nämns i aviseringen
    - Den identifierade aktivitetens tidsram
    - Det relaterade användar kontot (om det är relevant)

1. Sök efter misstänkta aktiviteter.

> [!TIP]
> För en bättre och bättre gransknings upplevelse strömmar du dina Azure-aktivitets loggar till Azure Sentinel enligt beskrivningen i [Anslut data från Azure aktivitets logg](../sentinel/connect-azure-activity.md).



## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om Azure Defender för Resource Manager. Information om relaterade material finns i följande artikel: 

- Säkerhets aviseringar kan genereras av Security Center eller tas emot av Security Center från olika säkerhets produkter. Om du vill exportera alla dessa aviseringar till Azure Sentinel, SIEM eller andra externa verktyg, följer du anvisningarna i [Exportera aviseringar till en Siem](continuous-export.md).

- > [!div class="nextstepaction"]
    > [Aktivera Azure Defender](security-center-pricing.md#enable-azure-defender)