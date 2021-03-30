---
title: Visa Azure Advisor rekommendationer som är viktiga för dig
description: Visa och filtrera Azure Advisor rekommendationer för att minska bruset.
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: 43bdaba7d774bba8857a6eb3ef296d7ab8e1c264
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "90986864"
---
# <a name="view-azure-advisor-recommendations-that-matter-to-you"></a>Visa Azure Advisor rekommendationer som är viktiga för dig

Azure Advisor ger rekommendationer som hjälper dig att optimera dina Azure-distributioner. I Advisor har du till gång till några funktioner som hjälper dig att begränsa dina rekommendationer till endast de som är viktiga för dig.

## <a name="configure-subscriptions-and-resource-groups"></a>Konfigurera prenumerationer och resurs grupper

Advisor ger dig möjlighet att välja prenumerationer och resurs grupper som är viktiga för dig och din organisation. Du ser bara rekommendationer för de prenumerationer och resurs grupper som du väljer. Som standard är alla markerade. Konfigurations inställningarna gäller för prenumerationen eller resurs gruppen, så samma inställningar gäller för alla som har åtkomst till den prenumerationen eller resurs gruppen. Konfigurations inställningar kan ändras i Azure Portal eller program mässigt.

Så här gör du ändringar i Azure Portal:

1. Öppna [Azure Advisor](https://aka.ms/azureadvisordashboard) i Azure Portal.

1. Välj **konfiguration** på menyn.

   ![Guide för konfigurations menyn](./media/view-recommendations/configuration.png)

1. Markera kryss rutan i kolumnen **ta** med för prenumerationer eller resurs grupper för att få rådgivare-rekommendationer. Om rutan är inaktive rad kanske du inte har behörighet att göra en konfigurations ändring för den prenumerationen eller resurs gruppen. Läs mer om [behörigheter i Azure Advisor](permissions.md).

1. Klicka på **Använd** längst ned när du har gjort en ändring.

## <a name="filtering-your-view-in-the-azure-portal"></a>Filtrera vyn i Azure Portal

Konfigurations inställningarna förblir aktiva tills de har ändrats. Om du vill begränsa visningen av rekommendationer för en enskild visning kan du använda List rutorna som visas längst upp i Advisor-panelen. Från panelerna översikt, hög tillgänglighet, säkerhet, prestanda, kostnad och alla rekommendationer kan du välja de prenumerationer, resurs typer och den rekommendations status som du vill se.

   :::image type="content" source="./media/view-recommendations/filtering.png" alt-text="Skärm bild av Azure Advisor som visar filtrerings alternativ.":::

## <a name="dismissing-and-postponing-recommendations"></a>Stänger och senarelägger rekommendationer

Med Azure Advisor kan du stänga eller skjuta upp rekommendationer på en enskild resurs. Om du stänger av en rekommendation visas den inte igen om du inte aktiverar den manuellt. Genom att skjuta upp en rekommendation kan du dock ange en varaktighet efter vilken rekommendationen automatiskt aktive ras igen. Senareläggning kan göras i Azure Portal eller program mässigt.

### <a name="postpone-a-single-recommendation-in-the-azure-portal"></a>Senarelägg en enskild rekommendation i Azure Portal 

1. Öppna [Azure Advisor](https://aka.ms/azureadvisordashboard) i Azure Portal.
1. Välj en rekommendations kategori för att visa dina rekommendationer
1. Välj en rekommendation i listan över rekommendationer
1. Välj Skjut upp eller Stäng av den rekommendation som du vill skjuta upp eller stänga av

     :::image type="content" source="./media/view-recommendations/postpone-dismiss.png" alt-text="Skärm bild av fönstret Använd Managed Disks som visar kolumnen Markera och skjuta upp och Stäng åtgärder för en enda rekommendation markerad.":::

### <a name="postpone-or-dismiss-a-multiple-recommendations-in-the-azure-portal"></a>Skjuta upp eller Stäng av flera rekommendationer i Azure Portal

1. Öppna [Azure Advisor](https://aka.ms/azureadvisordashboard) i Azure Portal.
1. Välj en rekommendations kategori för att visa dina rekommendationer.
1. Välj en rekommendation i listan över rekommendationer.
1. Markera kryss rutan till vänster om raden för alla resurser som du vill skjuta upp eller stänga av rekommendationen.
1. Välj **Skjut** upp eller **Stäng** längst upp till vänster i tabellen.

     :::image type="content" source="./media/view-recommendations/postpone-dismiss-multiple.png" alt-text="Skärm bild av fönstret Använd Managed Disks som visar kolumnen Markera och skjuta upp och Stäng av åtgärder längst upp till vänster i den markerade tabellen.":::

> [!NOTE]
> Du måste ha behörighet som deltagare eller ägare för att kunna stänga eller skjuta upp en rekommendation. Läs mer om behörigheter i Azure Advisor.

> [!NOTE]
> Om markerings rutorna är inaktiverade kan rekommendationer fortfarande läsas in. Vänta tills alla rekommendationer har lästs in innan du försöker skjuta upp eller stänga.

### <a name="reactivate-a-postponed-or-dismissed-recommendation"></a>Återaktivera en senarelagd eller avstängd rekommendation

Du kan aktivera en rekommendation som har uppskjutits eller stängts. Den här åtgärden kan utföras i Azure Portal eller program mässigt. I Azure-portalen:

1. Öppna [Azure Advisor](https://aka.ms/azureadvisordashboard) i Azure Portal.

1. Ändra filtret på översikts panelen till **uppskjutet**. Advisor visar sedan uppskjutna eller avstängda rekommendationer.

    :::image type="content" source="./media/view-recommendations/activate-postponed.png" alt-text="Skärm bild av fönstret Azure Advisor som visar den uppskjutna nedrullningsbara menyn.":::

1. Välj en kategori för att visa **uppskjutna** och **avstängda** rekommendationer.

1. Välj en rekommendation i listan över rekommendationer. Rekommendationerna öppnas med den **Uppskjutna &** fliken redan har valts för att visa de resurser som den här rekommendationen har skjutits upp eller stängts av.

1. Klicka på **Aktivera** i slutet av raden. När du har klickat på den här resursen så tas rekommendationen bort från den här tabellen. Rekommendationen visas nu på fliken **aktiv** .
 
     :::image type="content" source="./media/view-recommendations/activate-postponed-2.png" alt-text="Skärm bild av fönstret aktivera mjuk borttagning som visar den uppskjutna &-fliken med åtgärden aktivera markerad.":::

## <a name="next-steps"></a>Nästa steg

Den här artikeln förklarar hur du kan visa rekommendationer som är viktiga för dig i Azure Advisor. Mer information om Advisor finns i: 

- [Vad är Azure Advisor?](advisor-overview.md)
- [Komma igång med Advisor](advisor-get-started.md)
- [Behörigheter i Azure Advisor](permissions.md)



