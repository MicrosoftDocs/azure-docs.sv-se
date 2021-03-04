---
title: Anslutnings status fel i ITSMC-instrumentpanelen
description: Lär dig mer om vanliga fel som finns på instrument panelen för Anslutningsprogram för hantering av IT-tjänster (ITSM).
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 01/18/2021
ms.openlocfilehash: 5cc3c4a07cc698f3592a2ff2fd76e9f4bbef441b
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102036460"
---
# <a name="connector-status-errors-in-the-itsmc-dashboard"></a>Anslutnings status fel i ITSMC-instrumentpanelen

Instrument panelen för Anslutningsprogram för hantering av IT-tjänster (ITSM) (ITSMC) visar fel som kan hjälpa dig att åtgärda problem i din anslutning.

I följande avsnitt beskrivs vanliga fel som visas i avsnittet anslutnings status på instrument panelen och hur du kan lösa dem.

## <a name="unexpected-response"></a>Oväntat svar

**Fel**: "oväntat svar från ServiceNow tillsammans med status koden lyckades. Svar: {"import_set": "{import_set_id}", "staging_table": "x_mioms_microsoft_oms_incident", "resultat": [{"transform_map": "OMS-incident", "Tabell": "incident", "status": "fel", "error_message": {mål posten hittades inte | Ogiltig tabell | Ogiltig mellanlagringsdatabas}

**Orsak**: ServiceNow returnerar det här felet när:

* Ett anpassat skript som distribueras i en ServiceNow-instans gör att incidenter ignoreras.
* Koden "OMS Integrator app" har ändrats på ServiceNow-sidan (till exempel via `onBefore` skriptet).

**Lösning**: inaktivera alla anpassade skript eller kod ändringar.

## <a name="exception-update-failure"></a>Undantags uppdaterings fel

**Fel**: {"fel": {"meddelande": "åtgärden misslyckades", "information": uppdatering av ACL-undantag misslyckades på grund av säkerhets begränsningar "}"

**Orsak**: ServiceNow-behörigheter är felkonfigurerade.

**Lösning**: kontrol lera att alla roller har tilldelats korrekt som [angivet](itsmc-connections-servicenow.md#install-the-user-app-and-create-the-user-role).

## <a name="problem-sending-a-request"></a>Problem med att skicka en begäran

**Fel**: "ett fel uppstod när begäran skickades."

**Orsak**: en ServiceNow-instans är inte tillgänglig.

**Lösning**: kontrol lera din instans i ServiceNow. Det kan vara borttaget eller otillgängligt.

## <a name="servicenow-rate-problem"></a>Problem med ServiceNow-frekvens

**Fel**: "ServiceDeskHttpBadRequestException: StatusCode = 429"

**Orsak**: ServiceNows hastighets begränsningar är för höga eller för lågt.

**Lösning**: öka eller Avbryt frekvens gränserna i ServiceNow-instansen enligt beskrivningen i [ServiceNow-dokumentationen](https://docs.servicenow.com/bundle/london-application-development/page/integrate/inbound-rest/task/investigate-rate-limit-violations.html).

## <a name="invalid-refresh-token"></a>Ogiltig uppdateringstoken

**Fel**: 
  * "AccessToken och RefreshToken är ogiltiga. Användaren måste autentisera igen. "
  * "Det gick inte att synkronisera mallars konfigurationen för händelse, avisering, incident. Mer information finns i undantags meddelandet. "

**Orsak**: en uppdaterad token har upphört att gälla.

**Lösning**: synkronisera ITSMC för att generera en ny uppdateringstoken, enligt beskrivningen i [så här åtgärdar du synkroniseringsproblem manuellt](./itsmc-resync-servicenow.md).

## <a name="missing-connector"></a>Koppling saknas

**Fel**: "Det gick inte att skapa/uppdatera arbets objekt för aviseringen {alertName}. ITSM-anslutningsprogram {connectionIdentifier} finns inte eller har tagits bort. "

**Orsak**: ITSMC togs bort.

**Lösning**: ITSMC har tagits bort, men de definierade åtgärds grupperna IT Service Management (ITSM) är fortfarande kopplade till den. Det finns tre alternativ för att lösa det här problemet:

* Hitta och inaktivera eller ta bort sådana åtgärds grupper.
* [Konfigurera om åtgärds grupperna](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts) så att de använder en befintlig ITSMC-instans.
* [Skapa en ny ITSMC-instans](./itsmc-definition.md#create-an-itsm-connection) och [Konfigurera om de åtgärds grupper som ska användas](itsmc-definition.md#create-itsm-work-items-from-azure-alerts).

## <a name="lack-of-connection-details"></a>Avsaknad av anslutnings information

**Fel**: "något gick fel. Det gick inte att hämta anslutnings information. " Det här felet visas när du definierar en ITSM-åtgärds grupp.

**Orsak**: ett sådant fel visas i någon av följande situationer:

* En nyligen skapad ITSM-anslutningsprogram-instans har ännu slutfört den inledande synkroniseringen.
* Kopplingen har inte definierats korrekt.

**Lösning**: 

* När en ny ITSMC-instans skapas börjar den synkronisera information från ITSM-systemet, till exempel mallar för arbets objekt och arbets objekt. [Synkronisera ITSMC för att generera en ny uppdateringstoken](./itsmc-resync-servicenow.md).
* [Granska anslutnings informationen i ITSMC](./itsmc-connections-servicenow.md#create-a-connection) och kontrol lera att ITSMC kan [synkroniseras](./itsmc-resync-servicenow.md).
