---
title: Planera för att hantera kostnader för App Service
description: Lär dig att planera för och hantera kostnader för Azure App Service genom att använda kostnads analys i Azure Portal.
ms.custom: subject-cost-optimization
ms.service: app-service
ms.topic: how-to
ms.date: 01/01/2021
ms.openlocfilehash: 3df08705859678525526f8fef198826f58249d8b
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99573372"
---
# <a name="plan-and-manage-costs-for-azure-app-service"></a>Planera och hantera kostnader för Azure App Service

<!-- Check out the following published examples:
- [https://docs.microsoft.com/azure/cosmos-db/plan-manage-costs](../cosmos-db/plan-manage-costs.md)
- [https://docs.microsoft.com/azure/storage/common/storage-plan-manage-costs](../storage/common/storage-plan-manage-costs.md)
- [https://docs.microsoft.com/azure/machine-learning/concept-plan-manage-cost](../machine-learning/concept-plan-manage-cost.md)
-->

<!-- Note for Azure service writer: Links to Cost Management articles are full URLS with the ?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn campaign suffix. Leave those URLs intact. They're used to measure traffic to Cost Management articles.
-->

<!-- Note for Azure service writer: Modify the following for your service. -->

I den här artikeln beskrivs hur du planerar för och hanterar kostnader för Azure App Service. Först använder du pris Kalkylatorn för Azure för att planera för App Service kostnader innan du lägger till några resurser för tjänsten för att beräkna kostnaderna. När du lägger till Azure-resurser granskar du de uppskattade kostnaderna. När du har börjat använda App Service resurser använder du [Cost Management](../cost-management-billing/index.yml?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) funktioner för att ställa in budgetar och övervaka kostnader. Du kan också granska prognostiserade kostnader och identifiera utgifts trender för att identifiera områden där du kanske vill handla. Kostnaderna för Azure App Service är bara en del av månads kostnaderna på din Azure-faktura. Även om den här artikeln förklarar hur du planerar för och hanterar kostnader för App Service debiteras du för alla Azure-tjänster och-resurser som används i din Azure-prenumeration, inklusive tjänster från tredje part.

## <a name="relevant-costs-for-app-service"></a>Relevanta kostnader för App Service

App Service körs på Azure-infrastruktur som periodiserar kostnader. Det är viktigt att förstå att ytterligare infrastruktur kan periodisera kostnader. Du måste hantera den kostnaden när du gör ändringar i distribuerade resurser.

### <a name="costs-that-accrue-with-azure-app-service"></a>Kostnader som påförs med Azure App Service

Beroende på vilken funktion du använder i App Service kan följande kostnads upplupna resurser skapas:

- **App Service plan**  Krävs för att vara värd för en App Service app.
- **Isolerad nivå**  En [Virtual Network](../virtual-network/index.yml) krävs för en app service miljö.
- **Säkerhetskopiera**  Det krävs ett [lagrings konto](../storage/index.yml) för att göra säkerhets kopior.
- **Diagnostikloggar**  Du kan välja [lagrings konto](../storage/index.yml) som loggnings alternativ eller integrera med [Azure Log Analytics](../azure-monitor/log-query/log-analytics-tutorial.md).
- **App service certifikat**  Certifikat som du köper i Azure måste behållas i [Azure Key Vault](../key-vault/index.yml).

Andra kostnads resurser för App Service är (se [App Service prissättning](https://azure.microsoft.com/pricing/details/app-service/) för mer information):

- [App Service domäner](manage-custom-dns-buy-domain.md)  Din prenumeration debiteras för domän registreringen varje år om du aktiverar automatisk förnyelse.
- [App service certifikat](configure-ssl-certificate.md#import-an-app-service-certificate)  Engångs avgift vid inköps tillfället. Om du har flera under domäner för att skydda kan du minska kostnaderna genom att köpa ett certifikat med jokertecken i stället för flera standard certifikat.
- [IP-baserade certifikat bindningar](configure-ssl-bindings.md#create-binding)  Bindningen är konfigurerad på ett certifikat på App-nivå. Kostnaderna periodiseras för varje bindning. För **standard** -nivån och ovan debiteras inte den första IP-baserade bindningen.

### <a name="costs-that-might-accrue-after-resource-deletion"></a>Kostnader som kan uppstå efter borttagning av resursen

När du tar bort alla appar i en App Service plan fortsätter planen att debiteras enligt dess konfigurerade pris nivå och antalet instanser. Ta bort planen eller skala ned den till den **kostnads fria** nivån för att undvika oönskade kostnader.

När du har tagit bort Azure App Service-resurser kan resurser från relaterade Azure-tjänster fortsätta att existera. De fortsätter att Periodisera kostnader tills du tar bort dem. Exempel:

- Virtual Network som du har skapat för en **isolerad** nivå App Service plan
- Lagrings konton som du har skapat för att lagra säkerhets kopior eller diagnostikloggar
- Key Vault du har skapat för att lagra App Service certifikat
- Logga analys namn rymder som du har skapat för att leverera diagnostikloggar
- [Reservationer av instans eller stämpel](#azure-reservations) för App Service som ännu inte har gått ut

### <a name="using-monetary-credit-with-azure-app-service"></a>Använda monetär kredit med Azure App Service

Du kan betala för Azure App Service avgifter med din Azure-förskotts betalning (tidigare kallat betalnings åtagande) kredit. Du kan dock inte använda Azures förskotts kredit för att betala för avgifter för produkter och tjänster från tredje part, inklusive de från Azure Marketplace.

## <a name="estimate-costs"></a>Uppskatta kostnader

Ett enkelt sätt att beräkna och optimera dina App Service kostnader i förväg är att använda [pris Kalkylatorn för Azure](https://azure.microsoft.com/pricing/calculator/).

Om du vill använda pris kalkylatorn klickar du på **App Service** på fliken **produkter** . Rulla sedan ned för att arbeta med kalkylatorn. Följande skärm bild är ett exempel som inte återspeglar den aktuella prissättningen.

![Exempel som visar uppskattad kostnad i pris Kalkylatorn för Azure](media/overview-manage-costs/pricing-calculator.png)

### <a name="review-estimated-costs-in-the-azure-portal"></a>Gå igenom kostnadsuppskattningarna på Azure-portalen

När du skapar en App Service app eller en App Service plan kan du se de uppskattade kostnaderna.

Så här skapar du en app och visar det beräknade priset:

1. Rulla ned till **App Service plan** på sidan Skapa och klicka på **Skapa ny**.
1. Ange ett namn och klicka på **OK**.
1. Klicka på **ändra storlek** bredvid **SKU och storlek**.
1. Granska det beräknade priset som visas i sammanfattningen. Följande skärm bild är ett exempel som inte återspeglar den aktuella prissättningen.

    ![Granska uppskattad kostnad för varje pris nivå i portalen](media/overview-manage-costs/pricing-estimates.png)

Om din Azure-prenumeration har en utgifts gräns hindrar Azure dig från att behöva spendera på ditt kredit belopp. När du skapar och använder Azure-resurser används dina krediter. När du når din kredit gräns inaktive ras de resurser som du har distribuerat för resten av fakturerings perioden. Du kan inte ändra kredit gränsen, men du kan ta bort den. Mer information om utgifts gränser finns i [utgifts gränsen för Azure](../cost-management-billing/manage/spending-limit.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="optimize-costs"></a>Optimera kostnader

På en grundläggande nivå debiteras App Service-appar av App Service plan som är värdar för dem. Kostnaderna som är kopplade till din App Service-distribution beror på några viktiga faktorer:

- **Pris nivå**  Kallas annars SKU för App Service plan. Högre nivåer ger fler processor kärnor, minne, lagrings utrymme eller funktioner eller kombinationer av dem.
- **Antalet**  dedikerade instanser (Basic och högre) kan skalas ut och varje utskalad instans påförs kostnader.
- **Stämpel avgift**  På den isolerade nivån periodiseras en fast avgift i App Services miljön, oavsett hur många appar eller arbets instanser som finns.

En App Service plan kan vara värd för mer än en app. Beroende på din distribution kan du spara kostnader som är värdar för fler appar på en App Service planer (dvs. vara värd för dina appar i färre App Services planer).

Mer information finns i [App Service plan översikt](overview-hosting-plans.md)

### <a name="non-production-workloads"></a>Arbets belastningar som inte är produktion

Om du vill testa App Service eller lösningen när du periodiserar låg eller minimal kostnad, kan du börja med att använda de två pris nivåer som finns på ingångs nivå, **kostnads fria** och **delade**, som finns på delade instanser. Om du vill testa din app på dedikerade instanser med bättre prestanda kan du uppgradera till **Basic** -nivå, som stöder både Windows-och Linux-appar. 

> [!NOTE]
> **Priser för utveckling/testning i Azure**  För att testa arbets belastningar för för produktion som kräver högre nivåer (alla nivåer förutom **isolerade**) kan Visual Studio-prenumeranter också dra nytta av [priserna för utveckling/testning i Azure](https://azure.microsoft.com/pricing/dev-test/), vilket ger betydande rabatter.
>
> Både den **kostnads fria** och den **delade** nivån, samt pris rabatten för Azure dev/test, har inget ekonomiskt service avtal.

### <a name="production-workloads"></a>Produktionsarbetsbelastningar

Produktions arbets belastningar levereras med rekommendationen för den dedikerade **standard** pris nivån eller högre. Även om priset går upp för högre nivåer ger det också mer minne och lagring och mer presterande maskin vara, vilket ger dig högre app-densitet per beräknings instans. Detta översätter till lägre instans antal för samma antal appar och därmed lägre kostnad. Faktum är att **Premium v3** (den högsta icke-**isolerade** nivån) är det mest kostnads effektiva sättet att hantera din app i stor skala. Om du vill lägga till besparingar kan du få djup rabatt på [Premium v3-reservationer](#azure-reservations).

> [!NOTE]
> **Premium v3** stöder både Windows-behållare och Linux-behållare. 

När du har valt den pris nivå du vill ha bör du minimera inaktiva instanser. I en skalbar distribution kan du slösa pengar på underutnyttjade beräknings instanser. Du bör [Konfigurera automatisk skalning](../azure-monitor/platform/autoscale-get-started.md), tillgängligt på **standard** -nivån och över. Genom att skapa skalbara scheman, samt regler för måttbaserade skalbarhet, betalar du bara för de instanser som du verkligen behöver vid en specifik tidpunkt.

### <a name="azure-reservations"></a>Azure-reservationer

Om du planerar att använda ett känt minsta antal beräknings instanser för ett år eller mer, bör du dra nytta av **Premium v3** -nivån och minska instansen kostnads drastiskt genom att reservera instanserna i ett år eller tre års steg. Besparingarna för månatlig kostnad kan vara så mycket som 55% per instans. Det finns två typer av reservationer:

- **Windows (eller Platform oberoende)**  Kan tillämpas på Windows-eller Linux-instanser i din prenumeration.
- **Linux-Specific**  Gäller endast Linux-instanser i din prenumeration.

Priset för reserverad instans gäller för tillämpliga instanser i din prenumeration, upp till antalet instanser som du reserverar. Reserverade instanser är en fakturerings fråga och är inte knutna till vissa beräknings instanser. Om du kör färre instanser än du reserverar när som helst under reservations perioden, betalar du fortfarande för de reserverade instanserna. Om du kör fler instanser än du reserverar när som helst under reservations perioden betalar du den normala upplupna kostnaden för ytterligare instanser.

Den **isolerade** nivån (App Service miljö) stöder också 1-års-och 3-års reservationer till minskad prissättning. Mer information finns i [hur reservations rabatter gäller för Azure App Service](../cost-management-billing/reservations/reservation-discount-app-service.md).

## <a name="monitor-costs"></a>Övervaka kostnader

När du använder Azure-resurser med App Service debiteras du kostnader. Kostnaderna för Azure Resource Usage varierar med tidsintervall (sekunder, minuter, timmar och dagar). Så snart som App Service användning börjar kostnader och du kan se kostnaderna för [kostnads analys](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

När du använder kostnads analys kan du Visa App Service kostnader i grafer och tabeller i olika tidsintervall. Några exempel är per dag, innevarande och föregående månad och år. Du ser också kostnader för budget och prognostiserade kostnader. Att växla till längre vyer över tid kan hjälpa dig att identifiera utgifts trender. Och du ser var överförbrukning kan ha inträffat. Om du har skapat budgetar kan du också enkelt se var de överskreds.
    
Så här visar du App Service kostnader i kostnads analys:

1. Logga in på Azure-portalen.
2. Öppna omfånget i Azure Portal och välj **kostnads analys** i menyn. Gå till exempel till **prenumerationer**, Välj en prenumeration i listan och välj sedan  **kostnads analys** i menyn. Välj **omfång** för att växla till ett annat omfång i kostnads analys.
3. Som standard visas kostnaden för tjänster i det första Ring diagrammet. Markera det områden i diagrammet som är märkt App Service.

De faktiska månads kostnaderna visas när du först öppnar kostnads analys. Här är ett exempel som visar alla månatliga användnings kostnader.

![Exempel som visar ackumulerade kostnader för en prenumeration](media/overview-manage-costs/all-costs.png)

Om du vill begränsa kostnaderna för en enskild tjänst, till exempel App Service, väljer du **Lägg till filter** och väljer sedan **tjänst namn**. Välj sedan **App Service**.

Här är ett exempel som visar kostnader för just App Service.

![Exempel som visar ackumulerade kostnader för ServiceName](media/overview-manage-costs/service-specific-costs.png)

I föregående exempel visas den aktuella kostnaden för tjänsten. Kostnader per Azure-regioner (platser) och App Service kostnader per resurs grupp visas också. Härifrån kan du utforska kostnaderna på egen hand.

## <a name="create-budgets"></a>Skapa budgetar

<!-- Note to Azure service writer: Modify the following as needed for your service. -->

Du kan skapa [budgetar](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) för att hantera kostnader och skapa [aviseringar](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) som automatiskt meddelar mottagarna om kostnadsavvikelser och risker för överförbrukning. Aviseringar baseras på utgifter jämfört med budget- och kostnadströsklar. Budgetar och aviseringar skapas för Azure-prenumerationer och resurs grupper, så de är användbara som en del av en övergripande kostnads övervaknings strategi. 

Budgetar kan skapas med filter för vissa resurser eller tjänster i Azure om du vill ha mer detaljerad information i din övervakning. Filter hjälper till att se till att du inte av misstag skapar nya resurser som kostar extra pengar. Mer information om de filter alternativ som är tillgängliga när du skapar en budget finns i [alternativ för grupper och filter](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Exportera kostnadsdata

Du kan också [Exportera dina kostnads data](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) till ett lagrings konto. Detta är användbart när du behöver eller andra för att göra mer data analyser för kostnader. Ett ekonomi team kan till exempel analysera data med hjälp av Excel eller Power BI. Du kan exportera dina kostnader enligt ett dags-, vecko-eller månads schema och ange ett anpassat datum intervall. Att exportera kostnads data är det rekommenderade sättet att hämta kostnads data uppsättningar.

## <a name="next-steps"></a>Nästa steg

- Läs mer om hur prissättningen fungerar med Azure Storage. Se [App Service prissättning](https://azure.microsoft.com/pricing/details/app-service/).
- Lär dig [hur du optimerar din moln investering med Azure Cost Management](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Lär dig mer om att hantera kostnader med [kostnads analys](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Lär dig mer om att [förhindra oväntade kostnader](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Ta den [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) guidade utbildningen.

<!-- Insert links to other articles that might help users save and manage costs for you service here.

Create a table of contents entry for the article in the How-to guides section where appropriate. -->