---
title: Skapa och dela instrumentpaneler med data från Azure Log Analytics | Microsoft Docs
description: Den här självstudien hjälper dig att förstå hur Log Analytics instrument paneler kan visualisera alla sparade logg frågor, vilket ger dig en enkel lins att visa din miljö.
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 05/28/2020
ms.custom: mvc
ms.openlocfilehash: 84c710e44fbbccdefd5bf811477e1fa4c3989114
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102043464"
---
# <a name="create-and-share-dashboards-of-log-analytics-data"></a>Skapa och dela instrumentpaneler för Log Analytics

Log Analytics instrument paneler kan visualisera alla dina sparade logg frågor, vilket ger dig möjlighet att hitta, korrelera och dela den operativa informationen i organisationen.  I den här självstudien beskrivs hur du skapar en logg fråga som ska användas för att stödja en delad instrument panel som används av IT-avdelningen support.  Lär dig att:

> [!div class="checklist"]
> * Skapa en delad instrumentpanel i Azure Portal
> * Visualisera en prestanda logg fråga 
> * Lägga till en logg fråga till en delad instrument panel 
> * Anpassa en panel i en delad instrumentpanel

Du måste ha en befintlig virtuell dator [som är ansluten till Log Analytics-arbetsytan](../vm/quick-collect-azurevm.md) för att kunna utföra exemplet i självstudien.  
 
## <a name="sign-in-to-azure-portal"></a>Logga in på Azure-portalen
Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-shared-dashboard"></a>Skapa delade instrumentpaneler
Välj **instrument panel** för att öppna standard [instrument panelen](../../azure-portal/azure-portal-dashboards.md). Instrument panelen kommer att se annorlunda ut än i exemplet nedan.

![Instrumentpanelen för Azure-portalen](media/tutorial-logs-dashboards/log-analytics-portal-dashboard.png)

Här kan du samla de driftdata som är viktigast för IT över alla dina Azure-resurser, inklusive telemetri från Azure Log Analytics.  Innan vi går vidare med visualiseringen av en logg fråga ska vi först skapa en instrument panel och dela den.  Vi kan sedan fokusera på vårt exempel på prestanda logg frågor, som kommer att återges som ett linje diagram och lägga till den på instrument panelen.  

> [!NOTE]
> Följande diagram typer stöds i Azure-instrumentpaneler med hjälp av logg frågor:
> - areachart
> - columnchart
> - piechart (återges på instrument panelen som ring)
> - scatterchart
> - timechart

Om du vill skapa en instrumentpanel väljer du knappen **Ny instrumentpanel** bredvid namnet på den aktuella instrumentpanelen.

![Skapa ny instrumentpanel i Azure Portal](media/tutorial-logs-dashboards/log-analytics-create-dashboard-01.png)

Detta skapar en ny, tom, privat instrumentpanel och du hamnar i anpassningsläge. Där kan du ge instrumentpanelen ett namn och lägga till eller ordna om paneler. Redigera namnet på instrument panelen och ange *exempel instrument panel* för den här självstudien och välj sedan **anpassningen har gjorts**.<br><br> ![Spara anpassade instrumentpaneler i Azure](media/tutorial-logs-dashboards/log-analytics-create-dashboard-02.png)

Instrumentpaneler är privata som standard när de skapas, vilket innebär att du är den enda som kan se dem. Använd knappen **Dela** som visas tillsammans med andra kommandon i instrumentpanelen för att visa den för andra.

![Dela en ny instrumentpanel i Azure Portal](media/tutorial-logs-dashboards/log-analytics-share-dashboard.png) 

Du uppmanas att välja en prenumeration och resursgrupp som din instrumentpanel ska publiceras till. För att underlätta för dig guidar portalen dig genom ett mönster där du placerar dina instrumentpaneler i en resursgrupp som kallas **instrumentpaneler**.  Verifiera den valda prenumerationen och klicka på **Publicera**.  Åtkomst till den information som visas i instrument panelen styrs av [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md).   

## <a name="visualize-a-log-query"></a>Visualisera en logg fråga
[Log Analytics](../logs/log-analytics-tutorial.md) är en särskild portal som används för att arbeta med logg frågor och deras resultat. Funktionen ger möjlighet att redigera en fråga på flera rader, köra kod selektivt, sammanhangsberoende Intellisense och Smart Analytics. I den här självstudien ska du använda Log Analytics för att skapa en datavy i grafiskt format, spara den för en kommande fråga och fästa den på den delade instrument panelen som skapades tidigare.

Öppna Log Analytics genom att välja **loggar** på Azure Monitor-menyn. Den börjar med en ny tom fråga.

![Startsida](media/tutorial-logs-dashboards/homepage.png)

Ange följande fråga för att returnera processor användnings poster för både Windows-och Linux-datorer, grupperade efter dator-och TimeGenerated och visas i ett visuellt diagram. Klicka på **Kör** för att köra frågan och visa det resulterande diagrammet.

```Kusto
Perf 
| where CounterName == "% Processor Time" and ObjectName == "Processor" and InstanceName == "_Total" 
| summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 1hr), Computer 
| render timechart
```

Spara frågan genom att välja knappen **Spara** längst upp på sidan.

![Spara fråga](media/tutorial-logs-dashboards/save-query.png)

I kontroll panelen **Spara fråga** anger du ett namn som *virtuella Azure-datorer – processor användning* och en kategori, till exempel *instrument paneler* och klickar sedan på **Spara**.  På så sätt kan du skapa ett bibliotek med vanliga frågor som du kan använda och ändra.  Till sist kan du fästa den på den delade instrument panelen som skapades tidigare genom att välja knappen **Fäst på instrument panelen** i det övre högra hörnet på sidan och sedan välja instrument panelens namn.

Nu när du har en fråga som är fäst på instrumentpanelen, ser du att den har en generisk rubrik och en kommentar under sig.

![Exempel på instrumentpanel i Azure](media/tutorial-logs-dashboards/log-analytics-modify-dashboard-01.png)

 Du bör byta namn till ett beskrivande namn, som enkelt kan tolkas av de som tittar på den.  Klicka på knappen Redigera för att anpassa rubriken och under rubriken för panelen och klicka sedan på **Uppdatera**.  En banderoll visas med en fråga om du vill publicera eller ta bort ändringarna.  Klicka på **Spara en kopia**.  

![Slutförd konfiguration av exemplet för instrumentpanel](media/tutorial-logs-dashboards/log-analytics-modify-dashboard-02.png)

## <a name="next-steps"></a>Nästa steg
I den här självstudien har du lärt dig hur du skapar en instrument panel i Azure Portal och lägger till en logg fråga till den.  Gå vidare till nästa självstudie för att lära dig olika svar som du kan implementera baserat på logg frågeresultat.  

> [!div class="nextstepaction"]
> [Svara på händelser med Log Analytics-aviseringar](../alerts/tutorial-response.md)