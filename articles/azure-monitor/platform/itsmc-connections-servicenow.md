---
title: Anslut ServiceNow med Anslutningsprogram för hantering av IT-tjänster (ITSM)
description: Den här artikeln innehåller information om hur du Anslutningsprogram för hantering av IT-tjänster (ITSM) ServiceNow (ITSMC) i Azure Monitor för att centralt övervaka och hantera ITSM arbets objekt.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/21/2020
ms.openlocfilehash: 662c36e4f0082c376a6e250e9a0885f0cd225964
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/22/2020
ms.locfileid: "97729724"
---
# <a name="connect-servicenow-with-it-service-management-connector"></a>Anslut ServiceNow med Anslutningsprogram för hantering av IT-tjänster (ITSM)

Den här artikeln innehåller information om hur du konfigurerar anslutningen mellan ServiceNow-instansen och Anslutningsprogram för hantering av IT-tjänster (ITSM) (ITSMC) i Log Analytics för att centralt hantera dina arbets uppgifter.

Följande avsnitt innehåller information om hur du ansluter din ServiceNow-produkt till ITSMC i Azure.

## <a name="prerequisites"></a>Förutsättningar
Se till att följande krav är uppfyllda:
- ITSMC installerad. Mer information: [Lägg till anslutningsprogram för hantering av IT-tjänster (ITSM)-lösningen](./itsmc-definition.md#add-it-service-management-connector).
- ServiceNow-versioner som stöds: Orlando, New York, Madrid, London, Kingston, Jakarta, Istanbul, Helsingfors, Genève.
- I dag kan aviseringarna som skickas från Azure Monitor skapa i ServiceNow något av följande element: händelser, incidenter eller aviseringar.
> [!NOTE]
> ITSMC stöder bara det officiella SaaS-erbjudandet från tjänsten nu. Privata distributioner av service stöds nu inte. 

**ServiceNow-administratörer måste göra följande i sin ServiceNow-instans**:
- Generera klient-ID och klient hemlighet för ServiceNow-produkten. Information om hur du genererar klient-ID och hemlighet finns i följande information som krävs:

    - [Konfigurera OAuth för Orlando](https://docs.servicenow.com/bundle/orlando-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Konfigurera OAuth för New York](https://docs.servicenow.com/bundle/newyork-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Konfigurera OAuth för Madrid](https://docs.servicenow.com/bundle/madrid-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Konfigurera OAuth för London](https://docs.servicenow.com/bundle/london-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Konfigurera OAuth för Kingston](https://docs.servicenow.com/bundle/kingston-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Konfigurera OAuth för Jakarta](https://docs.servicenow.com/bundle/jakarta-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Konfigurera OAuth för Istanbul](https://docs.servicenow.com/bundle/istanbul-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Konfigurera OAuth för Helsingfors](https://docs.servicenow.com/bundle/helsinki-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Konfigurera OAuth för Genève](https://docs.servicenow.com/bundle/geneva-servicenow-platform/page/administer/security/task/t_SettingUpOAuth.html)
> [!NOTE]
> Som en del av definitionen av "Konfigurera OAuth" rekommenderar vi att:
>
> 1) **Uppdatera uppdateringstoken livs längd till 90 dagar (7 776 000 sekunder):** Som en del av [inställningen konfigurera OAuth](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.servicenow.com%2Fbundle%2Fnewyork-platform-administration%2Fpage%2Fadminister%2Fsecurity%2Ftask%2Ft_SettingUpOAuth.html&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C2c6812e429a549e71cdd08d7d1b148d8%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637208431696739125&sdata=Q7mF6Ej8MCupKaEJpabTM56EDZ1T8vFVyihhoM594aA%3D&reserved=0) i fas 2: [skapa en slut punkt för klienter för att få åtkomst till instansen](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.servicenow.com%2Fbundle%2Fnewyork-platform-administration%2Fpage%2Fadminister%2Fsecurity%2Ftask%2Ft_CreateEndpointforExternalClients.html&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C2c6812e429a549e71cdd08d7d1b148d8%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637208431696749123&sdata=hoAJHJAFgUeszYCX1Q%2FXr4N%2FAKiFcm5WV7mwR2UqeWA%3D&reserved=0) efter definitionen av slut punkten, i ServiceNow-bladswitch för system OAuth än Välj program register. Välj namnet på den OAuth som definierats och uppdatera fältet för uppdateringstoken livs längd till 7 776 000 (90 dagar i sekunder).
> Klicka på Uppdatera i slutet.
> 2) **Vi rekommenderar att du upprättar en intern procedur för att se till att anslutningen är i livet:** Enligt livs längd för uppdatering av token. Se till att utföra följande åtgärder innan uppdateringstoken förväntas förfallo tid (ett par dagar innan uppdateringstoken livs längd upphör att gälla):
>
>     1. [Slutför en manuell synkroniseringsprocess för ITSM Connector-konfiguration](./itsmc-resync-servicenow.md)
>     2. Återkalla till den gamla uppdateringstoken eftersom det inte rekommenderas att behålla gamla nycklar från av säkerhets skäl. I ServiceNow blads sökning efter system-OAuth än Välj Hantera token. Välj den gamla token från listan enligt OAuth-namn och förfallo datum.
> ![OAuth-definition för vinter system](media/itsmc-connections/snow-system-oauth.png)
>     3. Klicka på återkalla åtkomst och än vid återkallning.

- Installera User-appen för Microsoft Log Analytics integration (ServiceNow-app). [Läs mer](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1 ).
> [!NOTE]
> ITSMC stöder endast den officiella användar appen för Microsoft Log Analytics-integrering som hämtas från ServiceNow Store. ITSMC stöder inte kod inmatning på ServiceNow-sidan eller programmet som inte ingår i den officiella ServiceNow-lösningen. 
- Skapa en integrerings användar roll för användar programmet som är installerat. Information om hur du skapar integrations användar rollen finns [här](#create-integration-user-role-in-servicenow-app).

## <a name="connection-procedure"></a>**Anslutnings procedur**
Använd följande procedur för att skapa en ServiceNow-anslutning:


1. I Azure Portal går du till **alla resurser** och letar efter **Servicedesk (YourWorkspaceName)**

2.  Under **arbets ytans data källor** klickar du på **ITSM-anslutningar**.
    ![Ny anslutning](media/itsmc-connections/add-new-itsm-connection.png)

3. Klicka på **Lägg till** längst upp i den högra rutan.

4. Ange informationen enligt beskrivningen i följande tabell och klicka på **OK** för att skapa anslutningen.


> [!NOTE]
> Alla dessa parametrar är obligatoriska.

| **Fält** | **Beskrivning** |
| --- | --- |
| **Anslutnings namn**   | Ange ett namn för den ServiceNow-instans som du vill ansluta till ITSMC.  Du använder det här namnet senare i Log Analytics när du konfigurerar arbets objekt i den här ITSM/Visa detaljerad logg analys. |
| **Partner typ**   | Välj **ServiceNow**. |
| **Användarnamn**   | Skriv det integrations användar namn som du skapade i ServiceNow-appen för att stödja anslutningen till ITSMC. Mer information: [skapa ServiceNow app User Role](#create-integration-user-role-in-servicenow-app).|
| **Lösenord**   | Ange lösen ordet som är kopplat till det här användar namnet. **Obs!** användar namn och lösen ord används enbart för att skapa autentiseringstoken och lagras inte var som helst i ITSMC-tjänsten.  |
| **Server-URL**   | Ange URL: en för den ServiceNow-instans som du vill ansluta till ITSMC. URL: en måste peka på en SaaS-version som stöds med suffixet ". servicenow.com".|
| **Klient-ID**   | Ange det klient-ID som du vill använda för OAuth2-autentisering, som du skapade tidigare.  Mer information om hur du genererar klient-ID och hemlighet:   [OAuth-installation](https://wiki.servicenow.com/index.php?title=OAuth_Setup). |
| **Klient hemlighet**   | Ange klient hemligheten som genererats för detta ID.   |
| **Omfång för data synkronisering**   | Välj de ServiceNow arbets objekt som du vill synkronisera till Azure Log Analytics via ITSMC.  De valda värdena importeras till Log Analytics.   **Alternativ:**  Incidenter och ändrings begär Anden.|
| **Synkronisera data** | Ange antalet senaste dagar som du vill att data ska visas. **Maxgräns**: 120 dagar. |
| **Skapa nytt konfigurations objekt i ITSM-lösningen** | Välj det här alternativet om du vill skapa konfigurations objekt i ITSM-produkten. När det här alternativet är markerat skapar ITSMC det påverkade CIs-objektet som konfigurations objekt (om det inte finns något befintligt CIs) i det ITSM system som stöds. **Standard**: inaktiverat. |

![ServiceNow-anslutning](media/itsmc-connections/itsm-connection-servicenow-connection-latest.png)

**När du har anslutit och synkroniserat**:

- De valda arbets objekten från ServiceNow-instansen importeras till Azure **Log Analytics.** Du kan visa sammanfattningen av dessa arbets uppgifter på Anslutningsprogram för hantering av IT-tjänster (ITSM)s panelen.

- Du kan skapa incidenter från Log Analytics aviseringar eller från logg poster eller från Azure-aviseringar i den här ServiceNow-instansen.

> [!NOTE]
> I ServiceNow finns en hastighets gräns för begär Anden per timme. För att kunna konfigurera begränsningen använder du detta genom att definiera "begränsning av inkommande REST API hastighet" i ServiceNow-instansen.

## <a name="create-integration-user-role-in-servicenow-app"></a>Skapa en användar roll för integrering i ServiceNow-appen

Användaren följer följande procedur:

1. Besök [ServiceNow Store](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1) och installera User- **appen för SERVICENOW och Microsoft OMS-integration** till ServiceNow-instansen.
   
   >[!NOTE]
   >Som en del av den pågående över gången från Microsoft Operations Management Suite (OMS) till Azure Monitor, kallas OMS nu för Log Analytics.     
2. Efter installationen går du till det vänstra navigerings fältet för ServiceNow-instansen, söker och väljer Microsoft OMS Integrator.  
3. Klicka på **installations check lista**.

   Statusen visas som  **inte slutförd** om användar rollen ännu inte har skapats.

4. I text rutorna bredvid **skapa integrations användare** anger du användar namnet för den användare som kan ansluta till ITSMC i Azure.
5. Ange lösen ordet för den här användaren och klicka på **OK**.  

> [!NOTE]
> Du använder dessa autentiseringsuppgifter för att skapa ServiceNow-anslutningen i Azure.

Den nyskapade användaren visas med de standard roller som tilldelats.

**Standard roller**:
- personalize_choices
- import_transformer
-   x_mioms_microsoft. user
-   ITIL
-   template_editor
-   view_changer

När användaren har skapats flyttas statusen för **kontrol lera installations check listan** till slutförd, som visar information om den användar roll som har skapats för appen.

> [!NOTE]
> ITSM-anslutningsprogram kan skicka incidenter till ServiceNow utan att några andra moduler är installerade på din ServiceNow-instans. Om du använder EventManagement-modulen i din ServiceNow-instans och vill skapa händelser eller varningar i ServiceNow med hjälp av anslutnings tjänsten lägger du till följande roller i integrations användaren:
> 
>    - evt_mgmt_integration
>    - evt_mgmt_operator  


## <a name="next-steps"></a>Nästa steg

* [Översikt över ITSM-anslutningsprogram](itsmc-overview.md)
* [Skapa ITSM arbets objekt från Azure-aviseringar](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts)
* [Felsöka problem med anslutningsprogram för hantering av IT-tjänster (ITSM)](./itsmc-resync-servicenow.md)