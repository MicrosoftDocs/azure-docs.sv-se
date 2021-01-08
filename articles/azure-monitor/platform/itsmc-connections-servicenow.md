---
title: Anslut ServiceNow med Anslutningsprogram för hantering av IT-tjänster (ITSM)
description: Lär dig hur du ansluter ServiceNow med Anslutningsprogram för hantering av IT-tjänster (ITSM) (ITSMC) i Azure Monitor för att centralt övervaka och hantera ITSM arbets objekt.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/21/2020
ms.openlocfilehash: 7d1b4b3542f6914d413a5e29e57baa15e7a53346
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98012792"
---
# <a name="connect-servicenow-with-it-service-management-connector"></a>Anslut ServiceNow med Anslutningsprogram för hantering av IT-tjänster (ITSM)

Den här artikeln visar hur du konfigurerar anslutningen mellan en ServiceNow-instans och Anslutningsprogram för hantering av IT-tjänster (ITSM) (ITSMC) i Log Analytics, så att du kan hantera dina IT-arbetsobjekt i IT-hanteringen centralt.

## <a name="prerequisites"></a>Förutsättningar
Se till att du uppfyller följande krav för anslutningen.

### <a name="itsmc-installation"></a>ITSMC-installation

Information om hur du installerar ITSMC finns i [Lägg till anslutningsprogram för hantering av IT-tjänster (ITSM) lösning](./itsmc-definition.md#add-it-service-management-connector).

> [!NOTE]
> ITSMC stöder endast officiella program vara som en tjänst (SaaS) som erbjuds från ServiceNow. Privata distributioner av ServiceNow stöds inte.

### <a name="oauth-setup"></a>OAuth-installation

ServiceNow-versioner som stöds är Orlando, New York, Madrid, London, Kingston, Jakarta, Istanbul, Helsingfors och Genève.

ServiceNow-administratörer måste generera ett klient-ID och klient hemlighet för sin ServiceNow-instans. Se följande information efter behov:

- [Konfigurera OAuth för Orlando](https://docs.servicenow.com/bundle/orlando-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Konfigurera OAuth för New York](https://docs.servicenow.com/bundle/newyork-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Konfigurera OAuth för Madrid](https://docs.servicenow.com/bundle/madrid-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Konfigurera OAuth för London](https://docs.servicenow.com/bundle/london-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Konfigurera OAuth för Kingston](https://docs.servicenow.com/bundle/kingston-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Konfigurera OAuth för Jakarta](https://docs.servicenow.com/bundle/jakarta-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Konfigurera OAuth för Istanbul](https://docs.servicenow.com/bundle/istanbul-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Konfigurera OAuth för Helsingfors](https://docs.servicenow.com/bundle/helsinki-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Konfigurera OAuth för Genève](https://docs.servicenow.com/bundle/geneva-servicenow-platform/page/administer/security/task/t_SettingUpOAuth.html)

Som en del av konfigurationen av OAuth rekommenderar vi att:

1. [Skapa en slut punkt för klienter för att få åtkomst till instansen](https://docs.servicenow.com/bundle/newyork-platform-administration/page/administer/security/task/t_CreateEndpointforExternalClients.html).

1. Uppdatera livs längd för uppdateringstoken:

   1. I fönstret **ServiceNow** söker du efter **system OAuth** och väljer sedan **program register**. 
   1. Välj namnet på den OAuth som definierats och ändra **uppdateringstoken livs längd** till **7 776 000 sekunder** (90 dagar). 
   1. Välj **Uppdatera**. 

1. Upprätta en intern procedur för att säkerställa att anslutningen förblir aktiv. Ett par dagar innan livs längd förväntas, utför följande åtgärder:

   1. [Slutför en manuell synkroniseringsprocess för ITSM Connector-konfigurationen](./itsmc-resync-servicenow.md).

   1. Återkalla till den gamla uppdateringstoken. Vi rekommenderar inte att du behåller gamla nycklar av säkerhets skäl. 
   
      1. I fönstret **ServiceNow** söker du efter **system OAuth** och väljer sedan **Hantera token**. 
      
      1. Välj den gamla token från listan enligt OAuth-namn och förfallo datum.

         ![Skärm bild som visar en lista över token för OAuth.](media/itsmc-connections/snow-system-oauth.png)
      1. Välj **återkalla åtkomst till återkalla åtkomst**  >  .

## <a name="install-the-user-app-and-create-the-user-role"></a>Installera appen användare och skapa användar rollen

Använd följande procedur för att installera tjänsten nu-app och skapa en användar roll för integration. Du använder dessa autentiseringsuppgifter för att skapa ServiceNow-anslutningen i Azure.

> [!NOTE]
> ITSMC stöder bara den officiella användar appen för Microsoft Log Analytics-integrering som hämtas från ServiceNow Store. ITSMC stöder inte kod inmatning på ServiceNow-sidan eller något program som inte ingår i den officiella ServiceNow-lösningen. 

1. Besök [ServiceNow Store](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1) och installera **user app for SERVICENOW and Microsoft OMS integration** i ServiceNow-instansen.
   
   >[!NOTE]
   >Som en del av den pågående över gången från Microsoft Operations Management Suite (OMS) till Azure Monitor, kallas OMS nu för Log Analytics.     
2. Efter installationen går du till det vänstra navigerings fältet i ServiceNow-instansen och söker sedan efter och väljer **Microsoft OMS Integrator**.  
3. Välj **installations check lista**.

   Statusen visas som  **inte slutförd** eftersom användar rollen inte har skapats ännu.

4. I text rutan bredvid **skapa integrations användare** anger du namnet på den användare som kan ansluta till ITSMC i Azure.
5. Ange lösen ordet för den här användaren och välj sedan **OK**.  

Den nyskapade användaren visas med standard rollerna som tilldelats:

- personalize_choices
- import_transformer
- x_mioms_microsoft. user
- ITIL
- template_editor
- view_changer

När du har skapat användaren flyttas statusen för **kontrol lera installations check listan** till **slutförd** och visar information om den användar roll som har skapats för appen.

> [!NOTE]
> ITSMC kan skicka incidenter till ServiceNow utan att några andra moduler är installerade på ServiceNow-instansen. Om du använder modulen EventManagement i ServiceNow-instansen och vill skapa händelser eller varningar i ServiceNow med hjälp av anslutnings programmet lägger du till följande roller i integrations användaren:
> 
> - evt_mgmt_integration
> - evt_mgmt_operator  

## <a name="create-a-connection"></a>Skapa en anslutning
Använd följande procedur för att skapa en ServiceNow-anslutning.

> [!NOTE]
> Aviseringarna som skickas från Azure Monitor kan skapa ett av följande element i ServiceNow: händelser, incidenter eller aviseringar. 

1. I Azure Portal går du till **alla resurser** och letar efter **Servicedesk (YourWorkspaceName)**.

2. Under **arbets ytans data källor** väljer du **ITSM-anslutningar**.

   ![Skärm bild som visar markering av en data källa.](media/itsmc-connections/add-new-itsm-connection.png)

3. Klicka på **Lägg till** längst upp i den högra rutan.

4. Ange informationen enligt beskrivningen i följande tabell och välj sedan **OK**.

   | **Fält** | **Beskrivning** |
   | --- | --- |
   | **Anslutnings namn**   | Ange ett namn för den ServiceNow-instans som du vill ansluta till ITSMC. Du använder det här namnet senare i Log Analytics när du konfigurerar ITSM arbets objekt och Visa detaljerad analys. |
   | **Partner typ**   | Välj **ServiceNow**. |
   | **Server-URL**   | Ange URL: en för den ServiceNow-instans som du vill ansluta till ITSMC. URL: en måste peka på en SaaS-version som stöds med suffixet *. ServiceNow.com*.|
   | **Användarnamn**   | Ange det integrerings användar namn som du skapade i ServiceNow-appen för att stödja anslutningen till ITSMC.|
   | **Lösenord**   | Ange lösen ordet som är kopplat till det här användar namnet. **Obs!** användar namn och lösen ord används endast för att skapa autentiseringstoken. De lagras inte var som helst i ITSMC-tjänsten.  |
   | **Klient-ID**   | Ange det klient-ID som du vill använda för OAuth2-autentisering, som du skapade tidigare. Mer information om hur du genererar ett klient-ID och en hemlighet finns i [Konfigurera OAuth](https://wiki.servicenow.com/index.php?title=OAuth_Setup). |
   | **Klient hemlighet**   | Ange den klient hemlighet som genererats för detta ID.   |
   | **Omfång för data synkronisering (i dagar)** | Ange antalet senaste dagar som du vill att data ska visas. Gränsen är 120 dagar. |
   | **Arbets objekt som ska synkroniseras**   | Välj de ServiceNow arbets objekt som du vill synkronisera till Azure Log Analytics via ITSMC. De valda värdena importeras till Log Analytics. Alternativen är incidenter och ändrings begär Anden.|
   | **Skapa nytt konfigurations objekt i ITSM-produkten** | Välj det här alternativet om du vill skapa konfigurations objekt i ITSM-produkten. När den är markerad skapar ITSMC konfigurations objekt (om inget finns) i det ITSM system som stöds. Den är inaktive rad som standard. |

![Skärm bild av rutor och alternativ för att lägga till en ServiceNow-anslutning.](media/itsmc-connections/itsm-connection-servicenow-connection-latest.png)

När du har anslutit och synkroniserat:

- De valda arbets objekten från ServiceNow-instansen importeras till Log Analytics. Du kan visa sammanfattningen av dessa arbets uppgifter på **anslutningsprogram för hantering av IT-tjänster (ITSM)s** panelen.

- Du kan skapa incidenter från Log Analytics aviseringar eller logg poster, eller från Azure-aviseringar i den här ServiceNow-instansen.

> [!NOTE]
> ServiceNow har en hastighets gräns för begär Anden per timme. Om du vill konfigurera gränsen definierar du begränsningen för **inkommande REST API hastighet** i ServiceNow-instansen.

## <a name="next-steps"></a>Nästa steg

* [Översikt över ITSM-anslutningsprogram](itsmc-overview.md)
* [Skapa ITSM arbets objekt från Azure-aviseringar](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts)
* [Felsöka problem i ITSM-anslutningsprogram](./itsmc-resync-servicenow.md)