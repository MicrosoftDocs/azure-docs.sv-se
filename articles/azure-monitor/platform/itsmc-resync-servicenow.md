---
title: Åtgärda ServiceNow-synkroniseringsproblem manuellt
description: Återställ anslutningen till ServiceNow så att aviseringar i Microsoft Azure kan anropa ServiceNow igen
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: 3e836873219bde3836f2863e328b0b6f5b89addc
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97507293"
---
# <a name="troubleshooting-problems-in-itsm-connector"></a>Felsöka problem med anslutningsprogram för hantering av IT-tjänster (ITSM)

Den här artikeln beskriver vanliga problem i ITSM-anslutningsprogram och hur du felsöker dem.

Azure Monitor aviseringar proaktivt meddela dig när viktiga villkor finns i dina övervaknings data. De gör att du kan identifiera och åtgärda problem innan användarna av systemet ser dem. Mer information om aviseringar finns i Översikt över aviseringar i Microsoft Azure.
Kunden kan välja hur de ska meddelas på aviseringen om det är av e-post, SMS, webhook eller till och med för att automatisera en lösning. Ett annat alternativ att meddelas är att använda ITSM.
ITSM ger dig möjlighet att skicka aviseringar till externt biljett system, till exempel ServiceNow.

## <a name="visualize-and-analyze-the-incident-and-change-request-data"></a>Visualisera och analysera incidenter och data för ändringsbegäran

Beroende på din konfiguration när du konfigurerar en anslutning kan ITSMC synkronisera upp till 120 dagar av incidenten och data för ändringsbegäran. Schemat för logg poster för dessa data finns i [avsnittet Ytterligare information](https://docs.microsoft.com/azure/azure-monitor/platform/itsmc-overview#additional-information) i den här artikeln.

Du kan visualisera incident-och ändrings begär ande data med hjälp av ITSMC-instrument panelen:

![Skärm bild som visar ITSMC-instrumentpanelen.](media/itsmc-overview/itsmc-overview-sample-log-analytics.png)

Instrument panelen innehåller också information om anslutnings status, som du kan använda som utgångs punkt för att analysera problem med anslutningarna.

Du kan också visualisera de incidenter som synkroniseras mot de berörda datorerna i Tjänstkarta.

Tjänstkarta identifierar automatiskt program komponenterna i Windows-och Linux-system och mappar kommunikationen mellan tjänsterna. Du kan visa dina servrar när du ser dem: som sammankopplade system som levererar kritiska tjänster. Tjänstkarta visar anslutningar mellan servrar, processer och portar i alla TCP-anslutna arkitekturer. Förutom installationen av en agent krävs ingen konfiguration. Mer information finns i [använda tjänstkarta](../insights/service-map.md).

Om du använder Tjänstkarta kan du Visa Service Desk-objekten som skapats i ITSM-lösningar, som du ser här:

![Skärm bild som visar Log Analytics skärmen.](media/itsmc-overview/itsmc-overview-integrated-solutions.png)

## <a name="how-to-manually-fix-servicenow-sync-problems"></a>Åtgärda ServiceNow-synkroniseringsproblem manuellt

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

f.    Granska meddelandena för att se om processen slutfördes utan framgång

## <a name="troubleshoot-itsm-connections"></a>Felsöka ITSM-anslutningar

- Gör så här om en anslutning Miss lyckas från den anslutna källans användar gränssnitt och du får ett **fel när du sparar anslutnings** meddelandet:
   - För ServiceNow-, Cherwell-och upphandlings anslutningar:  
     - Kontrol lera att du har angett användar namn, lösen ord, klient-ID och klient hemlighet korrekt för varje anslutning.  
     - Se till att du har tillräcklig behörighet i motsvarande ITSM-produkt för att upprätta anslutningen.  
   - För Service Manager anslutningar:  
     - Se till att webbappen har distribuerats och att hybrid anslutningen har skapats. Om du vill kontrol lera att anslutningen har upprättats med den lokala Service Manager datorn går du till webbappens URL enligt beskrivningen i dokumentationen för att skapa en [hybrid anslutning](./itsmc-connections.md#configure-the-hybrid-connection).  

- Om data från ServiceNow inte kommer att synkroniseras till Log Analytics, se till att ServiceNow-instansen inte är i vilo läge. ServiceNow dev-instanser går ibland till vilo läge när de är inaktiva under en längre tid. Rapportera problemet om det inte är vad som händer.
- Om Log Analytics-aviseringar för brand men arbets objekt inte skapas i ITSM-produkten, om konfigurations objekt inte skapas/länkas till arbets objekt, eller för annan information, se följande resurser:
   -  ITSMC: lösningen visar en sammanfattning av anslutningar, arbets objekt, datorer med mera. Välj den panel som har etiketten **kopplings status** . Då kommer du att **Logga sökningen** med den relevanta frågan. Titta på logg poster med en `LogType_S` av `ERROR` för mer information.
   - Sidan **loggs ökning** : Visa fel och relaterad information direkt med hjälp av frågan `*ServiceDeskLog_CL*` .

## <a name="troubleshoot-service-manager-web-app-deployment"></a>Felsöka Service Manager Web App-distribution

-   Om du har problem med att distribuera webbappar kontrollerar du att du har behörighet att skapa/distribuera resurser i prenumerationen.
-   Om du får en **objekt referens som inte är inställt på en instans av ett objekt** fel när du kör [skriptet](itsmc-service-manager-script.md), måste du se till att du har angett giltiga värden i avsnittet **användar konfiguration** .
-   Om du inte kan skapa Service Bus Relay-namnområdet kontrollerar du att den nödvändiga resurs leverantören är registrerad i prenumerationen. Om den inte är registrerad skapar du Service Bus Relay-namnområdet manuellt från Azure Portal. Du kan också skapa den när du [skapar hybrid anslutningen](./itsmc-connections.md#configure-the-hybrid-connection) i Azure Portal.

## <a name="next-steps"></a>Nästa steg

Läs mer om [IT-tjänstens hanterings anslutningar](itsmc-connections.md)
