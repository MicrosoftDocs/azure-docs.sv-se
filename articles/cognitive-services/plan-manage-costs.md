---
title: Planera för att hantera kostnader för Azure Cognitive Services
description: Lär dig att planera för och hantera kostnader för Azure Cognitive Services genom att använda kostnads analys i Azure Portal.
author: erhopf
ms.author: erhopf
ms.custom: subject-cost-optimization
ms.service: cognitive-services
ms.topic: how-to
ms.date: 12/15/2020
ms.openlocfilehash: db99fa5caff27a24aa04e4780b25ade3f7c25496
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "101699937"
---
# <a name="plan-and-manage-costs-for-azure-cognitive-services"></a>Planera och hantera kostnader för Azure Cognitive Services

Den här artikeln beskriver hur du planerar för och hanterar kostnader för Azure Cognitive Services. Först använder du pris Kalkylatorn för Azure för att planera för Cognitive Services kostnader innan du lägger till några resurser för tjänsten för att beräkna kostnaderna. När du lägger till Azure-resurser granskar du de uppskattade kostnaderna. När du har börjat använda Cognitive Services-resurser (till exempel tal, Visuellt innehåll, LUIS, Textanalys, Translator osv.) använder du Cost Management funktioner för att ställa in budgetar och övervaka kostnader. Du kan också granska prognostiserade kostnader och identifiera utgifts trender för att identifiera områden där du kanske vill handla. Kostnaderna för Cognitive Services är bara en del av månads kostnaderna på din Azure-faktura. Även om den här artikeln förklarar hur du planerar för och hanterar kostnader för Cognitive Services debiteras du för alla Azure-tjänster och-resurser som används i din Azure-prenumeration, inklusive tjänster från tredje part.

## <a name="prerequisites"></a>Förutsättningar

Kostnads analys i Cost Management stöder de flesta typer av Azure-konton, men inte alla. Om du vill se hela listan med kontotyper som stöds kan du läsa [Förstå Cost Management-data](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Om du vill visa kostnads data måste du ha minst Läs behörighet för ett Azure-konto. Mer information om hur du får åtkomst till Azure Cost Management finns i [Tilldela åtkomst till data](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

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

### <a name="using-azure-prepayment-credit-with-cognitive-services"></a>Använda Azures förskotts kredit med Cognitive Services

Du kan betala för Cognitive Services avgifter med din Azure-förskotts betalning (tidigare kallat betalnings åtagande) kredit. Du kan dock inte använda Azures förskotts kredit för att betala för avgifter för produkter och tjänster från tredje part, inklusive de som finns på Azure Marketplace.

## <a name="monitor-costs"></a>Övervaka kostnader

<!-- Note to Azure service writer: Modify the following as needed for your service. Replace example screenshots with ones taken for your service. If you need assistance capturing screenshots, ask banders for help. -->

När du använder Azure-resurser med Cognitive Services debiteras du kostnader. Kostnaderna för Azure Resource Usage varierar med tidsintervall (sekunder, minuter, timmar och dagar) eller per enhets användning (byte, megabyte osv.). Så snart som användning av en kognitiv tjänst (eller Cognitive Services) börjar kostnader uppkommer kostnader och du kan se kostnaderna i [kostnads analysen](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

När du använder kostnads analys kan du Visa Cognitive Services kostnader i grafer och tabeller i olika tidsintervall. Några exempel är per dag, innevarande och föregående månad och år. Du ser också kostnader för budget och prognostiserade kostnader. Att växla till längre vyer över tid kan hjälpa dig att identifiera utgifts trender. Och du ser var överförbrukning kan ha inträffat. Om du har skapat budgetar kan du också enkelt se var de överskreds.

Så här visar du Cognitive Services kostnader i kostnads analys:

1. Logga in på Azure-portalen.
2. Öppna omfånget i Azure Portal och välj **kostnads analys** i menyn. Gå till exempel till **prenumerationer**, Välj en prenumeration i listan och välj sedan  **kostnads analys** i menyn. Välj **omfång** för att växla till ett annat omfång i kostnads analys.
3. Som standard visas kostnaden för tjänster i det första Ring diagrammet. Markera det områden i diagrammet som är märkt Cognitive Services.

De faktiska månads kostnaderna visas när du först öppnar kostnads analys. Här är ett exempel som visar alla månatliga användnings kostnader.

:::image type="content" source="./media/cost-management/all-costs.png" alt-text="Exempel som visar ackumulerade kostnader för en prenumeration":::

- Om du vill begränsa kostnaderna för en enskild tjänst, till exempel Cognitive Services, väljer du **Lägg till filter** och väljer sedan **tjänst namn**. Välj sedan **Cognitive Services**.

Här är ett exempel som visar kostnader för just Cognitive Services.

:::image type="content" source="./media/cost-management/cognitive-services-costs.png" alt-text="Exempel som visar ackumulerade kostnader för Cognitive Services":::

I föregående exempel visas den aktuella kostnaden för tjänsten. Kostnader per Azure-regioner (platser) och Cognitive Services kostnader per resurs grupp visas också. Härifrån kan du utforska kostnaderna på egen hand.

## <a name="create-budgets"></a>Skapa budgetar

Du kan skapa [budgetar](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) för att hantera kostnader och skapa [aviseringar](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) som automatiskt meddelar mottagarna om kostnadsavvikelser och risker för överförbrukning. Aviseringar baseras på utgifter jämfört med budget- och kostnadströsklar. Budgetar och aviseringar skapas för Azure-prenumerationer och resurs grupper, så de är användbara som en del av en övergripande kostnads övervaknings strategi. 

Budgetar kan skapas med filter för vissa resurser eller tjänster i Azure om du vill ha mer detaljerad information i din övervakning. Filter hjälper till att se till att du inte av misstag skapar nya resurser som kostar dig ytterligare pengar. Mer information om filter alternativen när du skapar en budget finns i alternativ för [grupper och filter](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Exportera kostnadsdata

Du kan också [Exportera dina kostnads data](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) till ett lagrings konto. Detta är användbart när du behöver eller andra för att utföra ytterligare data analyser för kostnader. Ekonomi team kan till exempel analysera data med hjälp av Excel eller Power BI. Du kan exportera dina kostnader enligt ett dags-, vecko-eller månads schema och ange ett anpassat datum intervall. Att exportera kostnads data är det rekommenderade sättet att hämta kostnads data uppsättningar.

## <a name="next-steps"></a>Nästa steg

- Lär dig [hur du optimerar din moln investering med Azure Cost Management](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Lär dig mer om att hantera kostnader med [kostnads analys](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Lär dig mer om att [förhindra oväntade kostnader](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Ta den [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) guidade utbildningen.