---
title: Anslut förhandlingen med Anslutningsprogram för hantering av IT-tjänster (ITSM)
description: Den här artikeln innehåller information om hur du kan bevisa med Anslutningsprogram för hantering av IT-tjänster (ITSM) (ITSMC) i Azure Monitor för att centralt övervaka och hantera ITSM-arbetsobjekten.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/21/2020
ms.openlocfilehash: 0cdbcf0a895a2fc9b81df464f364e04aa79abf99
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2021
ms.locfileid: "100006776"
---
# <a name="connect-provance-with-it-service-management-connector"></a>Anslut förhandlingen med Anslutningsprogram för hantering av IT-tjänster (ITSM)

Den här artikeln innehåller information om hur du konfigurerar anslutningen mellan din instans av upphandlings instansen och Anslutningsprogram för hantering av IT-tjänster (ITSM) (ITSMC) i Log Analytics för att centralt hantera dina arbets uppgifter.

> [!NOTE]
> Från och med 1 – okt-2020-ITSM-integrering med Azure-avisering kommer inte längre att vara aktive rad för nya kunder. Det går inte att använda nya ITSM-anslutningar.
> Befintliga ITSM-anslutningar kommer att stödjas.

Följande avsnitt innehåller information om hur du ansluter din bestyrkande produkt till ITSMC i Azure.

## <a name="prerequisites"></a>Förutsättningar

Se till att följande krav är uppfyllda:

- ITSMC installerad. Mer information: [Lägg till anslutningsprogram för hantering av IT-tjänster (ITSM)-lösningen](./itsmc-definition.md#add-it-service-management-connector).
- Upphandlings appen bör registreras med Azure AD-och klient-ID är tillgängligt. Detaljerad information finns i [så här konfigurerar du Active Directory-autentisering](../../app-service/configure-authentication-provider-aad.md).

- Användar roll: administratör.

## <a name="connection-procedure"></a>Anslutnings procedur

Använd följande procedur för att skapa en upphandlings anslutning:

1. I Azure Portal går du till **alla resurser** och letar efter **Servicedesk (YourWorkspaceName)**

2. Under **arbets ytans data källor** klickar du på **ITSM-anslutningar**.
    ![Ny anslutning](media/itsmc-connections/add-new-itsm-connection.png)

3. Klicka på **Lägg till** längst upp i den högra rutan.

4. Ange informationen enligt beskrivningen i följande tabell och klicka på **OK** för att skapa anslutningen.

> [!NOTE]
> Alla dessa parametrar är obligatoriska.

| **Fält** | **Beskrivning** |
| --- | --- |
| **Anslutnings namn**   | Ange ett namn på den instans av upphandlingen som du vill ansluta till ITSMC.  Du använder det här namnet senare när du konfigurerar arbets objekt i den här ITSM/Visa detaljerad Log Analytics. |
| **Partner typ**   | Välj **styrka**. |
| **Användarnamn**   | Ange användar namnet som kan ansluta till ITSMC.    |
| **Lösenord**   | Ange lösen ordet som är kopplat till det här användar namnet. **Obs:** Användar namn och lösen ord används endast för att skapa autentiseringstoken och lagras inte var som helst i ITSMC-tjänsten.|
| **Server-URL**   | Ange URL: en för din instans av instansen som du vill ansluta till ITSMC. |
| **Klient-ID**   | Ange klient-ID för att autentisera anslutningen, som du skapade i din upphandlings instans.  Mer information om klient-ID finns i [Konfigurera Active Directory-autentisering](../../app-service/configure-authentication-provider-aad.md). |
| **Omfång för data synkronisering**   | Välj de arbets objekt för styrka som du vill synkronisera till Azure Log Analytics via ITSMC.  Dessa arbets uppgifter importeras till Log Analytics.   **Alternativ:**   Incidenter, ändrings begär Anden.|
| **Synkronisera data** | Ange antalet senaste dagar som du vill att data ska visas. **Maxgräns**: 120 dagar. |
| **Skapa nytt konfigurations objekt i ITSM-lösningen** | Välj det här alternativet om du vill skapa konfigurations objekt i ITSM-produkten. När det här alternativet är markerat skapar ITSMC det påverkade CIs-objektet som konfigurations objekt (om det inte finns något befintligt CIs) i det ITSM system som stöds. **Standard**: inaktiverat.|

![Skärm bild som visar listorna anslutnings namn och partner typ.](media/itsmc-connections/itsm-connections-provance-latest.png)

**När du har anslutit och synkroniserat**:

- De valda arbets objekten från denna instans av bevisning importeras till Azure **Log Analytics.** Du kan visa sammanfattningen av dessa arbets uppgifter på Anslutningsprogram för hantering av IT-tjänster (ITSM)s panelen.

- Du kan skapa incidenter från Log Analytics aviseringar eller från logg poster eller från Azure-aviseringar i den här upphandlings instansen.

## <a name="next-steps"></a>Nästa steg

* [Översikt över anslutningsprogram för hantering av IT-tjänster (ITSM)](itsmc-overview.md)
* [Skapa ITSM arbets objekt från Azure-aviseringar](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts)
* [Felsöka problem med anslutningsprogram för hantering av IT-tjänster (ITSM)](./itsmc-resync-servicenow.md)