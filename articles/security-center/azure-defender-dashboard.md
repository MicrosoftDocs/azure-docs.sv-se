---
title: Azure Defender-instrumentpanelen och dess funktioner
description: Lär dig mer om funktionerna i Azure Defender-instrumentpanelen.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 16379380fc35bb2355c496dc857e9de3b41347f9
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102096912"
---
# <a name="the-azure-defender-dashboard"></a>Azure Defender-instrumentpanelen

Azure Defender-instrumentpanelen innehåller:

- Insyn i Azure Defender-täckningen för dina olika resurs typer
- Länkar till att konfigurera avancerade hot skydds funktioner
- Onboarding-tillstånd och agent installation
- Aviseringar om hot identifiering i Azure Defender 

Om du vill komma åt Azure Defender-instrumentpanelen väljer du **Azure Defender** i avsnittet Cloud Security i Security Center menyn.

## <a name="whats-shown-on-the-dashboard"></a>Vad visas på instrument panelen?

:::image type="content" source="./media/azure-defender-dashboard/sample-defender-dashboard-numbered.png" alt-text="Ett exempel på Azure Defender-instrumentpanelen" lightbox="./media/azure-defender-dashboard/sample-defender-dashboard-numbered.png":::

Instrument panelen innehåller följande avsnitt:

1. **Azure Defender-täckning** – här kan du se de resurs typer som finns i din prenumeration och som är berättigade till skydd av Azure Defender. I tillämpliga fall har du möjlighet att uppgradera. Om du vill uppgradera alla tillgängliga resurser väljer du **Uppgradera alla**.

2. **Säkerhets aviseringar** – när Azure Defender identifierar ett hot i alla områden i miljön genererar den en avisering. De här aviseringarna beskriver information om de resurser som påverkas, föreslagna åtgärds steg och i vissa fall ett alternativ för att utlösa en logisk app som svar. Om du väljer var som helst i det här diagrammet öppnas **sidan säkerhets aviseringar**.

3. **Avancerat skydd** – Azure Defender innehåller många avancerade hot skydds funktioner för virtuella datorer, SQL-databaser, behållare, webb program, nätverk och mycket annat. I det här avsnittet om Avancerat skydd kan du se statusen för resurserna i de valda prenumerationerna för var och en av dessa skydd. Välj någon av dem för att gå direkt till konfigurations ytan för den skydds typen.

4. **Insikter** – det här åtgärds fönstret med nyheter, förslag på läsningar och aviseringar med hög prioritet ger Security Center insikter om att trycka på säkerhets frågor som är relevanta för dig och din prenumeration. Oavsett om det är en lista med hög allvarlighets grad CVEs som identifierats på dina virtuella datorer av ett sårbarhets analys verktyg, eller ett nytt blogg inlägg från en medlem i Security Center-teamet, hittar du det här i fönstret insikter på **Azure Defender-instrumentpanelen**.




## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om Azure Defender-instrumentpanelen. 

Mer information om Azure Defender finns i [Introduktion till Azure Defender](azure-defender.md)

> [!div class="nextstepaction"]
> [Aktivera Azure Defender](enable-azure-defender.md)