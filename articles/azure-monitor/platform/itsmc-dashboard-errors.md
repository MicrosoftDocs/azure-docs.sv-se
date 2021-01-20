---
title: Vanliga fel
description: Det här dokumentet innehåller information om vanliga fel som finns i instrument panelen
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 01/18/2021
ms.openlocfilehash: 9521c13b21317bb0a782b0bea0b08312ff24b113
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/20/2021
ms.locfileid: "98610360"
---
# <a name="errors-in-the-connector-status"></a>Fel i anslutnings status

I listan anslutnings status hittar du fel som kan hjälpa dig att åtgärda problem i din ITSM-anslutning.

## <a name="status-common-errors"></a>Status vanliga fel

i det här avsnittet hittar du de vanliga fel som visas i avsnittet anslutnings status och hur du bör lösa det:

* **Fel**: "oväntat svar från ServiceNow tillsammans med status koden lyckades. Svar: {"import_set": "{import_set_id}", "staging_table": "x_mioms_microsoft_oms_incident", "resultat": [{"transform_map": "OMS-incident", "Tabell": "incident", "status": "fel", "error_message": {mål posten hittades inte | Ogiltig tabell | Ogiltig mellanlagringsdatabas}

    **Orsak**: det här felet returneras från ServiceNow när:
  * Ett anpassat skript som distribueras i ServiceNow-instansen gör att incidenter ignoreras.
  * "OMS Integrator-appens kod har ändrats på ServiceNow sida, t. ex. onBefore-skriptet.

  **Lösning**: inaktivera alla anpassade skript eller kod ändringar av data import Sök vägen.

* **Fel**: {"fel": {"meddelande": "åtgärden misslyckades", "information": uppdatering av ACL-undantag misslyckades på grund av säkerhets begränsningar "}"

    **Orsak**: felaktig konfiguration av ServiceNow-behörigheter

    **Lösning**: kontrol lera att alla roller har tilldelats korrekt som [angivet](itsmc-connections-servicenow.md#install-the-user-app-and-create-the-user-role).

* **Fel**: "ett fel uppstod när begäran skickades."

    **Orsak**: "ServiceNow-instans är inte tillgänglig"

    **Lösning**: kontrol lera din instans i ServiceNow att den kan tas bort eller inte är tillgänglig.

* **Fel**: "ServiceDeskHttpBadRequestException: StatusCode = 429"

    **Orsak**: ServiceNow-frekvensen är för hög/låg.

    **Lösning**: öka eller Avbryt frekvens gränserna i ServiceNow-instansen enligt beskrivningen [här](https://docs.servicenow.com/bundle/london-application-development/page/integrate/inbound-rest/task/investigate-rate-limit-violations.html).

* **Fel**: "AccessToken och RefreshToken är ogiltiga. Användaren måste autentisera igen. "

    **Orsak**: uppdaterings-token har upphört att gälla.

    **Lösning**: synkronisera ITSM-anslutningsprogram för att generera en ny uppdateringstoken enligt beskrivningen [här](./itsmc-resync-servicenow.md).

* **Fel**: "Det gick inte att skapa/uppdatera arbets objekt för aviseringen {alertName}. ITSM-anslutningsprogram {connectionIdentifier} finns inte eller har tagits bort. "

    **Orsak**: ITSM-anslutningsprogram togs bort.

    **Lösning**: ITSM-anslutningsprogram togs bort men det finns fortfarande ITSM åtgärds grupper som är kopplade till den. Det finns två alternativ för att lösa det här problemet:
  * Hitta och inaktivera eller ta bort sådan åtgärd
  * [Konfigurera om åtgärds gruppen](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts) så att den använder en befintlig ITSM-anslutningsprogram.
  * [Skapa en ny ITSM-koppling](./itsmc-definition.md#create-an-itsm-connection) och [Konfigurera om åtgärds gruppen så att den används](itsmc-definition.md#create-itsm-work-items-from-azure-alerts).

## <a name="ui-common-errors"></a>Vanliga användar gränssnitts fel

* **Fel**: "något gick fel. Det gick inte att hämta anslutnings information. " Det här felet presenteras när kunden definierar ITSM-åtgärds gruppen.

    **Orsak**: nyligen skapade ITSM-anslutningsprogram har ännu slutfört den inledande synkroniseringen.

    **Lösning**: när en ny ITSM-anslutning skapas börjar ITSM-anslutningsprogram synkronisera information från ITSM-systemet, till exempel mallar för arbets objekt och arbets objekt. Synkronisera ITSM-anslutningsprogram för att generera en ny uppdateringstoken enligt beskrivningen [här](./itsmc-resync-servicenow.md).
