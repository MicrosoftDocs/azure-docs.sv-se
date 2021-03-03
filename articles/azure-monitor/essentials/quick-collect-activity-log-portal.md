---
title: Skicka Azure aktivitets logg till Log Analytics arbets ytan med Azure Portal
description: Använd Azure Portal för att skapa en Log Analytics arbets yta och en diagnostisk inställning för att skicka aktivitets loggen till Azure Monitor loggar.
ms.subservice: logs
ms.topic: quickstart
author: bwren
ms.author: bwren
ms.date: 06/25/2020
ms.openlocfilehash: 1316512badd69b545e39a47ea5da3ca6a0902482
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101734289"
---
# <a name="send-azure-activity-log-to-log-analytics-workspace-using-azure-portal"></a>Skicka Azure aktivitets logg till Log Analytics arbets ytan med Azure Portal
Aktivitetsloggen är en plattformslogg i Azure som ger inblick i händelser på prenumerationsnivå. Bland annat loggas information som när en resurs ändras eller när en virtuell dator startas. Du kan visa aktivitets loggen i Azure Portal eller hämta poster med PowerShell och CLI. Den här snabb starten visar hur du använder Azure Portal för att skapa en Log Analytics arbets yta och en diagnostisk inställning för att skicka aktivitets loggen till Azure Monitor loggar där du kan analysera den med hjälp av [logg frågor](../logs/log-query-overview.md) och aktivera andra funktioner som [logg aviseringar](../alerts/alerts-log-query.md) och [arbets böcker](../visualize/workbooks-overview.md). 

## <a name="sign-in-to-azure-portal"></a>Logga in på Azure-portalen
Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com). 



## <a name="create-a-log-analytics-workspace"></a>Skapa en Log Analytics-arbetsyta
I Azure Portal söker du efter och väljer **Log Analytics arbets ytor**. 

![Skärm bild av Azure Portal som visar "Log Analytics-arbetsytor" i sökrutan och Log Analytics arbets ytor som är markerade under tjänster.](../logs/media/quick-create-workspace/azure-portal-01.png)
  
Klicka på **Lägg till** och ange värden för **resurs gruppen**, arbets ytans **namn** och **plats**. Arbets ytans namn måste vara unika för alla Azure-prenumerationer.

![Skapa arbetsyta](media/quick-collect-activity-log/create-workspace.png)

Klicka på **Granska + skapa** för att granska inställningarna och **skapa** sedan för att skapa arbets ytan. Detta väljer en standard pris nivå med **betala per användning,** vilket inte innebär några ändringar förrän du börjar samla in en tillräckligt stor mängd data. Det kostar inget att samla in aktivitets loggen.


## <a name="create-diagnostic-setting"></a>Skapa diagnostiskinställning
I Azure Portal söker du efter och väljer **övervaka**. 

![Skärm bild av Azure Portal med "övervaka" i sökrutan och övervakaren markerat under tjänster.](media/quick-collect-activity-log/azure-portal-monitor.png)

Välj **aktivitets logg**. Du bör se de senaste händelserna för den aktuella prenumerationen. Klicka på **diagnostikinställningar** för att Visa diagnostiska inställningar för prenumerationen.

![Aktivitetslogg](media/quick-collect-activity-log/activity-log.png)

Klicka på **Lägg till diagnostisk inställning** för att skapa en ny inställning. 

![Skapa diagnostiskinställning](media/quick-collect-activity-log/create-diagnostic-setting.png)

Ange ett namn, till exempel *Skicka aktivitets logg till arbets ytan*. Välj var och en av kategorierna. Välj **Skicka till Log Analytics** som den enda destinationen och ange sedan arbets ytan som du skapade. Klicka på **Spara** för att skapa den diagnostiska inställningen och stäng sedan sidan.

![Ny diagnostisk inställning](media/quick-collect-activity-log/new-diagnostic-setting.png)

## <a name="generate-log-data"></a>Generera logg data
Endast nya aktivitets logg poster skickas till Log Analytics arbets ytan, så utför vissa åtgärder i prenumerationen som ska loggas, till exempel starta eller stoppa en virtuell dator eller skapa eller ändra en annan resurs. Du kan behöva vänta några minuter för att den diagnostiska inställningen ska skapas och för att data ska kunna skrivas till arbets ytan. Efter den här fördröjningen kommer alla händelser som skrivs till aktivitets loggen att skickas till arbets ytan inom några sekunder.

## <a name="retrieve-data-with-a-log-query"></a>Hämta data med en logg fråga

Välj **loggar** på **Azure Monitor** -menyn. Stäng sidan **exempel frågor** . Om omfattningen inte är inställd på den arbets yta som du har skapat klickar du på **Välj omfång** och letar upp den.

![Log Analytics omfång](media/quick-collect-activity-log/log-analytics-scope.png)

I fönstret fråga skriver du `AzureActivity` och klickar på **Kör**. Det här är en enkel fråga som returnerar alla poster i tabellen *AzureActivity* som innehåller alla poster som skickas från aktivitets loggen.

![Exempelfråga](media/quick-collect-activity-log/query-01.png)

Expandera en av posterna för att visa dess detaljerade egenskaper.

![Expandera egenskaper](media/quick-collect-activity-log/expand-properties.png)

Testa en mer komplex fråga, till exempel `AzureActivity | summarize count() by CategoryValue` vilken som ger ett antal händelser sammanfattade efter kategori.

![Komplex fråga](media/quick-collect-activity-log/query-02.png)


## <a name="next-steps"></a>Nästa steg
I den här snabb starten konfigurerade du aktivitets loggen så att den skickas till en Log Analytics-arbetsyta. Nu kan du konfigurera andra data så att de samlas in i arbets ytan där du kan analysera dem tillsammans med hjälp av [logg frågor](../logs/log-query-overview.md) i Azure Monitor och utnyttja funktioner som [logg aviseringar](../alerts/alerts-log-query.md) och [arbets böcker](../visualize/workbooks-overview.md). Du bör börja med att samla in [resurs loggar](../essentials/resource-logs.md) från dina Azure-resurser som kompletterar data i aktivitets loggen och ger insyn i de åtgärder som utfördes i varje resurs.


> [!div class="nextstepaction"]
> [Samla in och analysera resurs loggar med Azure Monitor](../essentials/tutorial-resource-logs.md)