---
title: Åtgärda ServiceNow-synkroniseringsproblem manuellt
description: Återställ anslutningen till ServiceNow så att aviseringar i Microsoft Azure kan anropa ServiceNow igen
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: 9a6e46c0b52c22df0682034deaebd58bbfeb34a7
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98210103"
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

### <a name="error-investigation-using-the-dashboard"></a>Fel undersökning med instrument panelen

Om du vill visa felen på instrument panelen ska du följa anvisningarna nedan:

1. I **alla resurser** letar du efter **Servicedesk (*namnet på din arbets yta*)**:

   ![Skärm bild som visar de senaste resurserna i Azure Portal.](media/itsmc-definition/create-new-connection-from-resource.png)

2. Under **arbets ytans data källor** i det vänstra fönstret väljer du **ITSM-anslutningar**:

   ![Skärm bild som visar meny alternativet ITSM-anslutningar.](media/itsmc-overview/add-new-itsm-connection.png)

3. Under **Sammanfattning** i den vänstra rutan **anslutningsprogram för hantering av IT-tjänster (ITSM)** väljer du **Visa sammanfattning**:

    ![Skärm bild som visar Visa sammanfattning.](media/itsmc-resync-servicenow/dashboard-view-summary.png)

4. Klicka på diagrammet under **Sammanfattning** i den vänstra rutan **anslutningsprogram för hantering av IT-tjänster (ITSM)**:

    ![Skärm bild som visar graf-klickning.](media/itsmc-resync-servicenow/dashboard-graph-click.png)

5. Med den här instrument panelen kan du granska status och fel i din anslutning.
    ![Skärm bild som visar kopplings status.](media/itsmc-resync-servicenow/connector-dashboard.png)

### <a name="dashboard-elements"></a>Instrument panels element

Instrument panelen innehåller information om de aviseringar som har skickats till ITSM-verktyget med hjälp av den här anslutningen.
Instrument panelen är uppdelad i 4 delar:

1. Arbets objekt skapat: grafen och tabellen nedan innehåller antalet arbets objekt per typ. Om du klickar på grafen eller i tabellen kan du se mer information om arbets objekt.
    ![Skärm bild som visar arbets objekt som skapats.](media/itsmc-resync-servicenow/itsm-dashboard-workitems.png)
2. Påverkade datorer: tabellerna innehåller information om konfigurations objekt som skapade konfigurations objekt.
    Genom att klicka på rader i tabellerna kan du få mer information om konfigurations objekten.
    Tabellen innehåller ett begränsat antal rader om du vill se alla listor som du kan klicka på "Se alla".
    ![Skärm bild som visar påverkade datorer.](media/itsmc-resync-servicenow/itsm-dashboard-impacted-comp.png)
3. Anslutnings status: grafen och tabellen nedan innehåller meddelanden om anslutnings status. Genom att klicka på diagrammet på rader i tabellen kan du få mer information om meddelandena om anslutnings status.
    Tabellen innehåller ett begränsat antal rader om du vill se alla listor som du kan klicka på "Se alla".
    ![Skärm bild som visar kopplings status.](media/itsmc-resync-servicenow/itsm-dashboard-connector-status.png)
4. Aviserings regler: tabellerna innehåller information om antalet aviserings regler som har identifierats.
    Genom att klicka på rader i tabeller kan du få mer information om de regler som har identifierats.
    Tabellen innehåller ett begränsat antal rader om du vill se alla listor som du kan klicka på "Se alla".
    ![Skärm bild som visar varnings regler.](media/itsmc-resync-servicenow/itsm-dashboard-alert-rules.png)

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

- Om data från ServiceNow inte kommer att synkroniseras till Log Analytics, se till att ServiceNow-instansen inte är i vilo läge. ServiceNow dev-instanser går ibland till vilo läge när de är inaktiva under en längre tid. Rapportera problemet om det inte är vad som händer.
- Om Log Analytics-aviseringar för brand men arbets objekt inte skapas i ITSM-produkten, om konfigurations objekt inte skapas/länkas till arbets objekt, eller för annan information, se följande resurser:
   -  ITSMC: lösningen visar en sammanfattning av anslutningar, arbets objekt, datorer med mera. Välj den panel som har etiketten **kopplings status** . Då kommer du att **Logga sökningen** med den relevanta frågan. Titta på logg poster med en `LogType_S` av `ERROR` för mer information.
   - Sidan **loggs ökning** : Visa fel och relaterad information direkt med hjälp av frågan `*ServiceDeskLog_CL*` .

### <a name="troubleshoot-service-manager-web-app-deployment"></a>Felsöka Service Manager Web App-distribution

-   Om du har problem med att distribuera webbappar kontrollerar du att du har behörighet att skapa/distribuera resurser i prenumerationen.
-   Om du får en **objekt referens som inte är inställt på en instans av ett objekt** fel när du kör [skriptet](itsmc-service-manager-script.md), måste du se till att du har angett giltiga värden i avsnittet **användar konfiguration** .
-   Om du inte kan skapa Service Bus Relay-namnområdet kontrollerar du att den nödvändiga resurs leverantören är registrerad i prenumerationen. Om den inte är registrerad skapar du Service Bus Relay-namnområdet manuellt från Azure Portal. Du kan också skapa den när du [skapar hybrid anslutningen](./itsmc-connections-scsm.md#configure-the-hybrid-connection) i Azure Portal.

### <a name="how-to-manually-fix-sync-problems"></a>Så här åtgärdar du synkroniseringsproblem manuellt

Azure Monitor kan ansluta till ITSM-leverantörer (IT Service Management) från tredje part. ServiceNow är en av dessa leverantörer.

Av säkerhets skäl kan du behöva uppdatera autentiseringstoken som används för din anslutning med ServiceNow.
Använd följande synkroniseringsprocess för att återaktivera anslutningen och uppdatera token:


1. Sök efter lösningen i den översta sökbanderollen och välj sedan de relevanta lösningarna

    ![Skärm bild som visar den översta sökbanderollen och var du väljer de relevanta lösningarna.](media/itsmc-resync-servicenow/solution-search-8bit.png)

1. I lösnings skärmen väljer du "Markera alla" i prenumerations filtret och filtrerar sedan efter "ServiceDesk"

    ![Skärm bild som visar var du väljer Markera alla och var du vill filtrera efter ServiceDesk.](media/itsmc-resync-servicenow/solutions-list-8bit.png)

1. Välj lösning för din ITSM-anslutning.
1. Välj ITSM-anslutning i den vänstra banderollen.

    ![Skärm bild som visar var du väljer ITSM-anslutningar.](media/itsmc-resync-servicenow/itsm-connector-8bit.png)

1. Välj varje koppling i listan. 
    1. Klicka på kopplingens namn för att konfigurera det
    1. Ta bort alla kopplingar som inte längre används

    1. Uppdatera fälten enligt [dessa definitioner](./itsmc-connections.md) baserat på din partner typ

    1. Klicka på synkronisera

       ![Skärm bild som visar knappen Synkronisera.](media/itsmc-resync-servicenow/resync-8bit2.png)

    1. Klicka på Spara

        ![Ny anslutning](media/itsmc-resync-servicenow/save-8bit.png)

f.    Granska meddelandena för att se om processen har startats.
