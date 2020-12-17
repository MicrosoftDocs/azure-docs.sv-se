---
title: Anslutningsprogram för hantering av IT-tjänster (ITSM) i Azure Monitor
description: Den här artikeln innehåller information om hur du ansluter dina ITSM-produkter/-tjänster med Anslutningsprogram för hantering av IT-tjänster (ITSM) (ITSMC) i Azure Monitor för att centralt övervaka och hantera ITSM arbets objekt.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/12/2020
ms.openlocfilehash: 86fd136cdf03c9bdd6bd1f610cccc339512f8fbc
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97657115"
---
# <a name="connect-itsm-productsservices-with-it-service-management-connector"></a>Ansluta ITSM-produkter/-tjänster med anslutningsprogrammet för hantering av IT-tjänster (ITSM)
Den här artikeln innehåller information om hur du konfigurerar anslutningen mellan din ITSM-produkt/-tjänst och Anslutningsprogram för hantering av IT-tjänster (ITSM) (ITSMC) i Log Analytics för att centralt hantera dina arbets uppgifter. Mer information om ITSMC finns i [Översikt](./itsmc-overview.md).

Följande ITSM-produkter/-tjänster stöds. Välj produkten om du vill visa detaljerad information om hur du ansluter produkten till ITSMC.

- [System Center Service Manager](#connect-system-center-service-manager-to-it-service-management-connector-in-azure)
- [ServiceNow](#connect-servicenow-to-it-service-management-connector-in-azure)
- [Styrka](#connect-provance-to-it-service-management-connector-in-azure)
- [Cherwell](#connect-cherwell-to-it-service-management-connector-in-azure)

> [!NOTE]
> 
> Vi föreslår våra Cherwell och förstyrker kunder att använda [webhook-åtgärder](./action-groups.md#webhook) för att Cherwell och bevisa slut punkt som en annan lösning på integrationen.

## <a name="connect-system-center-service-manager-to-it-service-management-connector-in-azure"></a>Ansluta System Center Service Manager till Anslutningsprogram för hantering av IT-tjänster (ITSM) i Azure

I följande avsnitt finns information om hur du ansluter System Center Service Manager-produkten till ITSMC i Azure.

### <a name="prerequisites"></a>Krav

Se till att följande krav är uppfyllda:

- ITSMC installerad. Mer information: [Lägg till anslutningsprogram för hantering av IT-tjänster (ITSM)-lösningen](./itsmc-definition.md).
- Service Manager webb program (webbapp) distribueras och konfigureras. Information om Web App finns [här](#create-and-deploy-service-manager-web-app-service).
- Hybrid anslutning har skapats och kon figurer ATS. Mer information: [Konfigurera hybrid anslutningen](#configure-the-hybrid-connection).
- Versioner av Service Manager som stöds: 2012 R2 eller 2016.
- Användar roll:  [Avancerad operatör](/previous-versions/system-center/service-manager-2010-sp1/ff461054(v=technet.10)).
- I dag kan aviseringarna som skickas från Azure Monitor skapas i System Center Service Manager incidenter.

> [!NOTE]
> 
> - ITSM-anslutningsprogram kan bara ansluta till molnbaserade ServiceNow-instanser. Lokala ServiceNow-instanser stöds inte för närvarande.
> - För att kunna använda anpassade [mallar](./itsmc-definition.md#template-definitions) som en del av de åtgärder som parametern "ProjectionType" i SCSM-mallen ska mappas till "IncidentManagement! System. WorkItem. incident. ProjectionType "

### <a name="connection-procedure"></a>Anslutnings procedur

Använd följande procedur för att ansluta din System Center Service Manager-instans till ITSMC:

1. I Azure Portal går du till **alla resurser** och letar efter **Servicedesk (YourWorkspaceName)**

2.  Under **arbets ytans data källor** klickar du på **ITSM-anslutningar**.

    ![Ny anslutning](media/itsmc-connections/add-new-itsm-connection.png)

3. Klicka på **Lägg till** längst upp i den högra rutan.

4. Ange informationen enligt beskrivningen i följande tabell och klicka på **OK** för att skapa anslutningen.

> [!NOTE]
> 
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

![Service Manager-anslutning](media/itsmc-connections/service-manager-connection.png)

**När du har anslutit och synkroniserat**:

- De valda arbets objekten från Service Manager importeras till Azure **Log Analytics.** Du kan visa sammanfattningen av dessa arbets uppgifter på Anslutningsprogram för hantering av IT-tjänster (ITSM)s panelen.

- Du kan skapa incidenter från Log Analytics aviseringar eller från logg poster eller från Azure-aviseringar i den här Service Manager-instansen.


Läs mer: [skapa ITSM arbets objekt från Azure-aviseringar](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts).

### <a name="create-and-deploy-service-manager-web-app-service"></a>Skapa och distribuera Service Manager Web App Service

För att ansluta den lokala Service Manager med ITSMC i Azure har Microsoft skapat en Service Manager webbapp på GitHub.

Gör så här för att konfigurera ITSM-webbappen för din Service Manager:

- **Distribuera webbappen** – distribuera webbappen, ange egenskaperna och autentisera med Azure AD. Du kan distribuera webbappen med hjälp av det [automatiserade skriptet](./itsmc-service-manager-script.md) som Microsoft tillhandahåller.
- **Konfigurera hybrid anslutningen**  -  [Konfigurera den här anslutningen](#configure-the-hybrid-connection), manuellt.

#### <a name="deploy-the-web-app"></a>Distribuera webbappen
Använd det automatiserade [skriptet](./itsmc-service-manager-script.md) för att distribuera webbappen, ange egenskaperna och autentisera med Azure AD.

Kör skriptet genom att ange följande obligatoriska information:

- Information om Azure-prenumeration
- Namn på resursgrupp
- Plats
- Service Manager Server information (Server namn, domän, användar namn och lösen ord)
- Prefix för webbplats namn för din webbapp
- Service Bus-namnrymd.

Skriptet skapar webbappen med det namn som du har angett (tillsammans med några ytterligare strängar för att göra den unik). Den genererar **webbappens webb adress**, **klient-ID** och **klient hemlighet**.

Spara värdena, du använder dem när du skapar en anslutning med ITSMC.

**Kontrol lera installationen av webbapp**

1. Gå till **Azure Portal**-  >  **resurser**.
2. Välj webbapp, klicka på **Inställningar**  >  **program inställningar**.
3. Bekräfta informationen om Service Manager-instansen som du angav när du distribuerade appen via skriptet.

### <a name="configure-the-hybrid-connection"></a>Konfigurera hybrid anslutningen

Använd följande procedur för att konfigurera hybrid anslutningen som ansluter Service Manager-instansen med ITSMC i Azure.

1. Hitta Service Manager-webbappen under **Azure-resurser**.
2. Klicka på **Inställningar**  >  **nätverk**.
3. Under **hybridanslutningar** klickar **du på Konfigurera hybrid anslutnings slut punkter**.

    ![Anslutning till hybrid anslutning](media/itsmc-connections/itsmc-hybrid-connection-networking-and-end-points.png)
4. Klicka på **Lägg till hybrid anslutning** på bladet **hybridanslutningar** .

    ![Hybrid anslutning, Lägg till](media/itsmc-connections/itsmc-new-hybrid-connection-add.png)

5. Klicka på **Skapa ny hybrid anslutning** i bladet **Lägg till hybridanslutningar** .

    ![Ny hybrid anslutning](media/itsmc-connections/itsmc-create-new-hybrid-connection.png)

6. Ange följande värden:

   - **Slut punkts namn**: Ange ett namn för den nya hybrid anslutningen.
   - **Slut punkts värd**: FQDN för service Managers hanterings servern.
   - **Slut punkts port**: typ 5724
   - **Service Bus-namnrymd**: Använd ett befintligt Service Bus-namnområde eller skapa ett nytt.
   - **Plats**: Välj platsen.
   - **Namn**: Ange ett namn på Service Bus om du skapar det.

     ![Hybrid anslutnings värden](media/itsmc-connections/itsmc-new-hybrid-connection-values.png)
6. Klicka på **OK** för att stänga bladet **skapa hybrid anslutning** och börja skapa hybrid anslutningen.

    När hybrid anslutningen har skapats visas den under bladet.

7. När hybrid anslutningen har skapats väljer du anslutningen och klickar på **Lägg till vald hybrid anslutning**.

    ![Ny hybrid anslutning](media/itsmc-connections/itsmc-new-hybrid-connection-added.png)

#### <a name="configure-the-listener-setup"></a>Konfigurera Listener-installationen

Använd följande procedur för att konfigurera Listener-installationen för Hybrid anslutningen.

1. I bladet **hybridanslutningar** klickar **du på Hämta anslutnings hanteraren** och installerar den på den dator där System Center Service Manager instansen körs.

    När installationen är klar finns **HYBRIDANSLUTNINGSHANTERAREN UI** -alternativet på **Start** menyn.

2. Klicka på **Hybridanslutningshanteraren användar gränssnitt** , så uppmanas du att ange dina autentiseringsuppgifter för Azure.

3. Logga in med dina Azure-autentiseringsuppgifter och välj din prenumeration där hybrid anslutningen skapades.

4. Klicka på **Spara**.

Din hybrid anslutning har upprättats.

![lyckad hybrid anslutning](media/itsmc-connections/itsmc-hybrid-connection-listener-set-up-successful.png)
> [!NOTE]
> 
> När hybrid anslutningen har skapats kontrollerar du och testar anslutningen genom att gå till den distribuerade Service Manager-webbappen. Se till att anslutningen lyckas innan du försöker ansluta till ITSMC i Azure.

Följande exempel bild visar information om en lyckad anslutning:

![Test av hybrid anslutning](media/itsmc-connections/itsmc-hybrid-connection-test.png)

## <a name="connect-servicenow-to-it-service-management-connector-in-azure"></a>Ansluta ServiceNow till Anslutningsprogram för hantering av IT-tjänster (ITSM) i Azure

Följande avsnitt innehåller information om hur du ansluter din ServiceNow-produkt till ITSMC i Azure.

### <a name="prerequisites"></a>Krav
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
> 1. [Slutför en manuell synkroniseringsprocess för ITSM Connector-konfiguration](./itsmc-resync-servicenow.md)
> 2. Återkalla till den gamla uppdateringstoken eftersom det inte rekommenderas att behålla gamla nycklar från av säkerhets skäl. I ServiceNow blads sökning efter system-OAuth än Välj Hantera token. Välj den gamla token från listan enligt OAuth-namn och förfallo datum.
> ![OAuth-definition för vinter system](media/itsmc-connections/snow-system-oauth.png)
> 3. Klicka på återkalla åtkomst och än vid återkallning.

- Installera User-appen för Microsoft Log Analytics integration (ServiceNow-app). [Läs mer](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1 ).
> [!NOTE]
> ITSMC stöder endast den officiella användar appen för Microsoft Log Analytics-integrering som hämtas från ServiceNow Store. ITSMC stöder inte kod inmatning på ServiceNow-sidan eller programmet som inte ingår i den officiella ServiceNow-lösningen. 
- Skapa en integrerings användar roll för användar programmet som är installerat. Information om hur du skapar integrations användar rollen finns [här](#create-integration-user-role-in-servicenow-app).

### <a name="connection-procedure"></a>**Anslutnings procedur**
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

Läs mer: [skapa ITSM arbets objekt från Azure-aviseringar](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts).


> [!NOTE]
> I ServiceNow finns en hastighets gräns för begär Anden per timme. För att kunna konfigurera begränsningen använder du detta genom att definiera "begränsning av inkommande REST API hastighet" i ServiceNow-instansen.

### <a name="create-integration-user-role-in-servicenow-app"></a>Skapa en användar roll för integrering i ServiceNow-appen

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
> 
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
> 
> ITSM-anslutningsprogram kan skicka incidenter till ServiceNow utan att några andra moduler är installerade på din ServiceNow-instans. Om du använder EventManagement-modulen i din ServiceNow-instans och vill skapa händelser eller varningar i ServiceNow med hjälp av anslutnings tjänsten lägger du till följande roller i integrations användaren:
> 
>    - evt_mgmt_integration
>    - evt_mgmt_operator  


## <a name="connect-provance-to-it-service-management-connector-in-azure"></a>Ansluta bevisa till Anslutningsprogram för hantering av IT-tjänster (ITSM) i Azure

Följande avsnitt innehåller information om hur du ansluter din bestyrkande produkt till ITSMC i Azure.

> [!NOTE]
> 
> Vi föreslår att våra kund styrke kunder använder [webhook-åtgärder](./action-groups.md#webhook) för att Cherwell och bevisa slut punkter som en annan lösning på integrationen.

### <a name="prerequisites"></a>Krav

Se till att följande krav är uppfyllda:


- ITSMC installerad. Mer information: [Lägg till anslutningsprogram för hantering av IT-tjänster (ITSM)-lösningen](./itsmc-definition.md#add-it-service-management-connector).
- Upphandlings appen bör registreras med Azure AD-och klient-ID är tillgängligt. Detaljerad information finns i [så här konfigurerar du Active Directory-autentisering](../../app-service/configure-authentication-provider-aad.md).

- Användar roll: administratör.

### <a name="connection-procedure"></a>Anslutnings procedur

Använd följande procedur för att skapa en upphandlings anslutning:

1. I Azure Portal går du till **alla resurser** och letar efter **Servicedesk (YourWorkspaceName)**

2.  Under **arbets ytans data källor** klickar du på **ITSM-anslutningar**.
    ![Ny anslutning](media/itsmc-connections/add-new-itsm-connection.png)

3. Klicka på **Lägg till** längst upp i den högra rutan.

4. Ange informationen enligt beskrivningen i följande tabell och klicka på **OK** för att skapa anslutningen.

> [!NOTE]
> 
> Alla dessa parametrar är obligatoriska.

| **Fält** | **Beskrivning** |
| --- | --- |
| **Anslutnings namn**   | Ange ett namn på den instans av upphandlingen som du vill ansluta till ITSMC.  Du använder det här namnet senare när du konfigurerar arbets objekt i den här ITSM/Visa detaljerad Log Analytics. |
| **Partner typ**   | Välj **styrka**. |
| **Användarnamn**   | Ange användar namnet som kan ansluta till ITSMC.    |
| **Lösenord**   | Ange lösen ordet som är kopplat till det här användar namnet. **Obs:** Användar namn och lösen ord används endast för att skapa autentiseringstoken och lagras inte var som helst inom ITSMC-service._|
| **Server-URL**   | Ange URL: en för din instans av instansen som du vill ansluta till ITSMC. |
| **Klient-ID**   | Ange klient-ID för att autentisera anslutningen, som du skapade i din upphandlings instans.  Mer information om klient-ID finns i [Konfigurera Active Directory-autentisering](../../app-service/configure-authentication-provider-aad.md). |
| **Omfång för data synkronisering**   | Välj de arbets objekt för styrka som du vill synkronisera till Azure Log Analytics via ITSMC.  Dessa arbets uppgifter importeras till Log Analytics.   **Alternativ:**   Incidenter, ändrings begär Anden.|
| **Synkronisera data** | Ange antalet senaste dagar som du vill att data ska visas. **Maxgräns**: 120 dagar. |
| **Skapa nytt konfigurations objekt i ITSM-lösningen** | Välj det här alternativet om du vill skapa konfigurations objekt i ITSM-produkten. När det här alternativet är markerat skapar ITSMC det påverkade CIs-objektet som konfigurations objekt (om det inte finns något befintligt CIs) i det ITSM system som stöds. **Standard**: inaktiverat.|

![Skärm bild som visar listorna anslutnings namn och partner typ.](media/itsmc-connections/itsm-connections-provance-latest.png)

**När du har anslutit och synkroniserat**:

- De valda arbets objekten från denna instans av bevisning importeras till Azure **Log Analytics.** Du kan visa sammanfattningen av dessa arbets uppgifter på Anslutningsprogram för hantering av IT-tjänster (ITSM)s panelen.

- Du kan skapa incidenter från Log Analytics aviseringar eller från logg poster eller från Azure-aviseringar i den här upphandlings instansen.

Läs mer: [skapa ITSM arbets objekt från Azure-aviseringar](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts).

## <a name="connect-cherwell-to-it-service-management-connector-in-azure"></a>Ansluta Cherwell till Anslutningsprogram för hantering av IT-tjänster (ITSM) i Azure

Följande avsnitt innehåller information om hur du ansluter din Cherwell-produkt till ITSMC i Azure.

> [!NOTE]
> 
> Vi föreslår våra Cherwell-kunder att använda [webhook-åtgärder](./action-groups.md#webhook) för att Cherwell och bevisa slut punkter som en annan lösning på integrationen.

### <a name="prerequisites"></a>Krav

Se till att följande krav är uppfyllda:

- ITSMC installerad. Mer information: [Lägg till anslutningsprogram för hantering av IT-tjänster (ITSM)-lösningen](./itsmc-definition.md#add-it-service-management-connector).
- Ett klient-ID genererades. Mer information: [generera klient-ID för Cherwell](#generate-client-id-for-cherwell).
- Användar roll: administratör.

### <a name="connection-procedure"></a>Anslutnings procedur

Använd följande procedur för att skapa en Cherwell-anslutning:

1. I Azure Portal går du till **alla resurser** och letar efter **Servicedesk (YourWorkspaceName)**

2.  Under **arbets ytans data källor** klickar du på **ITSM-anslutningar**.
    ![Ny anslutning](media/itsmc-connections/add-new-itsm-connection.png)

3. Klicka på **Lägg till** längst upp i den högra rutan.

4. Ange informationen enligt beskrivningen i följande tabell och klicka på **OK** för att skapa anslutningen.

> [!NOTE]
> 
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


![Upphandlings anslutning](media/itsmc-connections/itsm-connections-cherwell-latest.png)

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

[ITSM-anslutningsprogram översikt](itsmc-overview.md) [skapa ITSM arbets objekt från Azure Alerts](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts) 
 [Felsöka problem i ITSM-anslutningsprogram](./itsmc-resync-servicenow.md)