---
title: Analysera oväntade Azure-avgifter
description: Lär dig hur du analyserar oväntade avgifter för din Azure-prenumeration.
author: bandersmsft
ms.reviewer: adwise
ms.service: cost-management-billing
ms.subservice: cost-management
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: banders
ms.custom: contperf-fy21q1
ms.openlocfilehash: 6d5198f47b6089730ff345eb12ab522fc5ce58a1
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100575382"
---
# <a name="analyze-unexpected-charges"></a>Analysera oväntade kostnader

Den infrastruktur du skapat för organisationens molnresurser är förmodligen komplex. Många Azure-resurstyper kan ha olika typer av avgifter. Azure-resurser kan ägas av olika team i organisationen och de kan ha olika typer av faktureringsmodeller som gäller för olika resurser. För att få en bättre förståelse för avgifterna kan du göra en analys med hjälp av en eller flera av strategierna i följande avsnitt.

## <a name="review-invoice-for-resource-responsible-for-charge"></a>Granska faktura för den resurs som står för avgiften

Hur du köper Azure-tjänster hjälper dig att avgöra vilken metodik och vilka verktyg som är tillgängliga när du identifierar vilken resurs som är kopplad till en avgift. För att avgöra vilken metod som gäller för dig bör du först [identifiera din typ av Azure-erbjudande](../costs/understand-cost-mgt-data.md#determine-your-offer-type). Identifiera sedan din kundkategori i listan med [Azure-erbjudanden som stöds](../costs/understand-cost-mgt-data.md#supported-microsoft-azure-offers).

Följande artiklar innehåller detaljerade anvisningar som förklarar hur du granskar din faktura baserat på kundtyp. I varje artikel finns anvisningar om hur du hämtar en CSV-fil som innehåller användnings- och kostnadsinformation för en viss faktureringsperiod.

- [Process för granskning av faktura för betala per användning](review-individual-bill.md#charges)
- [Process för granskning av faktura för Enterprise-avtal](review-enterprise-agreement-bill.md)
- [Process för att granska Microsoft-kundavtal](review-customer-agreement-bill.md#analyze-your-azure-usage-charges)
- [Process för att granska Microsoft-partneravtal](review-partner-agreement-bill.md#analyze-your-azure-usage-charges)

På din Azure-faktura samlas avgifter för månaden per _mätare_. Mätare används för att spåra en resurs användning över tid och används för att beräkna din faktura. När du skapar en enskild Azure-resurs, till exempel en virtuell dator, skapas en eller flera mätare för den resursen.

Filtrera CSV-filen över användning på _Mätarnamn_ som det visas på den faktura du vill analysera för att se alla poster som gäller den mätaren. _Instans-ID_ för radposten motsvarar den faktiska Azure-resursen som gett upphov till kostnaden.

När du har identifierat den aktuella resursen kan du använda Kostnadsanalys i Azure Cost Management för att vidare analysera kostnaderna för resursen. Läs mer om att använda kostnadsanalys i [Börja analysera kostnader](../costs/quick-acm-cost-analysis.md).

## <a name="review-invoiced-charges-in-cost-analysis"></a>Granska fakturerade avgifter i Kostnadsanalys

Gå till kostnadsanalysen för omfattningen som är associerad med fakturan som du analyserar för att se information om den i Microsoft Azure-portalen. Välj vyn **Fakturainformation**. Fakturainformation visar avgifterna som visas på fakturan.

[![Exempel som visar fakturainformation](./media/analyze-unexpected-charges/invoice-details.png)](./media/analyze-unexpected-charges/invoice-details.png#lightbox)

När du granskar fakturainformationen kan du identifiera eventuella tjänster som har oväntade kostnader och avgöra vilka resurser som är direkt kopplade till resursen i kostnadsanalysen. Om du till exempel vill analysera avgifterna för tjänsten Virtual Machines går du till vyn **Ackumulerade kostnader**. Sedan ställer du in kornigheten på **Daglig** och filtrerar avgifterna efter **Tjänstnamn: Virtual Machines** samt grupperar avgifterna efter **Resurs**.

[![Exempel som visar ackumulerade kostnader för virtuella datorer](./media/analyze-unexpected-charges/virtual-machines.png)](./media/analyze-unexpected-charges/virtual-machines.png#lightbox)

## <a name="identify-spikes-in-cost-over-time"></a>Identifiera toppar i kostnaderna över tid

Ibland kanske du inte vet vilka kostnader som har lett till förändringar av de fakturerade avgifterna. För att förstå vad som har förändrats kan du använda Kostnadsanalys för att [visa en uppdelning per dag eller månad av kostnaderna över tid](../costs/cost-analysis-common-uses.md#view-costs-per-day-or-by-month). När du har skapat vyn ska du gruppera dina avgifter efter antingen **tjänst** eller **resurs** för att identifiera ändringarna. Du kan också ändra vyn till ett **linje** diagram för att visualisera data bättre.

![Exempel som visar kostnader över tid i kostnadsanalys](./media/analyze-unexpected-charges/costs-over-time.png)

## <a name="determine-resource-pricing-and-billing-model"></a>Fastställa resurspriser och faktureringsmodellen

En enskild resurs kan ge upphov till kostnader på flera Azure-produkter och -tjänster. Se sidan med [Azure-priser per produkt](https://azure.microsoft.com/pricing/#product-pricing) för att läsa mer om priserna för respektive Azure-tjänst. Till exempel kan en enskild virtuell dator (VM) som skapats i Azure ha följande mätare som skapas för att spåra dess användning. Var och en kan ha olika priser.

- Beräkningstimmar
- IP-adresstimmar
- Inkommande dataöverföring
- Utgående dataöverföring
- Hanterade standarddiskar
- Åtgärder med hanterade standarddiskar
- I/O-standarddisk
- Läsning av I/O-blockblob
- Skrivning till I/O-blockblob
- Standard – I/O-blockblobsborttagning

När den virtuella datorn skapas börjar varje mätare att skicka användningsposter. Användningen och priset för mätaren spåras i Azure-mätarsystemet. Du kan se de mätare som användes för att beräkna din faktura i CSV-filen för användning.

## <a name="find-people-responsible-for-the-resource-and-engage"></a>Hitta de personer som ansvarar för resursen och samarbeta

Ofta vet det team som ansvarar för en viss resurs vilka ändringar som har gjorts för en resurs. Att engagera dem är till hjälp eftersom du får veta varför kostnaderna kanske uppkommer. Det ägnade teamet kanske till exempel nyligen har skapat resursen, uppdaterat dess SKU (och därmed ändrat resurstaxan) eller ökat belastningen på resursen på grund av kodändringar. Fortsätt att läsa nedanstående avsnitt för mer information om hur du kan avgöra vem som äger en resurs.

### <a name="analyze-the-audit-logs-for-the-resource"></a>Analysera spårningsloggarna för resursen

Om du har behörighet att visa en resurs bör du även ha åtkomst till dess spårningsloggar. Granska loggarna för att ta reda på vilken användare som är ansvarig för de senaste ändringarna av en resurs. Mer information finns i [Visa och hämta Azure aktivitetslogghändelser](../../azure-monitor/essentials/activity-log.md#view-the-activity-log).

### <a name="analyze-user-permissions-to-the-resources-parent-scope"></a>Analysera användarbehörigheter till resursens överordnade omfång

Användare som har skrivåtkomst till en prenumeration eller resursgrupp har ofta information om de resurser som skapats. De bör kunna förklara syftet med en resurs eller hänvisa dig till någon som vet. Information om hur du identifierar personer som har behörighet för ett prenumerationsomfång finns i [Kontrollera en användares åtkomst till Azure-resurser](../../role-based-access-control/check-access.md). Du kan använda en liknande process för resursgrupper.

## <a name="get-help-to-identify-charges"></a>Få hjälp med att identifiera kostnader

Om du har använt ovanstående strategier och fortfarande inte förstår varför du har debiterats en kostnad, eller om du behöver hjälp med andra fakturafrågor, kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg

- Lär dig att [Optimera din molninvestering med Azure Cost Management](../costs/cost-mgt-best-practices.md).