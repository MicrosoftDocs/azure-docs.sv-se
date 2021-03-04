---
title: Anslut SCSM med Anslutningsprogram för hantering av IT-tjänster (ITSM)
description: Den här artikeln innehåller information om hur du Anslutningsprogram för hantering av IT-tjänster (ITSM) SCSM (ITSMC) i Azure Monitor för att centralt övervaka och hantera ITSM arbets objekt.
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/21/2020
ms.openlocfilehash: 3f80c42be217d062510c687075cf46b4e7539419
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102045351"
---
# <a name="connect-system-center-service-manager-with-it-service-management-connector"></a>Ansluta System Center Service Manager med Anslutningsprogram för hantering av IT-tjänster (ITSM)

Den här artikeln innehåller information om hur du konfigurerar anslutningen mellan System Center Service Manager-instansen och Anslutningsprogram för hantering av IT-tjänster (ITSM) (ITSMC) i Log Analytics för att centralt hantera dina arbets uppgifter.

I följande avsnitt finns information om hur du ansluter System Center Service Manager-produkten till ITSMC i Azure.

## <a name="prerequisites"></a>Förutsättningar

Se till att följande krav är uppfyllda:

- ITSMC installerad. Mer information: [Lägg till anslutningsprogram för hantering av IT-tjänster (ITSM)-lösningen](./itsmc-definition.md).
- Service Manager webb program (webbapp) distribueras och konfigureras. Information om Web App finns [här](#create-and-deploy-service-manager-web-app-service).
- Hybrid anslutning har skapats och kon figurer ATS. Mer information: [Konfigurera hybrid anslutningen](#configure-the-hybrid-connection).
- Versioner av Service Manager som stöds: 2012 R2 eller 2016.
- Användar roll:  [Avancerad operatör](/previous-versions/system-center/service-manager-2010-sp1/ff461054(v=technet.10)).
- I dag kan aviseringarna som skickas från Azure Monitor skapas i System Center Service Manager incidenter.

> [!NOTE]
> - ITSM-anslutningsprogram kan bara ansluta till molnbaserade ServiceNow-instanser. Lokala ServiceNow-instanser stöds inte för närvarande.
> - För att kunna använda anpassade [mallar](./itsmc-definition.md#define-a-template) som en del av de åtgärder som parametern "ProjectionType" i SCSM-mallen ska mappas till "IncidentManagement! System. WorkItem. incident. ProjectionType "

## <a name="connection-procedure"></a>Anslutnings procedur

Använd följande procedur för att ansluta din System Center Service Manager-instans till ITSMC:

1. I Azure Portal går du till **alla resurser** och letar efter **Servicedesk (YourWorkspaceName)**

2. Under **arbets ytans data källor** klickar du på **ITSM-anslutningar**.

    ![Ny anslutning](media/itsmc-connections-scsm/add-new-itsm-connection.png)

3. Klicka på **Lägg till** längst upp i den högra rutan.

4. Ange informationen enligt beskrivningen i följande tabell och klicka på **OK** för att skapa anslutningen.

> [!NOTE]
> Alla dessa parametrar är obligatoriska.

| **Fält** | **Beskrivning** |
| --- | --- |
| **Anslutnings namn**   | Ange ett namn för den System Center Service Manager-instans som du vill ansluta till ITSMC.  Du använder det här namnet senare när du konfigurerar arbets objekt i den här instansen/Visa detaljerad Log Analytics. |
| **Partner typ**   | Välj **System Center Service Manager**. |
| **Server-URL**   | Ange webb adressen till Service Manager webbappen. Mer information om Service Manager Web App finns [här](#create-and-deploy-service-manager-web-app-service).
| **Klient-ID**   | Ange det klient-ID som du genererade (med hjälp av det automatiska skriptet) för autentisering av webbappen. Mer information om det automatiserade skriptet finns [här.](./itsmc-service-manager-script.md)|
| **Klient hemlighet**   | Ange klient hemligheten som genererats för detta ID.   |
| **Synkronisera data**   | Välj de Service Manager arbets objekt som du vill synkronisera genom ITSMC.  Dessa arbets uppgifter importeras till Log Analytics. **Alternativ:**  Incidenter, ändrings begär Anden.|
| **Omfång för data synkronisering** | Ange antalet senaste dagar som du vill att data ska visas. **Maxgräns**: 120 dagar. |
| **Skapa nytt konfigurations objekt i ITSM-lösningen** | Välj det här alternativet om du vill skapa konfigurations objekt i ITSM-produkten. När det här alternativet är markerat skapar Log Analytics det påverkade CIs-objektet som konfigurations objekt (om det inte finns något befintligt CIs) i det ITSM system som stöds. **Standard**: inaktiverat. |

![Service Manager-anslutning](media/itsmc-connections-scsm/service-manager-connection.png)

**När du har anslutit och synkroniserat**:

- De valda arbets objekten från Service Manager importeras till Azure **Log Analytics.** Du kan visa sammanfattningen av dessa arbets uppgifter på Anslutningsprogram för hantering av IT-tjänster (ITSM)s panelen.

- Du kan skapa incidenter från Log Analytics aviseringar eller från logg poster eller från Azure-aviseringar i den här Service Manager-instansen.

Läs mer: [skapa ITSM arbets objekt från Azure-aviseringar](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts).

## <a name="create-and-deploy-service-manager-web-app-service"></a>Skapa och distribuera Service Manager Web App Service

För att ansluta den lokala Service Manager med ITSMC i Azure har Microsoft skapat en Service Manager webbapp på GitHub.

Gör så här för att konfigurera ITSM-webbappen för din Service Manager:

- **Distribuera webbappen** – distribuera webbappen, ange egenskaperna och autentisera med Azure AD. Du kan distribuera webbappen med hjälp av det [automatiserade skriptet](./itsmc-service-manager-script.md) som Microsoft tillhandahåller.
- **Konfigurera hybrid anslutningen**  -  [Konfigurera den här anslutningen](#configure-the-hybrid-connection), manuellt.

### <a name="deploy-the-web-app"></a>Distribuera webbappen
Använd det automatiserade [skriptet](./itsmc-service-manager-script.md) för att distribuera webbappen, ange egenskaperna och autentisera med Azure AD.

Kör skriptet genom att ange följande obligatoriska information:

- Information om Azure-prenumeration
- Namn på resursgrupp
- Location
- Service Manager Server information (Server namn, domän, användar namn och lösen ord)
- Prefix för webbplats namn för din webbapp
- Service Bus-namnrymd.

Skriptet skapar webbappen med det namn som du har angett (tillsammans med några ytterligare strängar för att göra den unik). Den genererar **webbappens webb adress**, **klient-ID** och **klient hemlighet**.

Spara värdena, du använder dem när du skapar en anslutning med ITSMC.

**Kontrol lera installationen av webbapp**

1. Gå till **Azure Portal**-  >  **resurser**.
2. Välj webbapp, klicka på **Inställningar**  >  **program inställningar**.
3. Bekräfta informationen om Service Manager-instansen som du angav när du distribuerade appen via skriptet.

## <a name="configure-the-hybrid-connection"></a>Konfigurera hybrid anslutningen

Använd följande procedur för att konfigurera hybrid anslutningen som ansluter Service Manager-instansen med ITSMC i Azure.

1. Hitta Service Manager-webbappen under **Azure-resurser**.
2. Klicka på **Inställningar**  >  **nätverk**.
3. Under **hybridanslutningar** klickar **du på Konfigurera hybrid anslutnings slut punkter**.

    ![Anslutning till hybrid anslutning](media/itsmc-connections-scsm/itsmc-hybrid-connection-networking-and-end-points.png)
4. Klicka på **Lägg till hybrid anslutning** på bladet **hybridanslutningar** .

    ![Hybrid anslutning, Lägg till](media/itsmc-connections-scsm/itsmc-new-hybrid-connection-add.png)

5. Klicka på **Skapa ny hybrid anslutning** i bladet **Lägg till hybridanslutningar** .

    ![Ny hybrid anslutning](media/itsmc-connections-scsm/itsmc-create-new-hybrid-connection.png)

6. Ange följande värden:

   - **Slut punkts namn**: Ange ett namn för den nya hybrid anslutningen.
   - **Slut punkts värd**: FQDN för service Managers hanterings servern.
   - **Slut punkts port**: typ 5724
   - **Service Bus-namnrymd**: Använd ett befintligt Service Bus-namnområde eller skapa ett nytt.
   - **Plats**: Välj platsen.
   - **Namn**: Ange ett namn på Service Bus om du skapar det.

     ![Hybrid anslutnings värden](media/itsmc-connections-scsm/itsmc-new-hybrid-connection-values.png)
6. Klicka på **OK** för att stänga bladet **skapa hybrid anslutning** och börja skapa hybrid anslutningen.

    När hybrid anslutningen har skapats visas den under bladet.

7. När hybrid anslutningen har skapats väljer du anslutningen och klickar på **Lägg till vald hybrid anslutning**.

    ![Ny hybrid anslutning](media/itsmc-connections-scsm/itsmc-new-hybrid-connection-added.png)

### <a name="configure-the-listener-setup"></a>Konfigurera Listener-installationen

Använd följande procedur för att konfigurera Listener-installationen för Hybrid anslutningen.

1. I bladet **hybridanslutningar** klickar **du på Hämta anslutnings hanteraren** och installerar den på den dator där System Center Service Manager instansen körs.

    När installationen är klar finns **HYBRIDANSLUTNINGSHANTERAREN UI** -alternativet på **Start** menyn.

2. Klicka på **Hybridanslutningshanteraren användar gränssnitt** , så uppmanas du att ange dina autentiseringsuppgifter för Azure.

3. Logga in med dina Azure-autentiseringsuppgifter och välj din prenumeration där hybrid anslutningen skapades.

4. Klicka på **Spara**.

Din hybrid anslutning har upprättats.

![lyckad hybrid anslutning](media/itsmc-connections-scsm/itsmc-hybrid-connection-listener-set-up-successful.png)
> [!NOTE]
> 
> När hybrid anslutningen har skapats kontrollerar du och testar anslutningen genom att gå till den distribuerade Service Manager-webbappen. Se till att anslutningen lyckas innan du försöker ansluta till ITSMC i Azure.

Följande exempel bild visar information om en lyckad anslutning:

![Test av hybrid anslutning](media/itsmc-connections-scsm/itsmc-hybrid-connection-test.png)

## <a name="next-steps"></a>Nästa steg

* [Översikt över anslutningsprogram för hantering av IT-tjänster (ITSM)](itsmc-overview.md)
* [Skapa ITSM arbets objekt från Azure-aviseringar](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts)
* [Felsöka problem med anslutningsprogram för hantering av IT-tjänster (ITSM)](./itsmc-resync-servicenow.md)