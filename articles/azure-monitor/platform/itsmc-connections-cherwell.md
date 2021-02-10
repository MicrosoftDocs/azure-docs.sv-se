---
title: Anslut Cherwell med Anslutningsprogram för hantering av IT-tjänster (ITSM)
description: Den här artikeln innehåller information om hur du Anslutningsprogram för hantering av IT-tjänster (ITSM) Cherwell (ITSMC) i Azure Monitor för att centralt övervaka och hantera ITSM arbets objekt.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/21/2020
ms.openlocfilehash: a24ece3c0b75d0d0d22e13c6e5367c27fbaeab66
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2021
ms.locfileid: "100006793"
---
# <a name="connect-cherwell-with-it-service-management-connector"></a>Anslut Cherwell med Anslutningsprogram för hantering av IT-tjänster (ITSM)

Den här artikeln innehåller information om hur du konfigurerar anslutningen mellan Cherwell-instansen och Anslutningsprogram för hantering av IT-tjänster (ITSM) (ITSMC) i Log Analytics för att centralt hantera dina arbets uppgifter.

> [!NOTE]
> Från och med 1 – okt-2020 Cherwell ITSM-integrering med Azure Alert kommer inte längre att aktive ras för nya kunder. Det går inte att använda nya ITSM-anslutningar.
> Befintliga ITSM-anslutningar kommer att stödjas.

Följande avsnitt innehåller information om hur du ansluter din Cherwell-produkt till ITSMC i Azure.

## <a name="prerequisites"></a>Förutsättningar

Se till att följande krav är uppfyllda:

- ITSMC installerad. Mer information: [Lägg till anslutningsprogram för hantering av IT-tjänster (ITSM)-lösningen](./itsmc-definition.md#add-it-service-management-connector).
- Ett klient-ID genererades. Mer information: [generera klient-ID för Cherwell](#generate-client-id-for-cherwell).
- Användar roll: administratör.

## <a name="connection-procedure"></a>Anslutnings procedur

Använd följande procedur för att skapa en Cherwell-anslutning:

1. I Azure Portal går du till **alla resurser** och letar efter **Servicedesk (YourWorkspaceName)**

2. Under **arbets ytans data källor** klickar du på **ITSM-anslutningar**.
    ![Ny anslutning](media/itsmc-connections/add-new-itsm-connection.png)

3. Klicka på **Lägg till** längst upp i den högra rutan.

4. Ange informationen enligt beskrivningen i följande tabell och klicka på **OK** för att skapa anslutningen.

> [!NOTE]
> Alla dessa parametrar är obligatoriska.

| **Fält** | **Beskrivning** |
| --- | --- |
| **Anslutnings namn**   | Ange ett namn för den Cherwell-instans som du vill ansluta till ITSMC.  Du använder det här namnet senare när du konfigurerar arbets objekt i den här ITSM/Visa detaljerad Log Analytics. |
| **Partner typ**   | Välj **Cherwell.** |
| **Användarnamn**   | Ange det användar namn för Cherwell som kan ansluta till ITSMC. |
| **Lösenord**   | Ange lösen ordet som är kopplat till det här användar namnet. **Obs:** Användar namn och lösen ord används endast för att skapa autentiseringstoken och lagras inte var som helst i ITSMC-tjänsten.|
| **Server-URL**   | Ange URL: en för den Cherwell-instans som du vill ansluta till ITSMC. |
| **Klient-ID**   | Ange klient-ID för att autentisera anslutningen, som du skapade i din Cherwell-instans.   |
| **Omfång för data synkronisering**   | Välj de Cherwell arbets objekt som du vill synkronisera via ITSMC.  Dessa arbets uppgifter importeras till Log Analytics.   **Alternativ:**  Incidenter, ändrings begär Anden. |
| **Synkronisera data** | Ange antalet senaste dagar som du vill att data ska visas. **Maxgräns**: 120 dagar. |
| **Skapa nytt konfigurations objekt i ITSM-lösningen** | Välj det här alternativet om du vill skapa konfigurations objekt i ITSM-produkten. När det här alternativet är markerat skapar ITSMC det påverkade CIs-objektet som konfigurations objekt (om det inte finns något befintligt CIs) i det ITSM system som stöds. **Standard**: inaktiverat. |

![Cherwell-anslutning](media/itsmc-connections/itsm-connections-cherwell-latest.png)

**När du har anslutit och synkroniserat**:

- De valda arbets objekten från den här Cherwell-instansen importeras till Azure **Log Analytics.** Du kan visa sammanfattningen av dessa arbets uppgifter på Anslutningsprogram för hantering av IT-tjänster (ITSM)s panelen.

- Du kan skapa incidenter från Log Analytics aviseringar eller från logg poster eller från Azure-aviseringar i den här Cherwell-instansen.

Läs mer: [skapa ITSM arbets objekt från Azure-aviseringar](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts).

### <a name="generate-client-id-for-cherwell"></a>Generera klient-ID för Cherwell

Använd följande procedur för att generera klient-ID/nyckel för Cherwell:

1. Logga in på din Cherwell-instans som administratör.
2. Klicka på **säkerhets**  >  **Redigera REST API klient inställningar**.
3. Välj **skapa en ny klients** klient  >  **hemlighet**.

    ![Användar-ID för Cherwell](media/itsmc-connections/itsmc-cherwell-client-id.png)

## <a name="next-steps"></a>Nästa steg

* [Översikt över anslutningsprogram för hantering av IT-tjänster (ITSM)](itsmc-overview.md)
* [Skapa ITSM arbets objekt från Azure-aviseringar](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts)
* [Felsöka problem med anslutningsprogram för hantering av IT-tjänster (ITSM)](./itsmc-resync-servicenow.md)