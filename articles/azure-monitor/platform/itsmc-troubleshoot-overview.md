---
title: Felsöka problem med anslutningsprogram för hantering av IT-tjänster (ITSM)
description: Felsöka problem i Anslutningsprogram för hantering av IT-tjänster (ITSM)
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: 14f1056bf761eb7b591d04db34610468058bc255
ms.sourcegitcommit: 61d2b2211f3cc18f1be203c1bc12068fc678b584
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/18/2021
ms.locfileid: "98562870"
---
# <a name="troubleshooting-problems-in-itsm-connector"></a>Felsöka problem med anslutningsprogram för hantering av IT-tjänster (ITSM)

Den här artikeln beskriver vanliga problem i ITSM-anslutningsprogram och hur du felsöker dem.

Azure Monitor aviseringar proaktivt meddela dig när viktiga villkor finns i dina övervaknings data. De gör att du kan identifiera och åtgärda problem innan användarna av systemet ser dem. Mer information om aviseringar finns i Översikt över aviseringar i Microsoft Azure.
Kunden kan välja hur de ska meddelas på aviseringen om det är av e-post, SMS, webhook eller till och med för att automatisera en lösning. Ett annat alternativ att meddelas är att använda ITSM.
ITSM ger dig möjlighet att skicka aviseringar till externt biljett system, till exempel ServiceNow.

## <a name="visualize-and-analyze-the-incident-and-change-request-data"></a>Visualisera och analysera incidenter och data för ändringsbegäran

Beroende på din konfiguration när du konfigurerar en anslutning kan ITSMC synkronisera upp till 120 dagar av incidenten och data för ändringsbegäran. Schemat för logg poster för dessa data finns i [avsnittet Ytterligare information](./itsmc-synced-data.md) i den här artikeln.

Du kan visualisera incident-och ändrings begär ande data med hjälp av ITSMC-instrument panelen:

![Skärm bild som visar ITSMC-instrumentpanelen.](media/itsmc-overview/itsmc-overview-sample-log-analytics.png)

Instrument panelen innehåller också information om anslutnings status, som du kan använda som utgångs punkt för att analysera problem med anslutningarna.

För att få mer information om instrument panelens undersökning, se [fel undersökning med hjälp av instrument panelen](./itsmc-dashboard.md).

### <a name="service-map"></a>Tjänst karta

Du kan också visualisera de incidenter som synkroniseras mot de berörda datorerna i Tjänstkarta.

Tjänstkarta identifierar automatiskt program komponenterna i Windows-och Linux-system och mappar kommunikationen mellan tjänsterna. Du kan visa dina servrar när du ser dem: som sammankopplade system som levererar kritiska tjänster. Tjänstkarta visar anslutningar mellan servrar, processer och portar i alla TCP-anslutna arkitekturer. Förutom installationen av en agent krävs ingen konfiguration. Mer information finns i [använda tjänstkarta](../insights/service-map.md).

Om du använder Tjänstkarta kan du Visa Service Desk-objekten som skapats i ITSM-lösningar, som du ser här:

![Skärm bild som visar Log Analytics skärmen.](media/itsmc-overview/itsmc-overview-integrated-solutions.png)

## <a name="troubleshoot-itsm-connections"></a>Felsöka ITSM-anslutningar

- Gör så här om en anslutning inte kan ansluta till ITSM-systemet och du får ett **fel meddelande när du sparar anslutnings** meddelandet:
   - För ServiceNow-, Cherwell-och upphandlings anslutningar:  
     - Kontrol lera att du har angett användar namn, lösen ord, klient-ID och klient hemlighet korrekt för varje anslutning.  
     - Se till att du har tillräcklig behörighet i motsvarande ITSM-produkt för att upprätta anslutningen.  
   - För Service Manager anslutningar:  
     - Se till att webbappen har distribuerats och att hybrid anslutningen har skapats. Om du vill kontrol lera att anslutningen har upprättats med den lokala Service Manager datorn går du till webbappens URL enligt beskrivningen i dokumentationen för att skapa en [hybrid anslutning](./itsmc-connections-scsm.md#configure-the-hybrid-connection).  

- Om Log Analytics-aviseringar för brand men arbets objekt inte skapas i ITSM-produkten, om konfigurations objekt inte skapas/länkas till arbets objekt, eller för annan information, se följande resurser:
   -  ITSMC: lösningen visar en sammanfattning av anslutningar, arbets objekt, datorer med mera. Välj den panel som har etiketten **kopplings status** . Då kommer du att **Logga sökningen** med den relevanta frågan. Titta på logg poster med en `LogType_S` av `ERROR` för mer information.
   - Sidan **loggs ökning** : Visa fel och relaterad information direkt med hjälp av frågan `*ServiceDeskLog_CL*` .

### <a name="troubleshoot-service-manager-web-app-deployment"></a>Felsöka Service Manager Web App-distribution

-   Om du har problem med att distribuera webbappar kontrollerar du att du har behörighet att skapa/distribuera resurser i prenumerationen.
-   Om du får en **objekt referens som inte är inställt på en instans av ett objekt** fel när du kör [skriptet](itsmc-service-manager-script.md), måste du se till att du har angett giltiga värden i avsnittet **användar konfiguration** .
-   Om du inte kan skapa Service Bus Relay-namnområdet kontrollerar du att den nödvändiga resurs leverantören är registrerad i prenumerationen. Om den inte är registrerad skapar du Service Bus Relay-namnområdet manuellt från Azure Portal. Du kan också skapa den när du [skapar hybrid anslutningen](./itsmc-connections-scsm.md#configure-the-hybrid-connection) i Azure Portal.