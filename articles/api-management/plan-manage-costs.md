---
title: Planera och hantera kostnader för Azure API Management
description: Lär dig att planera för och hantera kostnader för Azure API Management genom att använda kostnads analys i Azure Portal.
author: dlepow
ms.author: apimpm
ms.custom: subject-cost-optimization
ms.service: api-management
ms.topic: how-to
ms.date: 12/15/2020
ms.openlocfilehash: ab634e697e9d46d9e3b8c1b9597fc7d0544ba991
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97633022"
---
# <a name="plan-and-manage-costs-for-api-management"></a>Planera och hantera kostnader för API Management

Den här artikeln beskriver hur du planerar för och hanterar kostnader för Azure API Management. Först använder du pris Kalkylatorn för Azure för att planera för API Management kostnader innan du lägger till några resurser för tjänsten för att beräkna kostnaderna. När du har börjat använda API Management resurser använder du Cost Management funktioner för att ställa in budgetar och övervaka kostnader. Du kan också granska prognostiserade kostnader och identifiera utgifts trender för att identifiera områden där du kanske vill handla. 

Kostnaderna för API Management är bara en del av månads kostnaderna på din Azure-faktura. Även om den här artikeln förklarar hur du planerar för och hanterar kostnader för API Management debiteras du för alla Azure-tjänster och-resurser som används i din Azure-prenumeration, inklusive tjänster från tredje part.

## <a name="prerequisites"></a>Förutsättningar

Kostnads analys i Cost Management stöder de flesta typer av Azure-konton, men inte alla. Om du vill se hela listan med kontotyper som stöds kan du läsa [Förstå Cost Management-data](https://docs.microsoft.com/azure/cost-management-billing/costs/understand-cost-mgt-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Om du vill visa kostnads data måste du ha minst Läs behörighet för ett Azure-konto. Mer information om hur du får åtkomst till Azure Cost Management finns i [Tilldela åtkomst till data](https://docs.microsoft.com/azure/cost-management/assign-access-acm-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="estimate-costs-before-using-api-management"></a>Beräkna kostnader innan du använder API Management

Använd [pris Kalkylatorn för Azure](https://azure.microsoft.com/pricing/calculator/) för att beräkna kostnader innan du lägger till API Management. 

1. Sök efter *API Management* eller Välj **integrations**  >  **API Management**.
1. Välj **Visa** för att lägga till en standard kostnads uppskattning för en API Management tjänst instans.

> [!NOTE]
> Kostnaderna som visas i det här exemplet är endast i demonstrations syfte. Se [API Management priser](https://azure.microsoft.com/pricing/details/api-management/) för den senaste pris informationen.

:::image type="content" source="media/plan-manage-costs/pricing-calculator-developer-tier.png" alt-text="Uppskatta kostnader för nivån för utvecklare":::

* Standardkostnads uppskattningen baseras på en API Management tjänst instans i [tjänste nivån](api-management-features.md) för **utvecklare** med 1 [kapacitets enhet](api-management-capacity.md). Developer-nivån är för användnings fall och utvärderingar för icke-produktion. Det backas inte upp av ett service nivå avtal.

* Om du vill beräkna kostnader för ytterligare kapacitets enheter eller en annan tjänst nivå väljer du andra alternativ i list rutorna **enheter** och **nivå** .

* Beroende på funktions tillgänglighet och tjänst nivå kan ytterligare avgifter tillkomma för användning av [egna gatewayer](self-hosted-gateway-overview.md).

Ytterligare information om priser och funktioner finns i:

* [API Management priser](https://azure.microsoft.com/pricing/details/api-management/)
* [Funktions-baserad jämförelse av Azure API Management-nivåer](api-management-features.md)

### <a name="using-monetary-credit-with-api-management"></a>Använda monetär kredit med API Management

Du kan betala för API Management avgifter med din kredit för ditt EA-belopp. Du kan dock inte använda EA-krediter för att betala för avgifter för produkter och tjänster från tredje part, inklusive de som finns på Azure Marketplace.

## <a name="monitor-costs"></a>Övervaka kostnader

När du använder Azure-resurser med API Management debiteras du kostnader. Kostnaderna för Azure Resource Usage varierar med tidsintervall (sekunder, minuter, timmar och dagar) eller per enhets användning (byte, megabyte osv.). Så snart som API Management användning börjar kostnader och du kan se kostnaderna för [kostnads analys](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

När du använder kostnads analys kan du Visa API Management kostnader i grafer och tabeller i olika tidsintervall. Några exempel är per dag, innevarande och föregående månad och år. Du ser också kostnader för budget och prognostiserade kostnader. Att växla till längre vyer över tid kan hjälpa dig att identifiera utgifts trender. Och du ser var överförbrukning kan ha inträffat. Om du har skapat budgetar kan du också enkelt se var de överskreds.

> [!NOTE]
> Kostnaderna som visas i det här exemplet är endast i demonstrations syfte. Kostnaderna kan variera beroende på resursanvändningen och den aktuella prissättningen.

Så här visar du API Management kostnader i kostnads analys:

1. Logga in på [Azure-portalen](https://azure.microsoft.com).
1. Öppna fönstret **Cost Management + fakturering** , Välj **kostnads hantering** på menyn och välj sedan ett **fakturerings omfång**. Välj till exempel en prenumeration i listan.
1. Välj **Cost Management** på menyn och välj sedan **kostnads analys**.
1. Som standard visas månads kostnaden för alla tjänster i det första Ring diagrammet. 

    :::image type="content" source="media/plan-manage-costs/api-management-cost-analysis.png" alt-text="Månads kostnader för prenumeration":::

1. Om du vill begränsa kostnaderna för en enskild tjänst, till exempel API Management, väljer du **Lägg till filter** och väljer sedan **tjänst namn**. Välj sedan **API Management**.

    :::image type="content" source="media/plan-manage-costs/api-management-apim-cost-analysis.png" alt-text="Exempel som visar ackumulerade kostnader för API Management":::

I föregående exempel visas den aktuella kostnaden för tjänsten. Kostnader per Azure-regioner (platser) och API Management kostnader per resurs grupp visas också. Härifrån kan du utforska kostnaderna på egen hand.

## <a name="create-budgets"></a>Skapa budgetar

Du kan skapa [budgetar](https://docs.microsoft.com/azure/cost-management/tutorial-acm-create-budgets?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) för att hantera kostnader och skapa [aviseringar](https://docs.microsoft.com/azure/cost-management/cost-mgt-alerts-monitor-usage-spending?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) som automatiskt meddelar mottagarna om kostnadsavvikelser och risker för överförbrukning. Aviseringar baseras på utgifter jämfört med budget- och kostnadströsklar. Budgetar och aviseringar skapas för Azure-prenumerationer och resurs grupper, så de är användbara som en del av en övergripande kostnads övervaknings strategi. 

Budgetar kan skapas med filter för vissa resurser eller tjänster i Azure om du vill ha mer detaljerad information i din övervakning. Filter hjälper till att se till att du inte av misstag skapar nya resurser som kostar dig ytterligare pengar. Mer information om filter alternativen när du skapar en budget finns i alternativ för [grupper och filter](https://docs.microsoft.com/azure/cost-management-billing/costs/group-filter?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Exportera kostnadsdata

Du kan också [Exportera dina kostnads data](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) till ett lagrings konto. Detta är användbart när du behöver andra för att kunna utföra ytterligare data analyser för kostnader. Ett ekonomi team kan till exempel analysera data med hjälp av Excel eller Power BI. Du kan exportera dina kostnader enligt ett dags-, vecko-eller månads schema och ange ett anpassat datum intervall. Att exportera kostnads data är det rekommenderade sättet att hämta kostnads data uppsättningar.

## <a name="other-ways-to-manage-and-reduce-costs-for-api-management"></a>Andra sätt att hantera och minska kostnaderna för API Management

### <a name="choose-tier"></a>Välj nivå

Granska den [funktionsbaserade jämförelsen av Azure API Management-nivåerna](api-management-features.md) för att avgöra vilken tjänst nivå som kan vara lämplig för dina scenarier. De olika tjänst nivåerna stöder kombinationer av funktioner och funktioner som är utformade för olika användnings fall, med olika kostnader. [Uppgradera](upgrade-and-scale.md) till en annan tjänst nivå när som helst.

* **Förbruknings** tjänst nivån ger ett enkelt, utan Server alternativ som debiteras utan fasta kostnader. Du faktureras baserat på antalet API-anrop till tjänsten över ett visst tröskelvärde. Kapaciteten skalas också automatiskt utifrån belastningen på tjänsten.
* Andra API Managements nivåer debiteras månads kostnaden och ger större data flöden och rikare funktions uppsättningar för utvärderings-och produktions arbets belastningar.

### <a name="scale-using-capacity-units"></a>Skala med kapacitets enheter

Förutom i förbruknings tjänst nivån, API Management stöder skalning genom att lägga till eller ta bort [*kapacitets enheter*](api-management-capacity.md). När belastningen ökar på en API Management-instans kan det vara mer ekonomiskt att lägga till kapacitets enheter än att uppgradera till en högre tjänst nivå. Det maximala antalet enheter beror på tjänst nivån.

Varje kapacitets enhet har en viss bearbetnings funktion för begär Ande som är beroende av tjänst nivån. En enhet av Basic-nivån har till exempel ett uppskattat maximalt data flöde på cirka 1 000 begär Anden per sekund. 

När du lägger till eller tar bort enheter, kapacitet och kostnads skala proportionellt. Två enheter av standard nivån tillhandahåller till exempel ett uppskattat data flöde på cirka 2 000 begär Anden per sekund. Det faktiska data flödet kan variera på grund av storleken på begär Anden eller svar, anslutnings mönster, antal klienter som begär förfrågningar och andra faktorer.

[Övervaka](api-management-howto-use-azure-monitor.md) kapacitets måttet för API Management-instansen för att fatta beslut om att skala eller uppgradera en API Management instans för att få plats med mer belastning.

## <a name="next-steps"></a>Nästa steg

- Lär dig [hur du optimerar din moln investering med Azure Cost Management](https://docs.microsoft.com/azure/cost-management-billing/costs/cost-mgt-best-practices?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Lär dig mer om att hantera kostnader med [kostnads analys](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Lär dig mer om att [förhindra oväntade kostnader](https://docs.microsoft.com/azure/cost-management-billing/manage/getting-started?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Ta den [Cost Management](https://docs.microsoft.com/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) guidade utbildningen.
- Läs mer om API Management- [kapacitet](api-management-capacity.md).
- Se steg för att skala och uppgradera API Management med hjälp av [Azure Portal](upgrade-and-scale.md)och lär dig mer om [autoskalning](api-management-howto-autoscale.md).