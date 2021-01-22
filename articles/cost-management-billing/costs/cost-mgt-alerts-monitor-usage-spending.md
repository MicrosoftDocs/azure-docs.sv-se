---
title: Övervaka användningen och utgifterna med kostnadsaviseringar
description: I den här artikeln beskrivs hur kostnadsaviseringar hjälper dig att övervaka användning och utgifter i Azure Cost Management.
author: bandersmsft
ms.author: banders
ms.date: 09/03/2020
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: d1546ef92d923b9d72c4aca0ddf61d2b93646656
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/20/2021
ms.locfileid: "98602350"
---
# <a name="use-cost-alerts-to-monitor-usage-and-spending"></a>Använda kostnadsaviseringar för att övervaka användning och utgifter

Den här artikeln hjälper dig att förstå och använda Cost Management-aviseringar för att övervaka din användning och dina utgifter i Azure. Kostnadsaviseringar genereras automatiskt baserat på de tider då Azure-resurser förbrukas. Aviseringar visar alla aktiva kostnadshanterings- och faktureringsaviseringar på en och samma plats. När din förbrukning når ett angivet tröskelvärde genereras aviseringar av Cost Management. Det finns tre typer av kostnadsaviseringar: budgetaviseringar, kreditaviseringar och utgiftskvotaviseringar.

## <a name="budget-alerts"></a>Budgetaviseringar

Budgetaviseringar meddelar dig när utgifter, baserat på användning eller kostnad, når eller överskrider det belopp som definieras i [budgetens aviseringsvillkor](tutorial-acm-create-budgets.md). Cost Management-budgetar skapas med hjälp av Azure-portalen eller [Azure Consumption](/rest/api/consumption)-API:et.

I Azure-portalen definieras budgetar efter kostnad. Med hjälp av Azure Consumption-API:et definieras budgetarna efter kostnad eller förbrukningsanvändning. Budgetaviseringar stöder både kostnadsbaserade och användningsbaserade budgetar. Budgetaviseringar genereras automatiskt när villkor för budgetavisering uppfylls. Du kan visa alla kostnadsaviseringar i Azure-portalen. När en avisering genereras visas den i kostnadsaviseringar. En e-postavisering skickas dessutom till personerna i budgetens lista över mottagare som ska meddelas.

Du kan använda API för budgetar för att skicka e-postaviseringar på ett annat språk. Mer information finns i [Nationella inställningar som stöds för budgetavisering via e-post](manage-automation.md#supported-locales-for-budget-alert-emails).

## <a name="credit-alerts"></a>Kreditaviseringar

Kreditaviseringar meddelar dig när din Azure-förskottsbetalning (kallades tidigare ekonomiskt åtagande) har förbrukats. Azure-förskottsbetalning är till för organisationer med Enterprise-avtal. Kreditaviseringar genereras automatiskt vid 90 % och vid 100 % av ditt kreditsaldo för Azure-förskottsbetalning. När en avisering genereras återspeglas den i kostnadsaviseringar och i e-postmeddelandet som skickas till kontoinnehavarna.

## <a name="department-spending-quota-alerts"></a>Aviseringar för avdelningens utgiftskvot

Aviseringar för avdelningens utgiftskvot meddelar dig när avdelningens utgifter når ett fast tröskelvärde för kvoten. Utgiftskvoter konfigureras i EA-portalen. När ett tröskelvärde nås genereras ett e-postmeddelande till avdelningsägarna och visas i kostnadsaviseringarna. Till exempel 50 % eller 75 % av kvoten.

## <a name="supported-alert-features-by-offer-categories"></a>Aviseringsfunktioner som stöds av erbjudandekategorier

Stöd för aviseringstyper beror på vilken typ av Azure-konto du har (Microsoft-erbjudandet). I följande tabell visas de aviseringsfunktioner som stöds av de olika Microsoft-erbjudandena. Du kan visa den fullständiga listan över Microsoft-erbjudanden på [Förstå Cost Management-data](understand-cost-mgt-data.md).

| Aviseringstyp | Enterprise-avtal | Microsoft-kundavtal | Web Direct/Betala per användning |
|---|---|---|---|
| Budget | ✔ | ✔ | ✔ |
| Kredit | ✔ |✘ | ✘ |
| Avdelningens utgiftskvot | ✔ | ✘ | ✘ |



## <a name="view-cost-alerts"></a>Visa kostnadsaviseringar

Om du vill visa kostnadsaviseringar öppnar du önskat omfång i Azure-portalen och väljer **Budgetar** på menyn. Använd reglaget **Omfång** om du vill växla till ett annat omfång. Välj **Kostnadsaviseringar** på menyn. Mer information om omfång finns i [Förstå och arbeta med omfång](understand-work-scopes.md).

![Exempelbild med aviseringar som visas i Cost Management](./media/cost-mgt-alerts-monitor-usage-spending/budget-alerts-fullscreen.png)

Det totala antalet aktiva och avstängda aviseringar visas på sidan med kostnadsaviseringar.

Alla aviseringar visar aviseringstypen. En budgetavisering visar orsaken till varför den genererades och namnet på den budget som den gäller för. Varje avisering visar det datum då den genererades, sin status samt det omfång (prenumeration eller hanteringsgrupp) som aviseringen gäller för.

Möjlig status inkluderar **aktiv** och **avvisad**. Statusen aktiv anger att aviseringen fortfarande är relevant. Statusen avvisad visar att någon har markerat aviseringen och angett att den inte längre är relevant.

Välj en avisering i listan om du vill visa information om den. Aviseringsinformationen visar mer information om aviseringen. Budgetaviseringar innehåller en länk till budgeten. Om det finns en rekommendation för en budgetavisering visas även en länk till rekommendationen. I aviseringarna för budget, kredit och avdelningens utgiftskvot finns en länk för att analysera i kostnadsanalys. Där kan du utforska kostnaderna för aviseringens omfång. I följande exempel visas utgifterna för en avdelning med aviseringsinformation.

![Exempelbild som visar utgifter för en avdelning med aviseringsinformation](./media/cost-mgt-alerts-monitor-usage-spending/dept-spending-selected-with-credits.png)

När du visar information om en stängd avisering kan du återaktivera den om manuell åtgärd behövs. I följande bild visas ett exempel.

![Exempelbild som visar alternativ för att stänga och återaktivera](./media/cost-mgt-alerts-monitor-usage-spending/Dismiss-reactivate-options.png)

## <a name="see-also"></a>Se även

- Om du inte redan har skapat en budget eller angett aviseringsvillkor för en budget bör du slutföra självstudien [Skapa och hantera budgetar](tutorial-acm-create-budgets.md).