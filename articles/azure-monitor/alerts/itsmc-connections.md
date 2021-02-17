---
title: Anslutningsprogram för hantering av IT-tjänster (ITSM) i Azure Monitor
description: Den här artikeln innehåller information om hur du ansluter dina ITSM-produkter/-tjänster med Anslutningsprogram för hantering av IT-tjänster (ITSM) (ITSMC) i Azure Monitor för att centralt övervaka och hantera ITSM arbets objekt.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/12/2020
ms.openlocfilehash: 7ac842df17e80b49990d89d1623330c4e31ab566
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100624016"
---
# <a name="connect-itsm-productsservices-with-it-service-management-connector"></a>Ansluta ITSM-produkter/-tjänster med anslutningsprogrammet för hantering av IT-tjänster (ITSM)
Den här artikeln innehåller information om hur du konfigurerar anslutningen mellan din ITSM-produkt/-tjänst och Anslutningsprogram för hantering av IT-tjänster (ITSM) (ITSMC) i Log Analytics för att centralt hantera dina arbets uppgifter. Mer information om ITSMC finns i [Översikt](../platform/itsmc-overview.md).

Följande ITSM-produkter/-tjänster stöds. Välj produkten om du vill visa detaljerad information om hur du ansluter produkten till ITSMC.

- [ServiceNow](../platform/itsmc-connections-servicenow.md)
- [System Center Service Manager](../platform/itsmc-connections-scsm.md)
- [Cherwell](../platform/itsmc-connections-cherwell.md)
- [Provance](../platform/itsmc-connections-provance.md)

> [!NOTE]
> Vi föreslår våra Cherwell och förstyrker kunder att använda [webhook-åtgärder](../platform/action-groups.md#webhook) för att Cherwell och bevisa slut punkt som en annan lösning på integrationen.

## <a name="next-steps"></a>Nästa steg

* [Felsöka problem med anslutningsprogram för hantering av IT-tjänster (ITSM)](../platform/itsmc-resync-servicenow.md)