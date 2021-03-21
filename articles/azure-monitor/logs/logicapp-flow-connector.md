---
title: Använd Azure Monitor loggar med Azure Logic Apps och energi automatisering
description: Lär dig hur du kan använda Azure Logic Apps och energi automatisering för att snabbt automatisera upprepade processer med hjälp av Azure Monitor-anslutningen.
ms.service: azure-monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/13/2020
ms.openlocfilehash: 4a25d7a23d486c8ce22fa433cc1ead390726facc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102048870"
---
# <a name="azure-monitor-logs-connector-for-logic-apps-and-power-automate"></a>Azure Monitor loggar Connector för Logic Apps och energi automatisering
Med [Azure Logic Apps](../../logic-apps/index.yml) och [energi spar läge](https://flow.microsoft.com) kan du skapa automatiserade arbets flöden med hundratals åtgärder för olika tjänster. Med anslutnings programmet för Azure Monitor loggar kan du bygga arbets flöden som hämtar data från en Log Analytics arbets yta eller ett Application Insights program i Azure Monitor. I den här artikeln beskrivs de åtgärder som ingår i kopplingen och en genom gång av hur du skapar ett arbets flöde med dessa data.

Du kan till exempel skapa en Logic app för att använda Azure Monitor loggdata i ett e-postmeddelande från Office 365, skapa en bugg i Azure DevOps eller skicka ett slack-meddelande.  Du kan utlösa ett arbets flöde med ett enkelt schema eller från en åtgärd i en ansluten tjänst, till exempel när ett e-postmeddelande eller en tweet tas emot. 

## <a name="connector-limits"></a>Kopplings gränser
Azure Monitor loggar Connector har följande gränser:
* Maximal data storlek: 16 MB
* Maximal svars storlek för fråga 100 MB
* Max antal poster: 500 000
* Maximal tids gräns för fråga 110 sekund.
* Diagram visualiseringar kan vara tillgängliga på sidan loggar och saknas i kopplingen eftersom sidan koppling och loggar inte använder samma diagram bibliotek för tillfället.

Beroende på storleken på dina data och frågan som du använder, kan det hända att anslutnings programmet når sina gränser och Miss lyckas. Du kan undvika sådana fall när du justerar upprepningen av utlösaren så att den körs oftare och frågar mindre data. Du kan använda frågor som sammanställer dina data för att returnera mindre poster och kolumner.

## <a name="actions"></a>Åtgärder
I följande tabell beskrivs de åtgärder som ingår med Azure Monitor loggar Connector. Båda låter dig köra en logg fråga mot en Log Analytics arbets yta eller Application Insights program. Skillnaden är på samma sätt som data returneras.

> [!NOTE]
> Azure Monitor loggar Connector ersätter [Azure Log Analytics-anslutningen](/connectors/azureloganalytics/) och Azure Application Insights- [anslutningsprogrammet](/connectors/applicationinsights/). Den här anslutningen ger samma funktioner som de andra och är den bästa metoden för att köra en fråga mot en Log Analytics arbets yta eller ett Application Insights program.


| Åtgärd | Beskrivning |
|:---|:---|
| [Kör fråga-och list resultat](/connectors/azuremonitorlogs/#run-query-and-list-results) | Returnerar varje rad som ett eget objekt. Använd den här åtgärden när du vill arbeta med varje rad separat i resten av arbets flödet. Åtgärden följs vanligt vis av en [för varje aktivitet](../../logic-apps/logic-apps-control-flow-loops.md#foreach-loop). |
| [Kör fråga och visualisera resultat](/connectors/azuremonitorlogs/#run-query-and-visualize-results) | Returnerar alla rader i resultat uppsättningen som ett enda formaterat objekt. Använd den här åtgärden när du vill använda resultat uppsättningen tillsammans i resten av arbets flödet, till exempel skicka resultatet i ett e-postmeddelande.  |

## <a name="walkthroughs"></a>Genomgångar
Följande självstudier illustrerar användningen av Azure Monitor-kopplingar i Azure Logic Apps. Du kan utföra samma exempel med automatisk energi förbrukning, men den enda skillnaden är att du skapar det första arbets flödet och kör det när du är klar. Konfigurationen av arbets flödet och åtgärderna är samma mellan båda. Kom igång genom att se [skapa ett flöde från en mall i energi automatisering](/power-automate/get-started-logic-template) .


### <a name="create-a-logic-app"></a>Skapa en logikapp

Gå till **Logic Apps** i Azure Portal och klicka på **Lägg till**. Välj en **prenumeration**, en **resurs grupp** och en **region** för att lagra den nya Logic-appen och ge den ett unikt namn. Du kan aktivera **Log Analytics** inställningen för att samla in information om körnings data och händelser enligt beskrivningen i [Konfigurera Azure Monitor loggar och samla in diagnostikdata för Azure Logic Apps](../../logic-apps/monitor-logic-apps-log-analytics.md). Den här inställningen krävs inte för att använda anslutnings programmet för Azure Monitor-loggar.

![Skapa en logikapp](media/logicapp-flow-connector/create-logic-app.png)


Klicka på **Granska + skapa** och sedan på **skapa**. När distributionen är klar klickar du på **gå till resurs** för att öppna **Logic Apps designer**.

### <a name="create-a-trigger-for-the-logic-app"></a>Skapa en utlösare för Logic app
Under **börja med en gemensam utlösare** väljer du **upprepning**. Detta skapar en logisk app som körs automatiskt med jämna mellanrum. I rutan **frekvens** för åtgärden väljer du **dag** och i rutan **intervall** anger du **1** för att köra arbets flödet en gång per dag.

![Upprepnings åtgärd](media/logicapp-flow-connector/recurrence-action.png)

## <a name="walkthrough-mail-visualized-results"></a>Genom gång: visualisering av e-post
Följande själv studie kurs visar hur du skapar en logisk app som skickar resultatet av en Azure Monitor logg fråga via e-post. 

### <a name="add-azure-monitor-logs-action"></a>Åtgärden Lägg till Azure Monitor loggar
Klicka på **+ nytt steg** för att lägga till en åtgärd som körs efter upprepnings åtgärden. Under **Välj en åtgärd**, Skriv **Azure Monitor** och välj sedan **Azure Monitor loggar**.

![Åtgärd för Azure Monitor loggar](media/logicapp-flow-connector/select-azure-monitor-connector.png)

Klicka på **Azure Log Analytics – kör fråga och visualisera resultat**.

![Skärm bild av en ny åtgärd som läggs till i ett steg i Logic Apps designer. Azure Monitor loggar markeras under Välj en åtgärd.](media/logicapp-flow-connector/select-query-action-visualize.png)


### <a name="add-azure-monitor-logs-action"></a>Åtgärden Lägg till Azure Monitor loggar

Välj **prenumerationen** och **resurs gruppen** för din Log Analytics-arbetsyta. Välj *Log Analytics arbets yta* för **resurs typen** och välj sedan namnet på arbets ytan under **resurs namn**.

Lägg till följande logg fråga i **frågefönstret** .  

```Kusto
Event
| where EventLevelName == "Error" 
| where TimeGenerated > ago(1day)
| summarize TotalErrors=count() by Computer
| sort by Computer asc   
```

Välj *Ange i fråga* för **tidsintervallet** och **HTML-tabellen** för **diagram typen**.
   
![Skärm bild av inställningarna för åtgärden ny Azure Monitor loggar med namnet kör fråga och visualisera resultat.](media/logicapp-flow-connector/run-query-visualize-action.png)

E-postmeddelandet kommer att skickas av det konto som är associerat med den aktuella anslutningen. Du kan ange ett annat konto genom att klicka på **ändra anslutning**.

### <a name="add-email-action"></a>Lägg till e-poståtgärd

Klicka på **+ nytt steg** och klicka sedan på **+ Lägg till en åtgärd**. Under **Välj en åtgärd** skriver du **Outlook** och väljer sedan **Office 365 Outlook**.

![Välj Outlook-koppling](media/logicapp-flow-connector/select-outlook-connector.png)

Välj **Skicka ett e-postmeddelande (v2)**.

![Office 365 Outlook Selection-fönster](media/logicapp-flow-connector/select-mail-action.png)

Klicka någonstans i rutan **brödtext** för att öppna ett **dynamiskt innehålls** fönster som öppnas med värden från föregående åtgärder i Logic-appen. Välj **Visa mer** och sedan **brödtext** som är resultatet av frågan i Log Analytics åtgärden.

![Välj brödtext](media/logicapp-flow-connector/select-body.png)

Ange e-postadressen för en mottagare i fönstret **till** och ett ämne för e-postmeddelandet i **ämne**. 

![E-poståtgärd](media/logicapp-flow-connector/mail-action.png)


### <a name="save-and-test-your-logic-app"></a>Spara och testa din Logic app
Klicka på **Spara** och **Kör** sedan för att utföra en testkörning av Logic app.

![Spara och kör](media/logicapp-flow-connector/save-run.png)


När Logic-appen har slutförts, kontrol lera e-postmeddelandets mottagare som du har angett.  Du bör ha fått ett e-postmeddelande med en brödtext som liknar följande:

![Exempel på e-post](media/logicapp-flow-connector/sample-mail.png)



## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [logg frågor i Azure Monitor](./log-query-overview.md).
- Läs mer om [Logic Apps](../../logic-apps/index.yml)
- Lär dig mer om att [Automatisera energi](https://flow.microsoft.com).