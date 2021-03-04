---
title: Data har synkroniserats från din ITSM-produkt till LA-arbetsytan
description: Den här artikeln innehåller en översikt över data som synkroniseras från ITSM-produkten till LA-arbetsytan.
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/29/2020
ms.custom: references_regions
ms.openlocfilehash: 83994c1b6e150342a777a079d79d6a594d30c3ff
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102041611"
---
# <a name="data-synced-from-your-itsm-product"></a>Data har synkroniserats från din ITSM-produkt

Incidenter och ändrings begär Anden synkroniseras från ITSM-verktyget till din Log Analytics-arbetsyta, baserat på anslutningens konfiguration (med fältet synkronisera data):
* [ServiceNow](./itsmc-connections-servicenow.md)
* [System Center Service Manager](./itsmc-connections-scsm.md)
* [Cherwell](./itsmc-connections-cherwell.md)
* [Provance](./itsmc-connections-provance.md)

## <a name="synced-data"></a>Synkroniserade data

I det här avsnittet visas några exempel på data som samlas in av ITSMC.

Fälten i **ServiceDesk_CL** varierar beroende på vilken typ av arbets objekt som du importerar till Log Analytics. Här är en lista över fält för två arbets objekts typer:

**Arbets objekt:** **incidenter**  
ServiceDeskWorkItemType_s = "incident"

**Fält**

- ServiceDeskConnectionName
- Service Desk-ID
- Stat
- Angelägenhetsgrad
- Påverkan
- Prioritet
- Eskalering
- Skapad av
- Löst av
- Stängd av
- Källa
- Tilldelad till
- Kategori
- Rubrik
- Beskrivning
- Skapad datum
- Stängningsdatum
- Lösningsdatum
- Senast ändrad datum
- Dator

**Arbets objekt:** **ändrings begär Anden**

ServiceDeskWorkItemType_s = "ändringsbegäran"

**Fält**
- ServiceDeskConnectionName
- Service Desk-ID
- Skapad av
- Stängd av
- Källa
- Tilldelad till
- Rubrik
- Typ
- Kategori
- Stat
- Eskalering
- Konflikt status
- Angelägenhetsgrad
- Prioritet
- Risk
- Påverkan
- Tilldelad till
- Skapad datum
- Stängningsdatum
- Senast ändrad datum
- Begärt datum
- Planerat start datum
- Planerat slutdatum
- Start datum för arbete
- Slutdatum för arbete
- Beskrivning
- Dator

## <a name="servicenow-example"></a>ServiceNow-exempel 
### <a name="output-data-for-a-servicenow-incident"></a>Utdata för en ServiceNow-incident

| Log Analytics fält | ServiceNow-fält |
|:--- |:--- |
| ServiceDeskId_s| Antal |
| IncidentState_s | Stat |
| Urgency_s |Angelägenhetsgrad |
| Impact_s |Påverkan|
| Priority_s | Prioritet |
| CreatedBy_s | Öppnad av |
| ResolvedBy_s | Löst av|
| ClosedBy_s  | Stängd av |
| Source_s| Kontakt typ |
| AssignedTo_s | Tilldelad  |
| Category_s | Kategori |
| Title_s|  Kort beskrivning |
| Description_s|  Kommentarer |
| CreatedDate_t|  Inleddes |
| ClosedDate_t| stängd|
| ResolvedDate_t|Matchat|
| Dator  | Konfigurationsobjekt |

### <a name="output-data-for-a-servicenow-change-request"></a>Utdata för en ServiceNow-ändringsbegäran

| Log Analytics | ServiceNow-fält |
|:--- |:--- |
| ServiceDeskId_s| Antal |
| CreatedBy_s | Begärd av |
| ClosedBy_s | Stängd av |
| AssignedTo_s | Tilldelad  |
| Title_s|  Kort beskrivning |
| Type_s|  Typ |
| Category_s|  Kategori |
| CRState_s|  Stat|
| Urgency_s|  Angelägenhetsgrad |
| Priority_s| Prioritet|
| Risk_s| Risk|
| Impact_s| Påverkan|
| RequestedDate_t  | Begärd efter datum |
| ClosedDate_t | Stängnings datum |
| PlannedStartDate_t  | Planerat start datum |
| PlannedEndDate_t  | Planerat slutdatum |
| WorkStartDate_t  | Verkligt start datum |
| WorkEndDate_t | Verkligt slutdatum|
| Description_s | Beskrivning |
| Dator  | Konfigurations objekt |

## <a name="next-steps"></a>Nästa steg

* [Felsöka problem med anslutningsprogram för hantering av IT-tjänster (ITSM)](./itsmc-resync-servicenow.md)
