---
title: Hantera säkerhetsaviseringar i Azure Security Center | Microsoft Docs
description: I det här dokumentet beskrivs hur du hanterar och åtgärdar säkerhetsaviseringar med hjälp av funktionerna i Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: b88a8df7-6979-479b-8039-04da1b8737a7
ms.service: security-center
ms.topic: how-to
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/17/2021
ms.author: memildin
ms.openlocfilehash: a47ece762f2df3fa18cf2b79d338c28d4069c4ad
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100633490"
---
# <a name="manage-and-respond-to-security-alerts-in-azure-security-center"></a>Hantera och svara på säkerhets aviseringar i Azure Security Center

Det här avsnittet visar hur du visar och bearbetar Security Centers aviseringar och skyddar dina resurser.

Avancerade identifieringar som utlöser säkerhets aviseringar är bara tillgängliga med Azure Defender. Det finns en kostnadsfri utvärderingsversion. Information om hur du uppgraderar finns i [Aktivera Azure Defender](security-center-pricing.md#enable-azure-defender).

## <a name="what-are-security-alerts"></a>Vad är säkerhetsaviseringar?
Security Center samlar automatiskt in, analyserar och integrerar loggdata från Azure-resurser, nätverket och anslutna partnerlösningar som brandväggs- och slutpunktsskyddslösningar för att identifiera verkliga hot och minimera antalet falska positiva identifieringar. En lista över prioriterade säkerhetsvarningar visas i Security Center tillsammans med den information som du behöver för att snabbt undersöka problemet, samt rekommendationer för hur du åtgärdar ett angrepp.

Information om olika typer av aviseringar finns i [säkerhets aviseringar – en referens guide](alerts-reference.md).

En översikt över hur Security Center genererar aviseringar finns i [hur Azure Security Center identifierar och svarar på hot](security-center-alerts-overview.md).


## <a name="manage-your-security-alerts"></a>Hantera dina säkerhets aviseringar

1. På sidan Översikt för Security Center väljer du panelen **säkerhets aviseringar** överst på sidan eller länken från sid panelen..

    :::image type="content" source="media/security-center-managing-and-responding-alerts/overview-page-alerts-links.png" alt-text="Sidan säkerhets aviseringar visas på sidan med översikts sidan för Azure Security Center":::

    Sidan säkerhets aviseringar öppnas.

    :::image type="content" source="media/security-center-managing-and-responding-alerts/alerts-page.png" alt-text="Azure Security Center säkerhets aviserings listan":::

1. Om du vill filtrera aviserings listan väljer du något av de relevanta filtren. Du kan också lägga till ytterligare filter med alternativet **Lägg till filter** .

    :::image type="content" source="./media/security-center-managing-and-responding-alerts/alerts-adding-filters-small.png" alt-text="Lägga till filter i vyn aviseringar" lightbox="./media/security-center-managing-and-responding-alerts/alerts-adding-filters-large.png":::

    Listan uppdateras enligt de filtrerings alternativ som du har valt. Filtrering kan vara användbart. Kanske vill du till exempel se säkerhetsaviseringar från det senaste dygnet eftersom du undersöker ett potentiellt angrepp i systemet under den här tiden.


## <a name="respond-to-security-alerts"></a>Åtgärda säkerhetsaviseringar

1. Välj en avisering i listan **säkerhets aviseringar** . Ett sido fönster öppnas och visar en beskrivning av aviseringen och alla berörda resurser. 

    :::image type="content" source="./media/security-center-managing-and-responding-alerts/alerts-details-pane.png" alt-text="Vyn minispelare för en säkerhets avisering":::

    > [!TIP]
    > Med den här sidan öppen kan du snabbt granska aviserings listan med upp-och nedpilarna på tangent bordet.

1. Om du vill ha mer information väljer du **Visa fullständig information**.

    Den vänstra rutan på sidan säkerhets avisering visar information på hög nivå om säkerhets aviseringen: rubrik, allvarlighets grad, status, aktivitets tid, beskrivning av den misstänkta aktiviteten och den berörda resursen. Tillsammans med den berörda resursen är de Azure-taggar som är relevanta för resursen. Använd dessa för att härleda resursens organisatoriska kontext vid utredning av aviseringen.

    Den högra rutan innehåller fliken **aviserings information** som innehåller mer information om aviseringen för att hjälpa dig att undersöka problemet: IP-adresser, filer, processer med mera.
     
    ![Förslag på vad du kan göra om säkerhets aviseringar](./media/security-center-managing-and-responding-alerts/security-center-alert-remediate.png)

    I den högra rutan är fliken **vidta åtgärd** . Använd den här fliken för att vidta ytterligare åtgärder för säkerhets aviseringen. Åtgärder som:
    - *Minimera hotet* – ger manuella reparations steg för denna säkerhets avisering
    - *Förhindra framtida attacker* – ger säkerhets rekommendationer för att minska attack ytan, öka säkerheten position och därmed förhindra framtida attacker
    - *Utlös automatiserat svar* – ger möjlighet att utlösa en Logic app som ett svar på denna säkerhets avisering
    - *Ignorera liknande aviseringar* – ger möjlighet att ignorera framtida aviseringar med liknande egenskaper om aviseringen inte är relevant för din organisation

    ![Vidta åtgärd-fliken](./media/security-center-managing-and-responding-alerts/alert-take-action.png)




## <a name="see-also"></a>Se även

I det här dokumentet har du lärt dig hur du visar säkerhets aviseringar. Se följande sidor för relaterat material:

- [Konfigurera regler för att utelämna varningar](alerts-suppression-rules.md)
- [Automatisera svar på Security Center utlösare](workflow-automation.md)
- [Säkerhetsaviseringar – en referensguide](alerts-reference.md)
