---
title: Felsöka problem med anslutningsprogram för hantering av IT-tjänster (ITSM)
description: Felsöka problem i Anslutningsprogram för hantering av IT-tjänster (ITSM)
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: a4a7b7a4008d5cc4636e2d533c225a618f35af05
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/20/2021
ms.locfileid: "98611193"
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
   -  ITSMC: lösningen visar en [Sammanfattning av anslutningar](itsmc-dashboard.md), arbets objekt, datorer med mera. Välj den panel som har etiketten **kopplings status** . Då kommer du att **Logga sökningen** med den relevanta frågan. Titta på logg poster med en `LogType_S` av `ERROR` för mer information.
   Du kan se information om meddelandena i tabellen – [här](itsmc-dashboard-errors.md).
   - Sidan **loggs ökning** : Visa fel och relaterad information direkt med hjälp av frågan `*ServiceDeskLog_CL*` .

## <a name="common-symptoms---how-should-it-be-resolved"></a>Vanliga symtom – hur ska det lösas?

Listan nedan innehåller vanliga symtom och hur ska den lösas:

* **Symptom**: dubbla arbets objekt skapas

    **Orsak**: orsaken kan vara något av de två alternativen:
    * Fler än en ITSM-åtgärd har definierats för aviseringen.
    * Aviseringen har lösts.

    **Lösning**: det kan finnas två lösningar:
    * Se till att du har en enda ITSM-åtgärds grupp per avisering.
    * ITSM-anslutningsprogram stöder inte matchande arbets objekts status uppdatering när en avisering har lösts. Ett nytt löst arbets objekt har skapats.
* **Symptom**: arbets objekt har inte skapats

    **Orsak**: det kan finnas några orsaker till det här problemet:
    * Kod ändring på ServiceNow sida
    * Felaktig konfiguration av behörigheter
    * ServiceNow hastighets begränsningar är för höga/låga
    * Uppdateringstoken har upphört att gälla
    * ITSM-anslutningsprogram har tagits bort

    **Lösning**: du kan kontrol lera [instrument panelen](itsmc-dashboard.md) och granska felen i avsnittet anslutnings status. Granska de [vanliga felen](itsmc-dashboard-errors.md) och ta reda på hur du kan lösa felet.

* **Symptom**: det gick inte att skapa ITSM-åtgärd för åtgärds gruppen

    **Orsak**: nyligen skapade ITSM-anslutningsprogram har ännu slutfört den inledande synkroniseringen.

    **Lösning**: du kan granska [vanliga användar gränssnitts fel](itsmc-dashboard-errors.md#ui-common-errors) och ta reda på hur du kan lösa felet.