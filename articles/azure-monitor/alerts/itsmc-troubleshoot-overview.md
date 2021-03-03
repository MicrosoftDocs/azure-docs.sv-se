---
title: Felsöka problem i ITSMC
description: Lär dig hur du löser vanliga problem i Anslutningsprogram för hantering av IT-tjänster (ITSM).
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: a01005231b5d775f79555ec10dedeb3f30b3426a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101737213"
---
# <a name="troubleshoot-problems-in-it-service-management-connector"></a>Felsöka problem i Anslutningsprogram för hantering av IT-tjänster (ITSM)

I den här artikeln beskrivs vanliga problem i Anslutningsprogram för hantering av IT-tjänster (ITSM) (ITSMC) och hur du felsöker dem.

Azure Monitor proaktivt meddela dig i aviseringar när det hittar viktiga villkor i dina övervaknings data. De här aviseringarna hjälper dig att identifiera och åtgärda problem innan användarna av systemet kan se dem.

Du kan välja hur du vill få aviseringar. Du kan välja e-post, SMS eller webhook eller till och med automatisera en lösning. 

Ett alternativ är att meddelas via ITSMC. ITSMC ger dig möjlighet att skicka aviseringar till ett externt biljett system, till exempel ServiceNow.

## <a name="use-the-dashboard-to-analyze-incident-and-change-request-data"></a>Använd instrument panelen för att analysera incident-och data för ändringsbegäran

Beroende på din konfiguration när du konfigurerar en anslutning kan ITSMC synkronisera upp till 120 dagar av incidenten och data för ändringsbegäran. För att hämta logg postens schema för dessa data, se de [data som synkroniseras från din ITSM produkt](./itsmc-synced-data.md) artikel.

Du kan visualisera incident-och ändrings begär ande data med hjälp av ITSMC-instrument panelen:

![Skärm bild som visar ITSMC-instrumentpanelen.](media/itsmc-overview/itsmc-overview-sample-log-analytics.png)

Instrument panelen innehåller också information om anslutnings status. Du kan använda den informationen som en utgångs punkt för att analysera problem med anslutningarna. Mer information finns i [fel undersökning med hjälp av instrument panelen](./itsmc-dashboard.md).

## <a name="use-service-map-to-visualize-incidents"></a>Använd Tjänstkarta för att visualisera incidenter

Du kan också visualisera de incidenter som synkroniseras mot de berörda datorerna i Tjänstkarta.

Tjänstkarta identifierar automatiskt program komponenterna i Windows-och Linux-system och mappar kommunikationen mellan tjänsterna. Du kan visa dina servrar när du ser dem: som sammankopplade system som levererar kritiska tjänster. 

Tjänstkarta visar anslutningar mellan servrar, processer och portar i alla TCP-anslutna arkitekturer. Förutom installationen av en agent krävs ingen konfiguration. Mer information finns i [använda tjänstkarta](../vm/service-map.md).

Om du använder Tjänstkarta kan du Visa Service Desk-objekten som skapats i IT Service Management (ITSM)-lösningar, som du ser i det här exemplet:

![Skärm bild som visar Log Analytics skärmen.](media/itsmc-overview/itsmc-overview-integrated-solutions.png)

## <a name="resolve-problems"></a>Lösa problem

I följande avsnitt hittar du vanliga symptom, möjliga orsaker och lösningar. 

### <a name="a-connection-to-the-itsm-system-fails-and-you-get-an-error-in-saving-connection-message"></a>En anslutning till ITSM-systemet Miss lyckas och du får meddelandet "fel vid sparande av anslutning"

**Orsak**: orsaken kan vara något av följande:

* Autentiseringsuppgifterna är felaktiga.
* Behörigheterna är otillräckliga.
* För Service Manager-anslutningar: webbappen har distribuerats felaktigt.

**Lösning**:

* För ServiceNow-, Cherwell-och upphandlings anslutningar:
  * Kontrol lera att du har angett användar namn, lösen ord, klient-ID och klient hemlighet korrekt för varje anslutning.  
  * För ServiceNow kontrollerar du att du har [tillräcklig behörighet](itsmc-connections-servicenow.md#install-the-user-app-and-create-the-user-role) för motsvarande ITSM-produkt.

* För Service Manager anslutningar:  
  * Se till att webbappen har distribuerats och att hybrid anslutningen har skapats. Du kan kontrol lera att anslutningen har upprättats med den lokala Service Manager datorn genom att gå till webbappens webb adress enligt beskrivningen i [dokumentationen för att skapa en hybrid anslutning](./itsmc-connections-scsm.md#configure-the-hybrid-connection).  

### <a name="duplicate-work-items-are-created"></a>Dubbla arbets objekt skapas

**Orsak**: orsaken kan vara något av följande två alternativ:

* Fler än en ITSM-åtgärd har definierats för aviseringen.
* Aviseringen har åtgärd ATS.

**Lösning**: det kan finnas två lösningar:

* Se till att du har en enda ITSM-åtgärds grupp per avisering.
* ITSMC stöder inte matchande arbets objekts status uppdateringar när en avisering har lösts. Skapa en ny åtgärdad arbets uppgift.

### <a name="work-items-are-not-created"></a>Arbets objekt har inte skapats

**Orsak**: det kan finnas flera orsaker till det här problemet:

* Koden har ändrats på ServiceNow-sidan.
* Behörigheterna är felkonfigurerade.
* ServiceNow hastighets begränsningar är för höga eller för lågt.
* En uppdateringstoken har upphört att gälla.
* ITSMC har tagits bort.

**Lösning**: kontrol lera [instrument panelen](itsmc-dashboard.md) och granska felen i avsnittet för anslutnings status. Granska sedan de [vanliga felen och lösningarna](itsmc-dashboard-errors.md).

### <a name="you-cant-create-an-itsm-action-for-an-action-group"></a>Det går inte att skapa en ITSM-åtgärd för en åtgärds grupp

**Orsak**: en nyligen skapad ITSMC-instans har ännu inte slutfört den inledande synkroniseringen.

**Lösning**: granska [vanliga fel och lösningar](itsmc-dashboard-errors.md).

### <a name="sync-connection"></a>Synkronisera anslutning 

**Orsak**: det kan finnas flera orsaker till det här problemet:

* Mallar visas inte som en del av åtgärds definitionen.
* Incedents/Events skapas inte i ServiceNow.

**Lösning**: [Synkronisera anslutningen](itsmc-resync-servicenow.md).
