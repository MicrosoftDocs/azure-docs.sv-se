---
title: Översikt över Azure Cost Management + Fakturering
description: Du använder funktioner i Azure Cost Management + Fakturering för att utföra administrativa faktureringsuppgifter och hantera faktureringsåtkomst till kostnader. Du använder även funktioner för att övervaka och kontrollera Azure-utgifter samt optimera användningen av Azure-resurser.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/26/2020
ms.topic: overview
ms.service: cost-management-billing
ms.subservice: common
ms.custom: contperf-fy21q2
ms.openlocfilehash: 34034a99641d75e44783cb5b87af8948b4db1628
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97029948"
---
# <a name="what-is-azure-cost-management--billing"></a>Vad är Azure Cost Management + Fakturering?

Genom att använda Microsoft Cloud kan du avsevärt förbättra tekniska prestanda för företagets arbetsbelastningar. Du kan också minska dina kostnader och omkostnader för att hantera organisationstillgångar. Men affärsmöjligheten medför också en risk för att det kan uppstå förlorad kapacitet och ineffektivitet i molndistributionerna. Azure Cost Management + Fakturering är en uppsättning verktyg som tillhandahålls av Microsoft och som hjälper dig att analysera, hantera och optimera kostnaderna för dina arbetsbelastningar. Med hjälp av programsviten kan du se till att din organisation drar nytta av fördelarna med molnet.

Du kan se dina Azure-arbetsbelastningar som lamporna hemma. Lämnar du lamporna på när du går till jobbet? Skulle du kunna använda andra, mer strömsnåla glödlampor för att minska elräkningen? Har du fler lampor i ett rum än du behöver? Med Azure Cost Management + Fakturering kan du använda ett liknande tankesätt för företagets arbetsbelastningar.

Med Azure-produkter och -tjänster betalar du bara för det du använder. När du skapar och använder Azure-resurser debiteras du för resurserna. Eftersom det är enkelt att distribuera nya resurser kan kostnaderna för arbetsbelastningarna skena utan en ordentlig analys och övervakning. Med Azure Cost Management + Fakturering kan du:

- Utföra administrativa faktureringsuppgifter som att betala din faktura
- Hantera faktureringsåtkomst till kostnader
- Ladda ned kostnader och användningsdata som användes som underlag för att generera din månadsfaktura
- Tillämpa dataanalys proaktivt för dina kostnader
- Ange utgiftströsklar
- Ta reda på om det går att göra arbetsbelastningsändringar som kan optimera dina utgifter

Om du vill veta mer om metoder för kostnadshantering för organisationer kan du läsa artikeln om [metodtips för Azure Cost Management](./costs/cost-mgt-best-practices.md).

## <a name="understand-azure-billing"></a>Förstå Azure-faktureringen

Funktionerna i Azure-fakturering används för att granska dina fakturerade kostnader och hantera åtkomst till faktureringsinformation. I större organisationer genomförs faktureringsuppgifter vanligtvis av inköps- och ekonomiteamen.

När du registrerar dig för att använda Azure skapas ett faktureringskonto. Du använder faktureringskontot för att hantera fakturor och betalningar samt spåra kostnader. Du kan ha åtkomst till flera faktureringskonton. Du kan till exempel ha registrerat dig för Azure för dina personliga projekt. Därför kan du ha en enskild Azure-prenumeration med ett faktureringskonto. Du kan även ha åtkomst via organisationens Enterprise-avtal eller Microsoft-kundavtal. Du skulle ha ett separat faktureringskonto för varje scenario.

### <a name="billing-accounts"></a>Faktureringskonton

Azure-portalen stöder för närvarande följande typer av faktureringskonton:

- **Microsoft Online Services-programmet**: Ett enskilt faktureringskonto för ett Microsoft Online Services-program skapas när du registrerar dig för Azure via Azure-webbplatsen. Det gäller till exempel när du registrerar dig för ett kostnadsfritt Azure-konto eller ett konto med Betala per användning-priser eller som Visual studio-prenumerant.

- **Enterprise-avtal**: Ett faktureringskonto för ett Enterprise-avtal skapas när din organisation tecknar ett Enterprise-avtal (EA) för att använda Azure.

- **Microsoft-kundavtal**: Ett faktureringskonto för ett Microsoft-kundavtal skapas när din organisation arbetar med en Microsoft-representant för att teckna ett Microsoft-kundavtal. Vissa kunder i utvalda regioner som registrerar sig via Azure-webbplatsen för ett konto med Betala per användning-priser eller uppgraderar sitt kostnadsfria Azure-konto kan även ha ett faktureringskonto för ett Microsoft-kundavtal.

### <a name="scopes-for-billing-accounts"></a>Omfång för faktureringskonton
Ett omfång är en nod i ett faktureringskonto som du använder för att visa och hantera fakturering. Där hanterar du faktureringsdata, betalningar, fakturor och utför vanliga kontohanteringsuppgifter.

#### <a name="microsoft-online-services-program"></a>Microsoft Online Services-programmet

|Omfång  |Definition  |
|---------|---------|
|Faktureringskonto     | Representerar en enskild ägare (kontoadministratör) för en eller flera Azure-prenumerationer. Kontoadministratörer är auktoriserade att utföra olika faktureringsuppgifter som att skapa prenumerationer, visa fakturor eller ändra faktureringen för prenumerationer.  |
|Prenumeration     |  Representerar en gruppering av Azure-resurser. En faktura genereras i prenumerationsomfånget. Den har sina egna betalningsmetoder som används för att betala fakturan.|

#### <a name="enterprise-agreement"></a>Enterprise-avtal

|Omfång  |Definition  |
|---------|---------|
|Faktureringskonto    | Representerar en Enterprise-avtalsregistrering. Fakturan genereras i omfång för faktureringskonto. Den struktureras med hjälp av avdelningar och registreringskonton.  |
|Avdelning     |  Valfri gruppering av registreringskonton.      |
|Registreringskonto     |  Representerar en enskild kontoägare. Azure-prenumerationer skapas under omfånget för registreringskonto.  |

#### <a name="microsoft-customer-agreement"></a>Microsoft-kundavtal

|Omfång  |Aktiviteter  |
|---------|---------|
|Faktureringskonto     |   Representerar ett kundavtal för flera Microsoft-produkter och -tjänster. Faktureringskontot struktureras med hjälp av faktureringsprofiler och fakturaavsnitt.   |
|Faktureringsprofil     |  Representerar en faktura och dess betalningsmetod. Fakturan genereras i detta omfång. Faktureringsprofilen kan ha flera fakturaavsnitt.      |
|Fakturaavsnitt     |   Representerar en grupp med kostnader i en faktura. Prenumerationer och andra inköp är associerade med omfånget för fakturaavsnitt.    |

## <a name="understand-azure-cost-management"></a>Förstå Azure Cost Management

Fakturering är relaterat till kostnadshantering men inbegriper skillnader. I faktureringsprocessen ingår att fakturera kunder för varor eller tjänster och att hantera den affärsrelationen.

Kostnadshantering visar organisationens kostnader och användningsmönster med hjälp av avancerade analyser. Rapporter i Cost Management visar de användningskostnader som förbrukas av Azure-tjänster och Marketplace-erbjudanden från tredje part. Kostnaderna baseras på förhandlade priser och beaktar reservations- och Azure Hybrid-förmånsrabatter. Sammantaget visar rapporterna dina interna och externa kostnader för användning och Azure Marketplace-debiteringar. Andra debiteringar, till exempel reservationsköp, support och skatter, visas ännu inte i rapporter. Rapporterna hjälper dig att förstå dina utgifter och resursanvändningen och kan hjälpa dig hitta utgiftsavvikelser. Förutsägelseanalys är också tillgängligt. Kostnadshantering använder Azure-hanteringsgrupper, budgetar och rekommendationer för att tydligt visa hur dina utgifter organiseras och hur du kan minska kostnaderna.

Du kan använda Azure-portalen eller olika API:er för exportautomatisering för att integrera kostnadsdata med externa system och processer. Automatiserad export av faktureringsdata och schemalagda rapporter är också tillgängligt.

Titta på videon med en översikt av Azure Cost Management för att få en snabb översikt över hur Azure Cost Management kan hjälpa dig att spara pengar i Azure. Om du vill titta på andra videor går du till [YouTube-kanalen för Cost Management](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/el4yN5cHsJ0]

### <a name="plan-and-control-expenses"></a>Planering och kontrollera utgifter

Med Cost Management kan du planera för och styra dina kostnader på följande sätt: Kostnadsanalys, budgetar, rekommendationer och export av kostnadshanteringsdata.

Du använder kostnadsanalys för att utforska och analysera dina organisationskostnader. Du kan visa aggregerade kostnader efter organisation för att se var kostnader ackumuleras och för att identifiera utgiftstrender. Du kan även se ackumulerade kostnader över tid för att beräkna månatliga, kvartalsvisa eller årliga kostnadstrender mot en budget.

Budgetar hjälper dig att planera och uppfylla finansiellt ansvar i din organisation. De bidrar till att förhindra att kostnadströsklar eller -gränser överskrids. Budgetar kan även hjälpa dig att informera andra om deras utgifter för att proaktivt hantera kostnader. De gör också att du kan se hur utgifterna utvecklas över tid.

Rekommendationer visar hur du kan optimera och förbättra effektiviteten genom att identifiera inaktiva och underutnyttjade resurser. Eller så kan de visa billigare resursalternativ. När du agerar utifrån rekommendationer ändrar sättet som du använder resurser för att spara pengar. För att tittar du först på rekommendationer för kostnadsoptimering för att hitta potentiell ineffektivitet i användningen. Därefter agerar du utefter en rekommendation för att ändra din Azure-resursanvändning till ett mer kostnadseffektivt alternativ. Sedan kontrollerar du åtgärden för att se till att den ändring du gör lyckas.

Om du använder externa system för att komma åt eller granska kostnadshanteringsdata kan du enkelt exportera data från Azure. Och du kan ange en daglig schemalagd export i CSV-format och lagra datafilerna i Azure Storage. Du kan sedan komma åt data från ditt externa system.

### <a name="cloudyn-deprecation"></a>Cloudyn dras tillbaka

Cloudyn är en Azure-tjänst som är kopplad till Cost Management och som kommer att tas ur bruk i slutet av 2020. Befintliga Cloudyn-funktioner integreras direkt på Azure-portalen då det är möjligt. Inga nya kunder kan registreras, men supporten fortsätter för produkten tills den har dragits tillbaka helt.
 
### <a name="additional-azure-tools"></a>Ytterligare Azure-verktyg

I Azure finns andra verktyg som inte ingår i funktionerna i Azure Cost Management + Fakturering. De spelar dock en viktig roll i kostnadshanteringsprocessen. Se följande länkar om du vill veta mer om dessa verktyg.

- [Azure Pricing Calculator](https://azure.microsoft.com/pricing/calculator/) – använd det här verktyget för att beräkna dina startkostnader för moln.
- [Azure Migrate](../migrate/migrate-services-overview.md) – utvärdera din nuvarande datacenterarbetsbelastning för att få insikter om vad som behövs från en Azure-ersättningslösning.
- [Azure Advisor](../advisor/advisor-overview.md) – identifiera oanvända virtuella datorer och få rekommendationer om köp av reserverade Azure-instanser.
- [Azure Hybrid-förmån](https://azure.microsoft.com/pricing/hybrid-benefit/) – använd dina aktuella lokala Windows Server- eller SQL Server-licenser för virtuella datorer i Azure för att spara.

## <a name="next-steps"></a>Nästa steg

Nu när du vet mer om Cost Management + Fakturering är nästa steg att börja använda tjänsten.

- Börja använda Azure Cost Management för att [analysera kostnader](./costs/quick-acm-cost-analysis.md).
- Du kan även läsa mer om [metodtips för Azure Cost Management](./costs/cost-mgt-best-practices.md).