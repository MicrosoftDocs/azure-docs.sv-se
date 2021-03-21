---
title: Azure Monitor resurs grupps Insights | Microsoft Docs
description: Förstå hälso tillståndet och prestandan för dina distribuerade program och tjänster på resurs grupps nivå med Azure Monitor
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 09/19/2018
ms.reviewer: mbullwin
ms.openlocfilehash: ab2c56b5e5cfc3dcb7411db27e7faabb2589be6c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101704335"
---
# <a name="monitor-resource-groups-with-azure-monitor-preview"></a>Övervaka resurs grupper med Azure Monitor (för hands version)

Moderna program är ofta komplexa och mycket distribuerade med många diskreta delar som arbetar tillsammans för att leverera en tjänst. Den här komplexiteten är att Azure Monitor ger övervaknings insikter för resurs grupper. Detta gör det enkelt att prioritering och diagnostisera eventuella problem som kan uppstå för enskilda resurser, samtidigt som den erbjuder kontext och prestanda för resurs gruppen &mdash; och ditt program &mdash; som helhet.

## <a name="access-insights-for-resource-groups"></a>Få åtkomst till insikter för resurs grupper

1. Välj **resurs grupper**  i navigerings fältet på vänster sida.
2. Välj en av dina resurs grupper som du vill utforska. (Om du har ett stort antal resurs grupper kan filtrering av prenumeration ibland vara till hjälp.)
3. Om du vill komma åt insikter för en resurs grupp klickar du på **insikter** på den vänstra menyn i valfri resurs grupp.

![Skärm bild av översikts sidan för resurs gruppens insikter](./media/resource-group-insights/0001-overview.png)

## <a name="resources-with-active-alerts-and-health-issues"></a>Resurser med aktiva aviseringar och hälso problem

På sidan Översikt visas hur många aviseringar som har utlösts och fortfarande är aktiva, tillsammans med den aktuella Azure Resource Health för varje resurs. Tillsammans kan den här informationen hjälpa dig att snabbt hitta alla resurser som har problem. Aviseringar hjälper dig att identifiera problem i din kod och hur du har konfigurerat din infrastruktur. Azure Resource Health problem med själva Azure-plattformen, som inte är specifika för dina enskilda program.

![Skärm bild av Azure Resource Health fönstret](./media/resource-group-insights/0002-overview.png)

### <a name="azure-resource-health"></a>Azure Resource Health

Om du vill visa Azure Resource Health markerar du rutan **visa Azure Resource Health** ovanför tabellen. Den här kolumnen är dold som standard för att under lätta sid inläsningen.

![Skärm bild där resurs hälso diagram har lagts till](./media/resource-group-insights/0003-overview.png)

Som standard grupperas resurserna efter app-skikt och resurs typ. **App Layer** är en enkel kategorisering av resurs typer som bara finns i kontexten för översikts sidan för resurs grupper. Det finns resurs typer som är relaterade till program kod, beräknings infrastruktur, nätverk, lagring + databaser. Hanterings verktyg hämtar egna app-lager och varje annan resurs kategoriseras som tillhöra det **andra** App-lagret. Den här grupperingen kan hjälpa dig att se vilka under system i ditt program som är felfria och felaktiga.

## <a name="diagnose-issues-in-your-resource-group"></a>Diagnostisera problem i resurs gruppen

Sidan resurs grupp insikter innehåller flera andra verktyg som är begränsade till att hjälpa dig att diagnostisera problem

   |         |          |
   | ---------------- |:-----|
   | [**Aviseringar**](../alerts/alerts-overview.md)      |  Visa, skapa och hantera dina aviseringar. |
   | [**Mått**](../data-platform.md) | Visualisera och utforska dina Metric-baserade data.    |
   | [**Aktivitets loggar**](../essentials/platform-logs-overview.md) | Händelser på prenumerations nivå som har inträffat i Azure.  |
   | [**Program karta**](../app/app-map.md) | Navigera i den distribuerade appens topologi för att identifiera Flask halsar för prestanda eller felaktiga hotspots. |

## <a name="failures-and-performance"></a>Haverier och prestanda

Vad händer om du har märkt att programmet körs långsamt eller om användarna har rapporterat fel? Det tar tid att söka igenom alla resurser för att isolera problem.

Flikarna **prestanda** och **fel** fören klar den här processen genom att samla in prestanda-och fel diagnostiska vyer för många vanliga resurs typer.

De flesta resurs typer öppnar ett galleri med Azure Monitor mallar för arbets böcker. Varje arbets bok som du skapar kan anpassas, sparas, delas med ditt team och återanvändas i framtiden för att diagnosticera liknande problem.

### <a name="investigate-failures"></a>Undersök felen

Om du vill testa fliken felen väljer du **felen** under **Undersök** i den vänstra menyn.

Den vänstra meny raden ändras när ditt val har gjorts, vilket ger dig nya alternativ.

![Skärm bild av felöversikts fönstret](./media/resource-group-insights/00004-failures.png)

När App Service väljs visas ett galleri med mallar för Azure Monitor-arbetsböcker.

![Skärm bild av Galleri för program arbets boken](./media/resource-group-insights/0005-failure-insights-workbook.png)

Om du väljer mallen för att Visa felblickar öppnas arbets boken.

![Skärm bild av felrapport](./media/resource-group-insights/0006-failure-visual.png)

Du kan välja vilken som helst av raderna. Valet visas sedan i en grafisk detaljvy.

![Skärm bild av information om problem](./media/resource-group-insights/0007-failure-details.png)

Arbets böcker sammanfattar det svåra arbetet med att skapa anpassade rapporter och visualiseringar i ett enkelt förbruknings Bart format. Vissa användare kanske bara vill justera de förinställda parametrarna, men arbets böcker är helt anpassningsbara.

För att få en uppfattning om hur den här arbets boken fungerar internt väljer du **Redigera** i det översta fältet.

![Skärm bild av ytterligare redigerings alternativ](./media/resource-group-insights/0008-failure-edit.png)

Ett antal **redigerings** rutor visas nära de olika elementen i arbets boken. Välj **redigerings** rutan under tabellen med åtgärder.

![Skärm bild av redigerings rutor](./media/resource-group-insights/0009-failure-edit-graph.png)

Detta visar den underliggande logg frågan som kör tabell visualiseringen.

 ![Skärm bild av fönstret logg fråga](./media/resource-group-insights/0010-failure-edit-query.png)

Du kan ändra frågan direkt. Eller så kan du använda den som referens och låna från den när du skapar en egen anpassad parametriserad arbets bok.

### <a name="investigate-performance"></a>Undersök prestanda

Prestanda erbjuder ett eget galleri med arbets böcker. För att App Service arbets boken för fördefinierade program prestanda erbjuder du följande vy:

 ![Skärm bild av vyn prestanda](./media/resource-group-insights/0011-performance.png)

I det här fallet om du väljer Redigera ser du att den här uppsättningen visualiseringar drivs av Azure Monitor mått.

 ![Skärm bild av prestanda visning med Azure-mått](./media/resource-group-insights/0012-performance-metrics.png)

## <a name="troubleshooting"></a>Felsökning

### <a name="enabling-access-to-alerts"></a>Aktivera åtkomst till aviseringar

Om du vill visa aviseringar i Azure Monitor för resurs grupper måste någon med rollen ägare eller deltagare för den här prenumerationen öppna Azure Monitor för resurs grupper för en resurs grupp i prenumerationen. Detta gör att alla med Läs behörighet kan se aviseringar i Azure Monitor för resurs grupper för alla resurs grupper i prenumerationen. Om du har en ägare eller deltagar roll uppdaterar du den här sidan om några minuter.

Azure Monitor för resurs grupper förlitar sig på Azure Monitor aviserings hanterings system för att hämta aviserings status. Aviserings hantering har inte kon figurer ATS för varje resurs grupp och prenumeration som standard, och den kan bara aktive ras av någon med rollen ägare eller deltagare. Den kan aktive ras antingen av:
* Öppnar Azure Monitor för resurs grupper för alla resurs grupper i prenumerationen.
* Eller genom att gå till prenumerationen, klicka på **Resource providers** och sedan klicka på **Registrera för aviseringar. hantering**.

## <a name="next-steps"></a>Nästa steg

- [Azure Monitor arbets böcker](../visualize/workbooks-overview.md)
- [Azure Resource Health](../../service-health/resource-health-overview.md)
- [Azure Monitor-aviseringar](../alerts/alerts-overview.md)
