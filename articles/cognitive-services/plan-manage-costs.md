---
title: Planera för att hantera kostnader för Azure Cognitive Services
description: Lär dig att planera för och hantera kostnader för Azure Cognitive Services genom att använda kostnads analys i Azure Portal.
author: erhopf
ms.author: erhopf
ms.custom: subject-cost-optimization
ms.service: cognitive-services
ms.topic: how-to
ms.date: 12/15/2020
ms.openlocfilehash: 48e111ac8b2b4fd3c1e2fee568e20699896dfff3
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/20/2020
ms.locfileid: "97705792"
---
# <a name="plan-and-manage-costs-for-azure-cognitive-services"></a>Planera och hantera kostnader för Azure Cognitive Services

Den här artikeln beskriver hur du planerar för och hanterar kostnader för Azure Cognitive Services. Först använder du pris Kalkylatorn för Azure för att planera för Cognitive Services kostnader innan du lägger till några resurser för tjänsten för att beräkna kostnaderna. När du lägger till Azure-resurser granskar du de uppskattade kostnaderna. När du har börjat använda Cognitive Services-resurser (till exempel tal, Visuellt innehåll, LUIS, Textanalys, Translator osv.) använder du Cost Management funktioner för att ställa in budgetar och övervaka kostnader. Du kan också granska prognostiserade kostnader och identifiera utgifts trender för att identifiera områden där du kanske vill handla. Kostnaderna för Cognitive Services är bara en del av månads kostnaderna på din Azure-faktura. Även om den här artikeln förklarar hur du planerar för och hanterar kostnader för Cognitive Services debiteras du för alla Azure-tjänster och-resurser som används i din Azure-prenumeration, inklusive tjänster från tredje part.

## <a name="prerequisites"></a>Förutsättningar

Kostnads analys i Cost Management stöder de flesta typer av Azure-konton, men inte alla. Om du vill se hela listan med kontotyper som stöds kan du läsa [Förstå Cost Management-data](https://docs.microsoft.com/azure/cost-management-billing/costs/understand-cost-mgt-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Om du vill visa kostnads data måste du ha minst Läs behörighet för ett Azure-konto. Mer information om hur du får åtkomst till Azure Cost Management finns i [Tilldela åtkomst till data](https://docs.microsoft.com/azure/cost-management/assign-access-acm-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

<!--Note for Azure service writer: If you have other prerequisites for your service, insert them here -->

## <a name="estimate-costs-before-using-cognitive-services"></a>Beräkna kostnader innan du använder Cognitive Services

Använd [pris Kalkylatorn för Azure](https://azure.microsoft.com/pricing/calculator/) för att beräkna kostnader innan du lägger till Cognitive Services.

:::image type="content" source="media/cognitive-services-pricing-calculator.png" alt-text="Pris kalkylator för Azure för Cognitive Services" border="true":::

När du lägger till nya resurser i din arbets yta går du tillbaka till den här kalkylatorn och lägger till samma resurs här för att uppdatera dina kostnads beräkningar.

Mer information finns i [priser för Azure Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/).

## <a name="understand-the-full-billing-model-for-cognitive-services"></a>Förstå den fullständiga fakturerings modellen för Cognitive Services

Cognitive Services körs på Azure-infrastruktur som [periodiserar kostnader](https://azure.microsoft.com/pricing/details/cognitive-services/) när du distribuerar den nya resursen. Det är viktigt att förstå att ytterligare infrastruktur kan periodisera kostnader. Du måste hantera den kostnaden när du gör ändringar i distribuerade resurser. 

### <a name="costs-that-typically-accrue-with-cognitive-services"></a>Kostnader som vanligt vis påförs med Cognitive Services

När du har distribuerat en Azure-resurs bestäms kostnaderna av pris nivån och de API-anrop du gör till din slut punkt. Om tjänsten som du använder har en åtagande nivå, kan det hända att du tar över de tilldelade anropen på din nivå.

Ytterligare kostnader kan uppstå när du använder dessa tjänster:

#### <a name="qna-maker"></a>QnA Maker

När du skapar resurser för QnA Maker kan även resurser för andra Azure-tjänster skapas. De omfattar:

- [Azure App Service (för körning)](https://azure.microsoft.com/pricing/details/app-service/)
- [Azure Kognitiv sökning (för data)](https://azure.microsoft.com/pricing/details/search/)
 
### <a name="costs-that-might-accrue-after-resource-deletion"></a>Kostnader som kan uppstå efter borttagning av resursen

#### <a name="qna-maker"></a>QnA Maker

När du har tagit bort QnA Maker-resurser kan följande resurser fortsätta att existera. De fortsätter att Periodisera kostnader tills du tar bort dem.

- [Azure App Service (för körning)](https://azure.microsoft.com/pricing/details/app-service/)
- [Azure Kognitiv sökning (för data)](https://azure.microsoft.com/pricing/details/search/)

### <a name="using-monetary-credit-with-cognitive-services"></a>Använda monetär kredit med Cognitive Services

Du kan betala för Cognitive Services avgifter med din kredit för ditt EA-belopp. Du kan dock inte använda EA-krediter för att betala för avgifter för produkter och tjänster från tredje part, inklusive de som finns på Azure Marketplace.

## <a name="create-budgets"></a>Skapa budgetar

Du kan skapa [budgetar](https://docs.microsoft.com/azure/cost-management/tutorial-acm-create-budgets?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) för att hantera kostnader och skapa [aviseringar](https://docs.microsoft.com/azure/cost-management/cost-mgt-alerts-monitor-usage-spending?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) som automatiskt meddelar mottagarna om kostnadsavvikelser och risker för överförbrukning. Aviseringar baseras på utgifter jämfört med budget- och kostnadströsklar. Budgetar och aviseringar skapas för Azure-prenumerationer och resurs grupper, så de är användbara som en del av en övergripande kostnads övervaknings strategi. 

Budgetar kan skapas med filter för vissa resurser eller tjänster i Azure om du vill ha mer detaljerad information i din övervakning. Filter hjälper till att se till att du inte av misstag skapar nya resurser som kostar dig ytterligare pengar. Mer information om filter alternativen när du skapar en budget finns i alternativ för [grupper och filter](https://docs.microsoft.com/azure/cost-management-billing/costs/group-filter?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Exportera kostnadsdata

Du kan också [Exportera dina kostnads data](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) till ett lagrings konto. Detta är användbart när du behöver eller andra för att utföra ytterligare data analyser för kostnader. Ekonomi team kan till exempel analysera data med hjälp av Excel eller Power BI. Du kan exportera dina kostnader enligt ett dags-, vecko-eller månads schema och ange ett anpassat datum intervall. Att exportera kostnads data är det rekommenderade sättet att hämta kostnads data uppsättningar.

<!--
## Other ways to manage and reduce costs for Cognitive Services

Work with Dean to complete this section in 2021.

-->

## <a name="next-steps"></a>Nästa steg

- Lär dig [hur du optimerar din moln investering med Azure Cost Management](https://docs.microsoft.com/azure/cost-management-billing/costs/cost-mgt-best-practices?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Lär dig mer om att hantera kostnader med [kostnads analys](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Lär dig mer om att [förhindra oväntade kostnader](https://docs.microsoft.com/azure/cost-management-billing/manage/getting-started?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Ta den [Cost Management](https://docs.microsoft.com/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) guidade utbildningen.
