---
title: Anslutningsprogram för hantering av IT-tjänster (ITSM) i Azure Monitor
description: Den här artikeln innehåller information om hur du ansluter dina ITSM-produkter/-tjänster med Anslutningsprogram för hantering av IT-tjänster (ITSM) (ITSMC) i Azure Monitor för att centralt övervaka och hantera ITSM arbets objekt.
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/12/2020
ms.openlocfilehash: 40e737a1ec5fb34cd22a08925143a100d36cdc6b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103009325"
---
# <a name="connect-itsm-productsservices-with-it-service-management-connector"></a>Ansluta ITSM-produkter/-tjänster med anslutningsprogrammet för hantering av IT-tjänster (ITSM)
Den här artikeln innehåller information om hur du konfigurerar anslutningen mellan din ITSM-produkt/-tjänst och Anslutningsprogram för hantering av IT-tjänster (ITSM) (ITSMC) i Log Analytics för att centralt hantera dina arbets uppgifter. Mer information om ITSMC finns i [Översikt](./itsmc-overview.md).

Följande ITSM-produkter/-tjänster stöds. Välj produkten om du vill visa detaljerad information om hur du ansluter produkten till ITSMC.

- [ServiceNow](./itsmc-connections-servicenow.md)
- [System Center Service Manager](./itsmc-connections-scsm.md)
- [Cherwell](./itsmc-connections-cherwell.md)
- [Provance](./itsmc-connections-provance.md)

> [!NOTE]
> Vi föreslår våra Cherwell och förstyrker kunder att använda [webhook-åtgärder](./action-groups.md#webhook) för att Cherwell och bevisa slut punkt som en annan lösning på integrationen.

## <a name="ip-ranges-for-itsm-partners-connections"></a>IP-intervall för anslutningar för ITSM-partner
För att få en lista över ITSM-IP-adresserna för att tillåta ITSM-anslutningar från partners ITSM verktyg rekommenderar vi att du listar hela det offentliga IP-intervallet för Azure-regionen där deras LogAnalytics-arbetsytan tillhör. [information här](https://www.microsoft.com/en-us/download/details.aspx?id=56519) För regionerna EUS/WEU/EUS2/WUS2/USA, södra centrala kunde kunden endast lista ActionGroup nätverks tag.

## <a name="next-steps"></a>Nästa steg

* [Felsöka problem med anslutningsprogram för hantering av IT-tjänster (ITSM)](./itsmc-resync-servicenow.md)
