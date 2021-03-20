---
title: Kom igång med Azure Advisor
description: Kom igång med Azure Advisor.
ms.topic: article
ms.date: 02/01/2019
ms.openlocfilehash: e91049077502a6c0eedf0a4f979c073690c214da
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85117870"
---
# <a name="get-started-with-azure-advisor"></a>Kom igång med Azure Advisor

Lär dig hur du får åtkomst till Advisor via Azure Portal, får rekommendationer och implementerar rekommendationer.

> [!NOTE]
> Azure Advisor körs automatiskt i bakgrunden för att hitta nyligen skapade resurser. Det kan ta upp till 24 timmar att tillhandahålla rekommendationer för dessa resurser.

## <a name="get-recommendations"></a>Hämta rekommendationer

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. I den vänstra rutan klickar du på **Advisor**.  Om du inte ser Advisor i den vänstra rutan klickar du på **alla tjänster**.  I meny fönstret tjänst, under **övervakning och hantering**, klickar du på **Advisor**. Advisor-instrumentpanelen visas.

   ![Åtkomst Azure Advisor med hjälp av Azure Portal](./media/advisor-get-started/advisor-portal-menu.png) 

1. Advisor-instrumentpanelen visar en sammanfattning av dina rekommendationer för alla valda prenumerationer.  Du kan välja de prenumerationer som du vill att rekommendationerna ska visas för i listrutan prenumerationsfilter.

1. Klicka på någon av flikarna om du vill ha rekommendationer för en viss kategori: **tillförlitlighet**, **säkerhet**, **prestanda** eller **kostnad**. 

   ![Azure Advisor instrument panel](./media/advisor-overview/advisor-dashboard.png)

## <a name="get-recommendation-details-and-implement-a-solution"></a>Hämta rekommendations information och implementera en lösning

Du kan välja en rekommendation i Advisor om du vill visa mer information – till exempel rekommendations åtgärder och påverkade resurser – och implementera lösningen i rekommendationen.  

1. Logga in på [Azure Portal](https://portal.azure.com)och öppna [Advisor](https://aka.ms/azureadvisordashboard).

1. Välj en rekommendations kategori för att visa listan över rekommendationer i den kategorin, eller Välj fliken **alla** för att visa alla dina rekommendationer.

1. Klicka på en rekommendation som du vill granska i detalj.

1. Granska informationen om rekommendationen och de resurser som rekommendationen gäller för.

1. Klicka på den **rekommenderade åtgärden** för att implementera rekommendationen.

## <a name="filter-recommendations"></a>Filter rekommendationer

Du kan filtrera rekommendationer för att öka detalj nivån till det som är viktigast för dig.  Du kan filtrera efter prenumeration, resurs typ eller rekommendations status.  

1. Logga in på [Azure Portal](https://portal.azure.com)och öppna [Advisor](https://aka.ms/azureadvisordashboard).

1. Använd List rutorna på instrument panelens instrument panel för att filtrera efter prenumeration, resurs typ eller rekommendations status.

    ![Advisor-sökning – filter kriterier](./media/advisor-get-started/advisor-filters.png)

## <a name="postpone-or-dismiss-recommendations"></a>Skjuta upp eller Stäng rekommendationer

1. Logga in på [Azure Portal](https://portal.azure.com)och öppna [Advisor](https://aka.ms/azureadvisordashboard).

1. Navigera till den rekommendation som du vill skjuta upp eller stänga.

1. Klicka på rekommendationen.

1. Klicka på **Skjut upp**. 

1. Ange en senareläggnings tids period eller Välj **aldrig** om du vill stänga av rekommendationen.

## <a name="exclude-subscriptions-or-resource-groups"></a>Exkludera prenumerationer eller resurs grupper

Du kan ha resurs grupper eller prenumerationer som du inte vill ta emot rådgivares rekommendationer för, t. ex. "test"-resurser.  Du kan konfigurera Advisor till att bara skapa rekommendationer för vissa prenumerationer och resurs grupper.

> [!NOTE]
> Om du vill ta med eller undanta en prenumeration eller resurs grupp från Advisor måste du vara prenumerations ägare.  Om du inte har de behörigheter som krävs för en prenumeration eller resurs grupp är alternativet för att inkludera eller exkludera det inaktiverat i användar gränssnittet.

1. Logga in på [Azure Portal](https://portal.azure.com)och öppna [Advisor](https://aka.ms/azureadvisordashboard).

1. Klicka på **Konfigurera** i åtgärds fältet.

1. Avmarkera eventuella prenumerationer eller resurs grupper som du inte vill få rådgivare-rekommendationer för.

    ![Advisor konfigurera resurser exempel](./media/advisor-get-started/advisor-configure-resources.png)

1. Klicka på knappen **Använd**.

## <a name="configure-low-usage-vm-recommendation"></a>Konfigurera rekommendation för virtuell dator med låg användning

Den här proceduren konfigurerar den genomsnittliga CPU-belastningen för rekommendationen för den virtuella datorn med låg användning.

Advisor övervakar din användning av virtuella datorer i 7 dagar och identifierar virtuella datorer med låg användning. Virtuella datorer betraktas som en låg belastning om processor användningen är 5% eller mindre och användningen av nätverket är mindre än 2% eller om den aktuella arbets belastningen kan hanteras av en mindre virtuell dator storlek.

Om du vill vara mer aggressiv vid identifiering av virtuella datorer med låg användnings nivå kan du justera den genomsnittliga CPU-belastningen per prenumeration.  Regeln för processor användning kan anges till 5%, 10%, 15% eller 20%.

> [!NOTE]
> Om du vill justera den genomsnittliga CPU-belastningen för att identifiera virtuella datorer med låg användning måste du vara prenumerations *ägare*.  Om du inte har de behörigheter som krävs för en prenumeration eller resurs grupp inaktive ras alternativet att inkludera eller exkludera den i användar gränssnittet. 

1. Logga in på [Azure Portal](https://portal.azure.com)och öppna [Advisor](https://aka.ms/azureadvisordashboard).

1. Klicka på **Konfigurera** i åtgärds fältet.

1. Klicka på fliken **regler** .

1. Välj de prenumerationer som du vill justera den genomsnittliga CPU-belastningen för och klicka sedan på **Redigera**.

1. Välj önskat Genomsnittligt processor användnings värde och klicka på **Använd**.

1. Klicka på **Uppdatera rekommendationer** om du vill uppdatera dina befintliga rekommendationer för att använda den nya regeln för genomsnittlig processor användning. 

   ![Exempel på guiden Konfigurera rekommendations regler](./media/advisor-get-started/advisor-configure-rules.png)

## <a name="download-recommendations"></a>Hämta rekommendationer

Advisor gör att du kan ladda ned en sammanfattning av dina rekommendationer.  Du kan ladda ned rekommendationer som en PDF-fil eller en CSV-fil.  Genom att hämta dina rekommendationer kan du enkelt dela med dina kollegor eller utföra en egen analys ovanpå rekommendations data.

1. Logga in på [Azure Portal](https://portal.azure.com)och öppna [Advisor](https://aka.ms/azureadvisordashboard).

1. Klicka på **Hämta som CSV** eller **Ladda ned som PDF** i åtgärds fältet.

Nedladdnings alternativet respekterar alla filter som du har tillämpat på instrument panelen för Advisor.  Om du väljer nedladdnings alternativet när du visar en viss rekommendations kategori eller rekommendation, innehåller den nedladdade sammanfattningen endast information för den kategorin eller rekommendationen. 

## <a name="next-steps"></a>Nästa steg

Mer information om Advisor finns i:

- [Introduktion till Azure Advisor](advisor-overview.md)
- [Rekommendationer om Advisor-tillförlitlighet](advisor-high-availability-recommendations.md)
- [Rekommendationer för Advisor-säkerhet](advisor-security-recommendations.md)
- [Rekommendationer för Advisor-prestanda](advisor-performance-recommendations.md)
- [Rekommendationer om Advisor-kostnader](advisor-cost-recommendations.md)
- [Rekommendationer om operativa rekommendationer](advisor-operational-excellence-recommendations.md)
