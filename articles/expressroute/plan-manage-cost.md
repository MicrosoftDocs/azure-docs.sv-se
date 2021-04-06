---
title: Planera för att hantera kostnader för Azure-ExpressRoute
description: Lär dig att planera för och hantera kostnader för Azure-ExpressRoute med hjälp av kostnads analys i Azure Portal.
author: duongau
ms.author: duau
ms.custom: subject-cost-optimization
ms.service: expressroute
ms.topic: how-to
ms.date: 11/30/2020
ms.openlocfilehash: de8405477611d62b8a46e8b6b645887cc4d30099
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98784249"
---
# <a name="plan-and-manage-costs-for-azure-expressroute"></a>Planera och hantera kostnader för Azure-ExpressRoute

Den här artikeln beskriver hur du planerar för och hanterar kostnader för ExpressRoute. Först använder du pris Kalkylatorn för Azure för att planera för ExpressRoute-kostnader innan du lägger till några resurser för tjänsten för att beräkna kostnaderna. När du lägger till Azure-resurser granskar du de uppskattade kostnaderna. 

När du har börjat använda ExpressRoute-resurser använder du Cost Management funktioner för att ställa in budgetar och övervaka kostnader. Du kan också granska prognostiserade kostnader och identifiera utgifts trender för att identifiera områden där du kanske vill handla. 

Tänk på att kostnader för ExpressRoute bara är en del av månads kostnaderna på din Azure-faktura. Även om den här artikeln förklarar hur du planerar för och hanterar kostnader för ExpressRoute debiteras du för alla Azure-tjänster och-resurser som används i din Azure-prenumeration, inklusive tjänster från tredje part.

## <a name="prerequisites"></a>Förutsättningar

Kostnads analys i Cost Management stöder de flesta typer av Azure-konton, men inte alla. Om du vill se hela listan med kontotyper som stöds kan du läsa [Förstå Cost Management-data](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Om du vill visa kostnads data måste du ha minst Läs behörighet för ett Azure-konto. 

Mer information om hur du får åtkomst till Azure Cost Management finns i [Tilldela åtkomst till data](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="local-vs-standard-vs-premium"></a>Lokal vs. standard vs. Premium

ExpressRoute har tre olika krets-SKU: er: [*lokal*](./expressroute-faqs.md#expressroute-local), *standard* och [*Premium*](./expressroute-faqs.md#expressroute-premium). Hur du debiteras för ExpressRoute-användningen varierar mellan dessa tre SKU-typer. Med lokal SKU debiteras du automatiskt med ett obegränsat data abonnemang. Med standard-och Premium-SKU kan du välja mellan en avgiftsbelagd eller en obegränsad data plan. Alla ingångs data är kostnads fria, förutom när du använder Global Reach-tillägget. Det är viktigt att förstå vilka SKU-typer och data abonnemang som fungerar bäst för din arbets belastning för bästa möjliga optimering av kostnader och budget.

## <a name="estimate-costs"></a>Uppskatta kostnader

Använd [pris Kalkylatorn för Azure](https://azure.microsoft.com/pricing/calculator/) för att beräkna kostnaderna innan du skapar en ExpressRoute-krets. 

1. Välj **nätverk** till vänster och välj sedan Azure- **ExpressRoute** att starta. 

1. Välj lämplig *zon* beroende på din peering-plats. Se [ExpressRoute-anslutnings leverantörer](./expressroute-locations-providers.md#partners) för att välja lämplig *zon* i list rutan. 

1. Välj sedan den *SKU*, *krets hastighet* och det *data abonnemang* som du vill beräkna. 

1. I den *extra utgående data överföringen* anger du en uppskattning i GB av hur mycket utgående data som du kan använda under en månad. 

1. Slutligen kan du lägga till *Global Reach-tillägget* i beräkningen.

Följande skärm bild visar kostnads uppskattningen med hjälp av Kalkylatorn:

:::image type="content" source="media/plan-manage-cost/capacity-calculator-cost-estimate.png" alt-text="ExpressRoute kostnads uppskattning i Azure kalkylator":::

Mer information finns i [priser för Azure ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute/).

### <a name="expressroute-gateway-estimated-cost"></a>Beräknad kostnad för ExpressRoute-Gateway

Om du använder en ExpressRoute-Gateway för att länka ett virtuellt nätverk till ExpressRoute-kretsen använder du följande steg för att beräkna kostnaden för gateway-användning.

1. Välj **nätverk** till vänster och välj sedan **VPN gateway** för att börja. 

1. Välj *region* för gatewayen och ändra sedan *typ* till **ExpressRoute-gatewayer**.

1. Välj *typ av Gateway* i list rutan.

1. Ange *Gateway-timmarna*. (720 timmar = 30 dagar)

## <a name="understand-the-full-billing-model-for-expressroute"></a>Förstå den fullständiga fakturerings modellen för ExpressRoute

ExpressRoute körs på Azure-infrastruktur som periodiserar kostnader tillsammans med ExpressRoute när du distribuerar den nya resursen. Det är viktigt att förstå att ytterligare infrastruktur kan periodisera kostnader. Du måste hantera den kostnaden när du gör ändringar i distribuerade resurser. 

### <a name="costs-that-typically-accrue-with-expressroute"></a>Kostnader som normalt påförs med ExpressRoute

När du skapar en ExpressRoute-krets kan du välja att skapa en ExpressRoute-Gateway för att länka ditt virtuella nätverk till kretsen. Gatewayer debiteras enligt Tim pris och kostnaden för en ExpressRoute-krets. Se [priser för ExpressRoute](https://azure.microsoft.com/en-us/pricing/details/expressroute) och välj ExpressRoute-gatewayer för att se priser för olika Gateway-SKU: er.
 
### <a name="costs-might-accrue-after-resource-deletion"></a>Kostnader kan uppstå efter borttagning av resurs

Om du har en ExpressRoute-gateway när du har tagit bort ExpressRoute-kretsen debiteras du ändå för kostnaden tills du tar bort den.

### <a name="using-azure-prepayment-credit"></a>Använda Azures förskotts kredit

Du kan betala för ExpressRoute-kostnader med din Azure-förskotts betalning (tidigare kallat betalnings åtagande) kredit. Du kan dock inte använda Azures förskotts kredit för att betala för avgifter för tredjepartsprodukter och tjänster från tredje part, inklusive de som finns på Azure Marketplace.

## <a name="monitor-costs"></a>Övervaka kostnader

När du använder Azure-resurser med ExpressRoute debiteras du kostnader. Kostnaderna för Azure Resource Usage varierar med tidsintervall (sekunder, minuter, timmar och dagar) eller per enhets användning (byte, megabyte osv.) Så snart ExpressRoute används börjar kostnaderna och du kan se kostnaderna för [kostnads analys](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

När du använder kostnads analys kan du Visa ExpressRoute-krets kostnader i grafer och tabeller i olika tidsintervall. Några exempel är per dag, innevarande och föregående månad och år. Du ser också kostnader för budget och prognostiserade kostnader. Att växla till längre vyer över tid kan hjälpa dig att identifiera utgifts trender. Och du ser var överförbrukning kan ha inträffat. Om du har skapat budgetar kan du också enkelt se var de överskreds.

Så här visar du ExpressRoute-kostnader i kostnads analys:

1. Logga in på Azure-portalen.

1. Gå till **prenumerationer**, Välj en prenumeration i listan och välj sedan  **kostnads analys** i menyn. Välj **omfång** för att växla till ett annat omfång i kostnads analys. Som standard visas kostnaden för tjänster i det första Ring diagrammet.

    De faktiska månads kostnaderna visas när du först öppnar kostnads analys. Här är ett exempel som visar alla månatliga användnings kostnader.

    :::image type="content" source="media/plan-manage-cost/cost-analysis-pane.png" alt-text="Exempel som visar ackumulerade kostnader för en prenumeration":::
    

1.  Om du vill begränsa kostnaderna för en enskild tjänst, till exempel ExpressRoute, väljer du **Lägg till filter** och väljer sedan **tjänst namn**. Välj sedan **ExpressRoute**.

    Här är ett exempel som visar kostnader för bara ExpressRoute.

    :::image type="content" source="media/plan-manage-cost/cost-analysis-pane-expressroute.png" alt-text="Exempel som visar ackumulerade kostnader för ExpressRoute":::

I föregående exempel visas den aktuella kostnaden för tjänsten. Kostnader per Azure-regioner (platser) och ExpressRoute-kostnader per resurs grupp visas också. Härifrån kan du utforska kostnaderna på egen hand.

## <a name="create-budgets-and-alerts"></a>Skapa budgetar och aviseringar

Du kan skapa [budgetar](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) för att hantera kostnader och skapa [aviseringar](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) som automatiskt meddelar mottagarna om kostnadsavvikelser och risker för överförbrukning. Aviseringar baseras på utgifter jämfört med budget- och kostnadströsklar. Budgetar och aviseringar skapas för Azure-prenumerationer och resurs grupper, så de är användbara som en del av en övergripande kostnads övervaknings strategi. 

Budgetar kan skapas med filter för vissa resurser eller tjänster i Azure om du vill ha mer detaljerad information i din övervakning. Filter hjälper till att se till att du inte av misstag skapar nya resurser som kostar dig ytterligare pengar. Mer information om filter alternativen när du skapar en budget finns i [alternativ för grupper och filter](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Exportera kostnadsdata

Du kan också [Exportera dina kostnads data](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) till ett lagrings konto. Detta är användbart när du behöver eller andra för att utföra ytterligare data analyser för kostnader. Ett ekonomi team kan till exempel analysera data med hjälp av Excel eller Power BI. Du kan exportera dina kostnader enligt ett dags-, vecko-eller månads schema och ange ett anpassat datum intervall. Att exportera kostnads data är det rekommenderade sättet att hämta kostnads data uppsättningar.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om hur priser fungerar med Azure ExpressRoute. Se [priser för Azure ExpressRoute-översikt](https://azure.microsoft.com/en-us/pricing/details/expressroute/).
- Lär dig [hur du optimerar din moln investering med Azure Cost Management](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Lär dig mer om att hantera kostnader med [kostnads analys](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Lär dig mer om att [förhindra oväntade kostnader](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Ta den [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) guidade utbildningen.