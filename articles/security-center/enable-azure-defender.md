---
title: Aktivera Azure Security Center s integrerade arbets belastnings skydd
description: Lär dig hur du aktiverar Azure Defender för att utöka skyddet av Azure Security Center till dina hybrid resurser och resurser i molnet
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: quickstart
ms.date: 02/24/2021
ms.openlocfilehash: 6496b18c8c22cbbd4fd3344736a4b38b7a998890
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102108921"
---
# <a name="quickstart-enable-azure-defender"></a>Snabb start: Aktivera Azure Defender

Läs mer om fördelarna med Azure Defender i [Security Center kostnads fri vs Azure Defender aktiverat](security-center-pricing.md).

## <a name="prerequisites"></a>Förutsättningar

I syfte att Security Center snabb starter och självstudier måste du aktivera Azure Defender. 

Du kan skydda en hel Azure-prenumeration med Azure Defender och skyddet kommer att ärvas av alla resurser i prenumerationen.

En kostnads fri 30-dagars utvärderings version är tillgänglig. Information om priser i din valuta och enligt din region finns [Security Center priser](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="enable-azure-defender-from-the-azure-portal"></a>Aktivera Azure Defender från Azure Portal

Om du vill aktivera alla Security Center funktioner, inklusive hot skydds funktioner, måste du aktivera Azure Defender på prenumerationen som innehåller de aktuella arbets belastningarna. Att aktivera den på arbets ytans nivå ger inte just-in-Time-åtkomst till virtuella datorer, anpassningsbara program kontroller och nätverks identifieringar för Azure-resurser. Dessutom är de enda Azure Defender-planer som är tillgängliga på arbets ytans nivå Azure Defender för servrar och Azure Defender för SQL-servrar på datorer.

- Du kan aktivera **Azure Defender för lagrings konton** på antingen prenumerations nivå eller resurs nivå
- Du kan aktivera **Azure Defender för SQL** på antingen prenumerations nivån eller resurs nivån
- Du kan aktivera skydd mot hot för **Azure Database for MariaDB/MySQL/postgresql** endast på resurs nivå

### <a name="to-enable-azure-defender-on-your-subscriptions-and-workspaces"></a>Så här aktiverar du Azure Defender på dina prenumerationer och arbets ytor:

- Så här aktiverar du Azure Defender på en prenumeration:

    1. Från Security Centerens huvud meny väljer du **pris & inställningar**.
    1. Välj den prenumeration eller arbets yta som du vill skydda.
    1. Välj **Azure Defender på** för att uppgradera.
    1. Välj **Spara**.

    > [!TIP]
    > Du märker att varje plan i Azure Defender priss ätts separat och kan ställas in på eller av separat. Du kanske till exempel vill inaktivera Azure Defender för App Service på prenumerationer som inte har någon tillhör ande Azure App Services plan. 

    :::image type="content" source="./media/security-center-pricing/pricing-tier-page.png" alt-text="Security Center sidan med priser i portalen":::

- Aktivera Azure Defender på flera prenumerationer eller arbets ytor:

    1. Välj **komma igång** från Security Center marginal List.

        På fliken **Uppgradera** visas de prenumerationer och arbets ytor som är berättigade till onboarding.

        :::image type="content" source="./media/enable-azure-defender/get-started-upgrade-tab.png" alt-text="Fliken uppgradera på sidan komma igång"::: 

    1. Från listan **Välj prenumerationer och arbets ytor för att aktivera Azure Defender på** väljer du de prenumerationer och arbets ytor som ska uppgraderas och väljer **Uppgradera** för att aktivera Azure Defender.

       - Om du väljer prenumerationer och arbets ytor som inte är tillgängliga för utvärderings versionen kommer nästa steg att uppgradera dem och avgifterna börjar.
       - Om du väljer en arbets yta som är berättigad till en kostnads fri utvärderings version påbörjar nästa steg en utvärderings version.

        :::image type="content" source="./media/enable-azure-defender/upgrade-selected-workspaces-and-subscriptions.png" alt-text="Uppgradera alla valda arbets ytor och prenumerationer från sidan komma igång":::


## <a name="next-steps"></a>Nästa steg

Nu när du har aktiverat Azure Defender aktiverar du automatisk data insamling med nödvändiga agenter och tillägg som beskrivs i [automatiska etablerings agenter och tillägg från Azure Security Center](security-center-enable-data-collection.md).