---
title: 'Självstudie: konfigurera SAP SuccessFactors tillbakaskrivning i Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar attributet Skriv-tillbaka till SAP SuccessFactors från Azure AD
services: active-directory
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.topic: tutorial
ms.workload: identity
ms.date: 10/14/2020
ms.author: chmutali
ms.openlocfilehash: 3260787dec4ae26cd6ef7cc3bd562f39db8e3655
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99526983"
---
# <a name="tutorial-configure-attribute-write-back-from-azure-ad-to-sap-successfactors"></a>Självstudie: konfigurera attributet Skriv-back från Azure AD till SAP SuccessFactors
Syftet med den här självstudien är att Visa stegen för att skriva tillbaka attribut från Azure AD till SAP SuccessFactors personal Central. 

## <a name="overview"></a>Översikt

Du kan konfigurera SAP SuccessFactors tillbakaskrivning-appen för att skriva vissa attribut från Azure Active Directory till SAP SuccessFactors personal Central. SuccessFactors tillbakaskrivning-appen stöder tilldelning av värden till följande anställdas centrala attribut:

* E-postadress till arbetet
* Användarnamn
* Telefonnummer till företaget (inklusive landskod, rikt nummer, nummer och tillägg)
* Primär flagga för företags telefonnummer
* Mobiltelefon nummer (inklusive landskod, rikt nummer, nummer)
* Primär flagga för mobil telefon 
* Användarens custom01-Custom15-attribut
* loginMethod-attribut

> [!NOTE]
> Den här appen har inget beroende av integrerings programmen för SuccessFactors inkommande användare. Du kan konfigurera den oberoende av [SuccessFactors till lokal AD](sap-successfactors-inbound-provisioning-tutorial.md) -etablering av appar eller [SUCCESSFACTORS till Azure AD](sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) Provisioning-appen.

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Vem är den här användar etablerings lösningen som passar bäst för?

Den här SuccessFactors-lösningen för tillbakaskrivning av användare passar utmärkt för:

* Organisationer som använder Microsoft 365 som vill skriva tillbaka auktoritativa attribut som hanteras av IT (till exempel e-postadress, telefonnummer, användar namn) tillbaka till SuccessFactors personal Central.

## <a name="configuring-successfactors-for-the-integration"></a>Konfigurera SuccessFactors för integrering

Alla SuccessFactors etablerings anslutningar kräver autentiseringsuppgifter för ett SuccessFactors-konto med rätt behörighet för att anropa de anställdas centrala OData-API: erna. I det här avsnittet beskrivs steg för att skapa tjänst kontot i SuccessFactors och bevilja lämpliga behörigheter. 

* [Skapa/identifiera API-användarkonto i SuccessFactors](#createidentify-api-user-account-in-successfactors)
* [Skapa en roll för API-behörigheter](#create-an-api-permissions-role)
* [Skapa en behörighets grupp för API-användaren](#create-a-permission-group-for-the-api-user)
* [Bevilja behörighets rollen till behörighets gruppen](#grant-permission-role-to-the-permission-group)

### <a name="createidentify-api-user-account-in-successfactors"></a>Skapa/identifiera API-användarkonto i SuccessFactors
Arbeta med ditt SuccessFactors-administratörs team eller implementerings partner för att skapa eller identifiera ett användar konto i SuccessFactors som ska användas för att anropa OData-API: erna. Autentiseringsuppgifterna för användar namn och lösen ord för det här kontot kommer att krävas när du konfigurerar etablerings appar i Azure AD. 

### <a name="create-an-api-permissions-role"></a>Skapa en roll för API-behörigheter

1. Logga in på SAP SuccessFactors med ett användar konto som har åtkomst till administrations centret.
1. Sök efter *Hantera behörighets roller* och välj **Hantera behörighets roller** från Sök resultaten.

   ![Hantera behörighets roller](./media/sap-successfactors-inbound-provisioning/manage-permission-roles.png)

1. I listan behörighets roll klickar du på **Skapa ny**.

   > [!div class="mx-imgBorder"]
   > ![Skapa ny behörighets roll](./media/sap-successfactors-inbound-provisioning/create-new-permission-role-1.png)

1. Lägg till ett **roll namn** och en **Beskrivning** för den nya behörighets rollen. Namnet och beskrivningen ska ange att rollen är för API-användnings behörigheter.

   > [!div class="mx-imgBorder"]
   > ![Information om behörighets roll](./media/sap-successfactors-inbound-provisioning/permission-role-detail.png)

1. Under behörighets inställningar klickar du på **behörighet...** och bläddrar sedan ned behörighets listan och klickar på **Hantera integrerings verktyg**. Markera kryss rutan om **du vill tillåta administratörs åtkomst till OData-API via grundläggande autentisering**.

   > [!div class="mx-imgBorder"]
   > ![Hantera integrerings verktyg](./media/sap-successfactors-inbound-provisioning/manage-integration-tools.png)

1. Rulla nedåt i samma ruta och välj **medarbetares centrala API**. Lägg till behörigheter som visas nedan för att läsa med ODATA API och redigera med ODATA API. Välj alternativet Redigera om du planerar att använda samma konto för Skriv-tillbaka till SuccessFactors-scenariot. 

   > [!div class="mx-imgBorder"]
   > ![Läs Skriv behörighet](./media/sap-successfactors-inbound-provisioning/odata-read-write-perm.png)

1. Klicka på **färdig**. Klicka på **Spara ändringar**.

### <a name="create-a-permission-group-for-the-api-user"></a>Skapa en behörighets grupp för API-användaren

1. I SuccessFactors administrations Center söker du efter *Hantera behörighets grupper* och väljer **Hantera behörighets grupper** från Sök resultaten.

   > [!div class="mx-imgBorder"]
   > ![Hantera behörighets grupper](./media/sap-successfactors-inbound-provisioning/manage-permission-groups.png)

1. I fönstret Hantera behörighets grupper klickar du på **Skapa nytt**.

   > [!div class="mx-imgBorder"]
   > ![Lägg till ny grupp](./media/sap-successfactors-inbound-provisioning/create-new-group.png)

1. Lägg till ett grupp namn för den nya gruppen. Grupp namnet ska indikera att gruppen är för API-användare.

   > [!div class="mx-imgBorder"]
   > ![Namn på behörighets grupp](./media/sap-successfactors-inbound-provisioning/permission-group-name.png)

1. Lägg till medlemmar i gruppen. Du kan till exempel välja **användar namn** i list rutan personer i poolen och sedan ange användar namnet för det API-konto som ska användas för integreringen. 

   > [!div class="mx-imgBorder"]
   > ![Lägga till gruppmedlemmar](./media/sap-successfactors-inbound-provisioning/add-group-members.png)

1. Slutför skapandet av behörighets gruppen genom att klicka på **klar** .

### <a name="grant-permission-role-to-the-permission-group"></a>Bevilja behörighets rollen till behörighets gruppen

1. I SuccessFactors administrations Center kan du söka efter *Hantera behörighets roller* och sedan välja **Hantera behörighets roller** från Sök resultaten.
1. I **listan behörighets roll** väljer du den roll som du har skapat för behörigheter för API-användning.
1. Under **tilldela den här rollen till... klickar du** på **Lägg till...** -knapp.
1. Välj **behörighets grupp...** från den nedrullningsbara menyn och klicka sedan på **Välj...** för att öppna fönstret grupper för att söka efter och välja den grupp som skapades ovan. 

   > [!div class="mx-imgBorder"]
   > ![Lägg till behörighets grupp](./media/sap-successfactors-inbound-provisioning/add-permission-group.png)

1. Granska behörighets rollen bevilja behörighets gruppen. 
   > [!div class="mx-imgBorder"]
   > ![Behörighets roll och grupp information](./media/sap-successfactors-inbound-provisioning/permission-role-group.png)

1. Klicka på **Spara ändringar**.

## <a name="preparing-for-successfactors-writeback"></a>Förbereder för tillbakaskrivning av SuccessFactors

SuccessFactors tillbakaskrivning Provisioning-appen använder vissa *kod* värden för att ställa in e-post och telefonnummer i personal Central. Dessa *kod* värden anges som konstanta värden i tabellen Attribute-mappning och skiljer sig åt för varje SuccessFactors-instans. Det här avsnittet innehåller anvisningar för att samla in dessa *kod* värden.

   > [!NOTE]
   > Använd SuccessFactors-administratören för att slutföra stegen i det här avsnittet. 

### <a name="identify-email-and-phone-number-picklist-names"></a>Identifiera namn på list rutor för e-post och telefonnummer 

I SAP SuccessFactors är en *listruta* en konfigurerbar uppsättning alternativ som en användare kan välja mellan. De olika typerna av e-post och telefonnummer (t. ex. företag, personliga, andra) representeras med hjälp av en listruta. I det här steget kommer vi att identifiera de List rutor som kon figurer ATS i din SuccessFactors-klient för att lagra värden för e-post och telefonnummer. 
 
1. I SuccessFactors administrations Center kan du söka efter *Hantera företags konfiguration*. 

   > [!div class="mx-imgBorder"]
   > ![Hantera företags konfiguration](./media/sap-successfactors-inbound-provisioning/manage-business-config.png)

1. Under **HRIS element** väljer du **emailInfo** och klickar på *informationen* för fältet **e-posttyp** .

   > [!div class="mx-imgBorder"]
   > ![Hämta e-postinformation](./media/sap-successfactors-inbound-provisioning/get-email-info.png)

1. På sidan information om **e-posttyp** noterar du namnet på list rutan som är kopplad till det här fältet. Som standard är det **ecEmailType**. Det kan dock vara annorlunda i din klient organisation. 

   > [!div class="mx-imgBorder"]
   > ![Identifiera List rutan för e-post](./media/sap-successfactors-inbound-provisioning/identify-email-picklist.png)

1. Under **HRIS element** väljer du **phoneInfo** och klickar på *informationen* för fältet **telefon typ** .

   > [!div class="mx-imgBorder"]
   > ![Hämta telefonnummer information](./media/sap-successfactors-inbound-provisioning/get-phone-info.png)

1. På sidan information om **telefonnummer anger** du namnet på list rutan som är kopplad till det här fältet. Som standard är det **ecPhoneType**. Det kan dock vara annorlunda i din klient organisation. 

   > [!div class="mx-imgBorder"]
   > ![Identifiera telefon List rutan](./media/sap-successfactors-inbound-provisioning/identify-phone-picklist.png)

### <a name="retrieve-constant-value-for-emailtype"></a>Hämta konstant värde för emailType

1. I SuccessFactors administrations Center söker du och öppnar *plock lista Center*. 
1. Använd namnet på den e-postlista som du hämtade från föregående avsnitt (t. ex. ecEmailType) för att hitta e-postlistan. 

   > [!div class="mx-imgBorder"]
   > ![Hitta List rutan för e-posttyp](./media/sap-successfactors-inbound-provisioning/find-email-type-picklist.png)

1. Öppna listan över aktiva e-postmeddelanden. 

   > [!div class="mx-imgBorder"]
   > ![Öppna listan aktiv e-posttyp](./media/sap-successfactors-inbound-provisioning/open-active-email-type-picklist.png)

1. På sidan listruta för e-posttyp väljer du typ av *affärs* meddelande.

   > [!div class="mx-imgBorder"]
   > ![Välj typ av företags e-post](./media/sap-successfactors-inbound-provisioning/select-business-email-type.png)

1. Anteckna det **alternativ-ID** som är kopplat till *Business* -e-postmeddelandet. Det här är den kod som vi ska använda med *emailType* i tabellen Attribute-mappning.

   > [!div class="mx-imgBorder"]
   > ![Hämta kod för e-posttyp](./media/sap-successfactors-inbound-provisioning/get-email-type-code.png)

   > [!NOTE]
   > Ta bort kommatecknet när du kopierar över värdet. T. ex. om värdet för **alternativ-ID** är *8 448* anger du *emailType* i Azure AD till det konstanta talet *8448* (utan kommatecknet). 

### <a name="retrieve-constant-value-for-phonetype"></a>Hämta konstant värde för phoneType

1. I SuccessFactors administrations Center söker du och öppnar *plock lista Center*. 
1. Använd namnet på telefon listan som du hämtade från föregående avsnitt för att hitta telefon listan. 

   > [!div class="mx-imgBorder"]
   > ![Hitta telefon typs List rutan](./media/sap-successfactors-inbound-provisioning/find-phone-type-picklist.png)

1. Öppna listan över aktiva telefoner. 

   > [!div class="mx-imgBorder"]
   > ![Öppna listan över aktiva telefon typer](./media/sap-successfactors-inbound-provisioning/open-active-phone-type-picklist.png)

1. På sidan telefon typs lista granskar du de olika telefon typerna som visas under **list Rute värden**.

   > [!div class="mx-imgBorder"]
   > ![Granska telefon typer](./media/sap-successfactors-inbound-provisioning/review-phone-types.png)

1. Anteckna det **alternativ-ID** som är kopplat till *företags* telefonen. Det här är den kod som vi ska använda med *businessPhoneType* i tabellen Attribute-mappning.

   > [!div class="mx-imgBorder"]
   > ![Hämta företags telefon kod](./media/sap-successfactors-inbound-provisioning/get-business-phone-code.png)

1. Anteckna det **alternativ-ID** som är associerat med *mobil* telefonen. Det här är den kod som vi ska använda med *cellPhoneType* i tabellen Attribute-mappning.

   > [!div class="mx-imgBorder"]
   > ![Hämta mobil telefon kod](./media/sap-successfactors-inbound-provisioning/get-cell-phone-code.png)

   > [!NOTE]
   > Ta bort kommatecknet när du kopierar över värdet. T. ex. om värdet för **alternativ-ID** är *10 606* anger du *cellPhoneType* i Azure AD till det konstanta talet *10606* (utan kommatecknet). 


## <a name="configuring-successfactors-writeback-app"></a>Konfigurera SuccessFactors tillbakaskrivning-appen

Det här avsnittet innehåller steg för 

* [Lägg till etablerings anslutnings programmet och konfigurera anslutningen till SuccessFactors](#part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors)
* [Konfigurera mappningar för attribut](#part-2-configure-attribute-mappings)
* [Aktivera och starta användar etablering](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors"></a>Del 1: Lägg till etablerings anslutnings programmet och konfigurera anslutningen till SuccessFactors

**Så här konfigurerar du tillbakaskrivning av SuccessFactors:**

1. Gå till <https://portal.azure.com>

2. I det vänstra navigerings fältet väljer du **Azure Active Directory**

3. Välj **företags program** och sedan **alla program**.

4. Välj **Lägg till ett program** och välj kategorin **alla** .

5. Sök efter **tillbakaskrivning av SuccessFactors** och Lägg till den appen från galleriet.

6. När appen har lagts till och skärmen information om appen visas väljer du **etablering**

7. Ändra **etablerings** **läget** till **automatiskt**

8. Slutför avsnittet **admin credentials** enligt följande:

   * **Administratörens användar namn** – Ange användar namnet för SuccessFactors-API: t med det företags-ID som lagts till. Formatet: **användar namn \@ companyID**

   * **Administratörs lösen ord –** Ange lösen ordet för SuccessFactors-API-användarkontot. 

   * **Klient-URL –** Ange namnet på SuccessFactors OData API Services-slutpunkten. Ange bara värd namnet för servern utan http eller https. Det här värdet bör se ut så här: `api4.successfactors.com` .

   * **E-postavisering –** Ange din e-postadress och markera kryss rutan "skicka e-post om fel inträffar".
    > [!NOTE]
    > Azure AD Provisioning-tjänsten skickar e-postavisering om etablerings jobbet hamnar i [karantän](../app-provisioning/application-provisioning-quarantine-status.md) .

   * Klicka på knappen **Testa anslutning** . Om anslutnings testet lyckas, klickar du på knappen **Spara** längst upp. Om det Miss lyckas kontrollerar du att autentiseringsuppgifterna och URL: en för SuccessFactors är giltiga.
    >[!div class="mx-imgBorder"]
    >![Azure-portalen](./media/sap-successfactors-inbound-provisioning/sfwb-provisioning-creds.png)

   * När autentiseringsuppgifterna har sparats visas standard mappningen i avsnittet **mappningar** . Uppdatera sidan, om mappningarna för attribut inte visas.  

### <a name="part-2-configure-attribute-mappings"></a>Del 2: Konfigurera mappningar för attribut

I det här avsnittet ska du konfigurera hur användar data flödar från SuccessFactors till Active Directory.

1. På fliken etablering under **mappningar** klickar du på **etablera Azure Active Directory användare**.

1. I fältet **käll objekt omfånget** kan du välja vilka uppsättningar av användare i Azure AD som ska användas för att skriva tillbaka, genom att definiera en uppsättning attributbaserade filter. Standard omfånget är "alla användare i Azure AD". 
   > [!TIP]
   > När du konfigurerar etablerings appen för första gången måste du testa och verifiera dina mappningar och uttryck för att kontrol lera att det ger önskat resultat. Microsoft rekommenderar att du använder omfångs filter under **käll objekt omfånget** för att testa dina mappningar med några test användare från Azure AD. När du har kontrollerat att mappningarna fungerar kan du antingen ta bort filtret eller gradvis expandera det så att det innehåller fler användare.

1. **Åtgärds fältet för mål objekt** stöder bara **uppdaterings** åtgärden.

1. I tabellen mappning under **attribut mappningar** kan du mappa följande Azure Active Directory attribut till SuccessFactors. Tabellen nedan innehåller vägledning om hur du mappar attribut för att skriva tillbaka. 

   | \# | Azure AD-attribut | SuccessFactors-attribut | Kommentarer |
   |--|--|--|--|
   | 1 | Anställnings | personIdExternal | Som standard är det här attributet matchnings-ID. I stället för Anställningsnr kan du använda andra Azure AD-attribut som kan lagra värdet som är lika med personIdExternal i SuccessFactors.    |
   | 2 | e-post | e-post | Mappa e-postattributets källa. I test syfte kan du mappa userPrincipalName till e-post. |
   | 3 | 8448 | emailType | Det här konstanta värdet är det SuccessFactors-ID-värde som är kopplat till Business email. Uppdatera det här värdet för att matcha din SuccessFactors-miljö. I avsnittet [Hämta konstant värde för emailType](#retrieve-constant-value-for-emailtype) finns anvisningar för hur du anger det här värdet. |
   | 4 | true | emailIsPrimary | Använd det här attributet för att ställa in företags-e-post som primär i SuccessFactors. Om e-postmeddelandet inte är primärt, anger du den här flaggan till falsk. |
   | 5 | userPrincipalName | [custom01 – custom15] | Med **Lägg till ny mappning** kan du välja att skriva userPrincipalName eller något Azure AD-attribut till ett anpassat attribut som är tillgängligt i SuccessFactors-användarobjektet.  |
   | 6 | På lokal SamAccountName | användarnamn | Med **Lägg till ny mappning** kan du välja att mappa lokalt sAMAccountName till SuccessFactors username-attribut. Använd [Azure AD Connect Sync: Katalog tillägg](../hybrid/how-to-connect-sync-feature-directory-extensions.md) för att synkronisera sAMAccountName till Azure AD. Den visas i list rutan källa som *extension_yourTenantGUID_samAccountName* |
   | 7 | Enkel inloggning | loginMethod | Om SuccessFactors-klienten har kon figurer ATS för [partiell SSO](https://apps.support.sap.com/sap/support/knowledge/en/2320766)och använder Lägg till ny mappning, kan du ange loginMethod till ett konstant värde "SSO" eller "PWD". |
   | 8 | telephoneNumber | businessPhoneNumber | Använd den här mappningen för att flöda *telephoneNumber* från Azure AD till SuccessFactors Business/Work-telefonnumret. |
   | 9 | 10605 | businessPhoneType | Det här konstanta värdet är det SuccessFactors-ID-värde som är kopplat till företags telefonen. Uppdatera det här värdet för att matcha din SuccessFactors-miljö. I avsnittet [Hämta konstant värde för phoneType](#retrieve-constant-value-for-phonetype) finns anvisningar för hur du anger det här värdet. |
   | 10 | true | businessPhoneIsPrimary | Använd det här attributet för att ange den primära flaggan för företags telefonnummer. Giltiga värden är true eller false. |
   | 11 | mobil | cellPhoneNumber | Använd den här mappningen för att flöda *telephoneNumber* från Azure AD till SuccessFactors Business/Work-telefonnumret. |
   | 12 | 10606 | cellPhoneType | Det här konstanta värdet är det SuccessFactors-ID-värde som är kopplat till mobil telefonen. Uppdatera det här värdet för att matcha din SuccessFactors-miljö. I avsnittet [Hämta konstant värde för phoneType](#retrieve-constant-value-for-phonetype) finns anvisningar för hur du anger det här värdet. |
   | 13 | falskt | cellPhoneIsPrimary | Använd det här attributet för att ange den primära flaggan för mobiltelefon numret. Giltiga värden är true eller false. |
 
1. Verifiera och granska dina mappningar av attribut. 
 
    >[!div class="mx-imgBorder"]
    >![Mappning av tillbakaskrivning av attribut](./media/sap-successfactors-inbound-provisioning/writeback-attribute-mapping.png)

1. Klicka på **Spara** för att spara mappningarna. Nu ska vi uppdatera API-uttrycken JSON Path så att de använder phoneType-koderna i din SuccessFactors-instans. 
1. Välj **visa avancerade alternativ**. 

    >[!div class="mx-imgBorder"]
    >![Visa avancerade alternativ](./media/sap-successfactors-inbound-provisioning/show-advanced-options.png)

1. Klicka på **Redigera attributlistan för SuccessFactors**. 

   > [!NOTE] 
   > Om alternativet **Redigera attributlistan för SuccessFactors** inte visas i Azure Portal använder du URL: en *https://portal.azure.com/?Microsoft_AAD_IAM_forceSchemaEditorEnabled=true* för att komma åt sidan. 

1. I kolumnen **API-uttryck** i den här vyn visas de JSON-sökvägar som används av anslutningen. 
1. Uppdatera JSON Path-uttryck för företags telefon och mobil telefon för att använda ID-värdet (*businessPhoneType* och *cellPhoneType*) som motsvarar din miljö. 

    >[!div class="mx-imgBorder"]
    >![Ändra sökväg till telefon-JSON](./media/sap-successfactors-inbound-provisioning/phone-json-path-change.png)

1. Klicka på **Spara** för att spara mappningarna.

## <a name="enable-and-launch-user-provisioning"></a>Aktivera och starta användar etablering

När SuccessFactors-konfigurationen har slutförts kan du aktivera etablerings tjänsten i Azure Portal.

> [!TIP]
> Som standard när du aktiverar etablerings tjänsten kommer den att initiera etablerings åtgärder för alla användare i omfånget. Om det uppstår fel i mappnings-eller data problemen kan etablerings jobbet Miss Miss kan och gå in i karantäns tillstånd. För att undvika detta rekommenderar vi att du konfigurerar **käll objekt omfångs** filter och testar dina mappningar av attribut med några test användare innan du startar den fullständiga synkroniseringen för alla användare. När du har kontrollerat att mappningarna fungerar och ger dig önskade resultat kan du antingen ta bort filtret eller gradvis expandera det så att det innehåller fler användare.

1. På fliken **etablering** ställer du in **etablerings status** på **på**.

1. Välj **Omfattning**. Du kan välja något av följande alternativ: 
   * **Synkronisera alla användare och grupper**: Välj det här alternativet om du planerar att skriva tillbaka mappade attribut för alla användare från Azure AD till SuccessFactors, enligt de definitions regler som definierats under **mappningar**  ->  **käll objekt omfånget**. 
   * **Synkronisera endast tilldelade användare och grupper**: Välj det här alternativet om du planerar att skriva tillbaka mappade attribut för endast användare som du har tilldelat till det här programmet i  ->    ->  meny alternativet hantera **användare och grupper** i programmet. Dessa användare omfattas också av de definitions regler som definierats under **mappningar**  ->  **käll objekt omfånget**.

   > [!div class="mx-imgBorder"]
   > ![Välj intervall för tillbakaskrivning](./media/sap-successfactors-inbound-provisioning/select-writeback-scope.png)

   > [!NOTE]
   > SuccessFactors tillbakaskrivning-appen stöder inte grupp tilldelning. Endast användar tilldelning stöds. 

1. Klicka på **Spara**.

1. Den här åtgärden startar den inledande synkroniseringen, vilket kan ta ett variabelt antal timmar beroende på hur många användare som finns i Azure AD-klienten och omfattningen som definierats för åtgärden. Du kan kontrol lera förlopps indikatorn för att följa synkroniseringens förlopp. 

1. Gå till fliken **etablerings loggar** i Azure Portal för att se vilka åtgärder som etablerings tjänsten har utfört. I etablerings loggarna visas alla enskilda synkroniseringsfel som utförs av etablerings tjänsten. 

1. När den inledande synkroniseringen har slutförts skrivs en gransknings sammanfattnings rapport på fliken **etablering** , som visas nedan.

   > [!div class="mx-imgBorder"]
   > ![Förlopps indikator för etablering](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="supported-scenarios-known-issues-and-limitations"></a>Scenarier som stöds, kända problem och begränsningar

Se avsnittet för [tillbakaskrivning av scenarier](../app-provisioning/sap-successfactors-integration-reference.md#writeback-scenarios) i referens hand boken för SAP SuccessFactors-integration. 

## <a name="next-steps"></a>Nästa steg

* [Djupgående att gå in i integrerings referens för Azure AD och SAP SuccessFactors](../app-provisioning/sap-successfactors-integration-reference.md)
* [Lär dig att granska loggar och hämta rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)
* [Lär dig hur du konfigurerar enkel inloggning mellan SuccessFactors och Azure Active Directory](successfactors-tutorial.md)
* [Lär dig hur du integrerar andra SaaS-program med Azure Active Directory](tutorial-list.md)
* [Lär dig hur du exporterar och importerar dina etablerings konfigurationer](../app-provisioning/export-import-provisioning-configuration.md)