---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med ServiceNow | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och ServiceNow.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/19/2020
ms.author: jeedes
ms.openlocfilehash: 9748c772466db73fc49b1291ffe2ce7d13da1d8c
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88540243"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-servicenow"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med ServiceNow

I den här självstudien får du lära dig hur du integrerar ServiceNow med Azure Active Directory (Azure AD). När du integrerar ServiceNow med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till ServiceNow.
* Gör det möjligt för användarna att logga in automatiskt till ServiceNow med sina Azure AD-konton.
* Hantera dina konton på en central plats: Azure Portal.

Om du vill veta mer om SaaS (Software as a Service) med Azure AD, se [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* En ServiceNow-aktiverad (SSO)-prenumeration med enkel inloggning.
* För ServiceNow, en instans eller klient för ServiceNow, Calgary-version eller senare.
* För ServiceNow Express, en instans av ServiceNow Express, Helsingfors version eller senare.
* ServiceNow-klientorganisationen måste ha [Multiple Provider Single Sign On Plugin](https://wiki.servicenow.com/index.php?title=Multiple_Provider_Single_Sign-On#gsc.tab=0) (Plugin-program för enkel inloggning med flera providrar) aktiverat. Det kan du göra genom att [skicka en tjänstbegäran](https://hi.service-now.com).
* Om du vill ha automatisk konfiguration aktiverar du plugin-programmet för flera providrar för ServiceNow.
* Om du vill installera det klassiska ServiceNow-programmet (mobil) går du till lämpligt Arkiv och söker efter det klassiska ServiceNow-programmet. Hämta det sedan.

> [!NOTE]
> Den här integreringen är också tillgänglig för användning från Azure AD amerikanska myndigheters moln miljö. Du hittar det här programmet i Cloud App-galleriet för Azure AD amerikanska myndigheter och konfigurerar det på samma sätt som du gör från det offentliga molnet.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö. 

* ServiceNow stöder **SP** -initierad SSO.

* ServiceNow har stöd för [Automatisk användar etablering](servicenow-provisioning-tutorial.md).

* När du har konfigurerat ServiceNow kan du framtvinga sessionsnycklar som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Sessions kontroller utökas från villkorlig åtkomst. [Lär dig hur du tvingar fram sessions kontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

* Du kan konfigurera det klassiska ServiceNow-programmet (mobil) med Azure AD för att aktivera SSO. Det stöder både Android-och iOS-användare. I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

## <a name="add-servicenow-from-the-gallery"></a>Lägg till ServiceNow från galleriet

För att konfigurera integreringen av ServiceNow till Azure AD behöver du lägga till ServiceNow från galleriet till listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med ett arbets-eller skol konto eller genom att använda en personlig Microsoft-konto.
1. I den vänstra rutan väljer du tjänsten **Azure Active Directory** .
1. Gå till **företags program**och välj **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , ange **ServiceNow** i sökrutan.
1. Välj **ServiceNow** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-servicenow"></a>Konfigurera och testa enkel inloggning med Azure AD för ServiceNow

Konfigurera och testa Azure AD SSO med ServiceNow med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i ServiceNow.

Om du vill konfigurera och testa Azure AD SSO med ServiceNow, slutför du följande Bygg stenar:

1. [Konfigurera Azure AD SSO](#configure-azure-ad-sso) så att användarna kan använda den här funktionen.
    1. [Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user) för att testa enkel inloggning i Azure AD med B. Simon.
    1. [Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user) att aktivera B. Simon för att använda enkel inloggning i Azure AD.
    1. [Konfigurera Azure AD SSO för ServiceNow Express](#configure-azure-ad-sso-for-servicenow-express) så att användarna kan använda den här funktionen.
2. [Konfigurera ServiceNow](#configure-servicenow) för att konfigurera SSO-inställningar på program sidan.
    1. [Skapa en ServiceNow-testanvändare](#create-servicenow-test-user) som ska ha en motsvarighet till B. Simon i ServiceNow, som är länkad till användarens Azure AD-representation.
    1. [Konfigurera ServiceNow Express SSO](#configure-servicenow-express-sso) för att konfigurera inställningarna för enkel inloggning på program sidan.  
3. [Testa SSO](#test-sso) för att kontrol lera om konfigurationen fungerar.
4. [Testa SSO för ServiceNow Classic (mobil)](#test-sso-for-servicenow-classic-mobile) för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **ServiceNow** och letar upp avsnittet **Hantera** . Välj **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** väljer du Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Skärm bild som visar hur du konfigurerar enkel inloggning med SAML-sidan med Penn ikonen markerad](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** utför du följande steg:

    a. I **inloggnings-URL**anger du en URL som använder följande mönster: `https://instance.service-now.com/login_with_sso.do?glide_sso_id=<sys_id of the sso configuration>`

    b. I **identifierare (enhets-ID)** anger du en URL som använder följande mönster: `https://<instance-name>.service-now.com`

    c. Ange en av följande URL: er för **svars-URL**:

    | Svars-URL|
    |----------|
    | `https://instancename.service-now.com/navpage.do` |
    | `https://instancename.service-now.com/customer.do` | 

    > [!NOTE]
    > Dessa värden är inte verkliga. Du måste uppdatera dessa värden med den faktiska inloggnings-URL: en och identifieraren, som beskrivs senare i självstudien. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **certifikat (base64)**. 

   ![Skärm bild av avsnittet SAML signerings certifikat med hämtning markerat](common/certificatebase64.png)

   a. Välj kopierings knappen för att kopiera **URL för appens Federations-metadata**och klistra in den i anteckningar. URL-adressen kommer att användas senare i självstudien.

    b. Välj **Ladda ned** för att ladda ned **certifikat (base64)** och spara sedan certifikat filen på din dator.

1. I avsnittet **Konfigurera ServiceNow** kopierar du lämpliga URL: er baserat på ditt krav.

   ![Skärm bild av avsnittet Konfigurera ServiceNow med URL: er markerade](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare, som kallas B. Simon, i Azure Portal.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**  >  **användare**  >  **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. Som **namn**anger du `B.Simon` .  
   1. För **användar namn**anger du username@companydomain.extension . Till exempel `B.Simon@contoso.com`.
   1. Välj **Visa lösen ord**och skriv sedan ned värdet som visas i rutan **lösen ord** .
   1. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till ServiceNow.

1. I Azure Portal väljer du **företags program**  >  **alla program**.
1. I programlistan väljer du **ServiceNow**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Skärm bild av avsnittet hantera med användare och grupper markerade](common/users-groups-blade.png)

1. Välj **Lägg till användare**. I dialog rutan **Lägg till tilldelning** väljer **du användare och grupper**.

    ![Skärm bild av användare och grupper med Lägg till användare markerat](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och väljer sedan **Välj**.
1. Om du förväntar dig ett roll värde i SAML-intyget väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** . Välj sedan **Välj**.
1. I dialogrutan **Lägg till tilldelning** väljer du **Tilldela**.

### <a name="configure-azure-ad-sso-for-servicenow-express"></a>Konfigurera Azure AD SSO för ServiceNow Express

1. Välj **enkel inloggning**på sidan **ServiceNow** Application Integration i [Azure Portal](https://portal.azure.com/).

    ![Skärm bild av sidan ServiceNow Application Integration med enkel inloggning markerad](common/select-sso.png)

2. I dialog rutan **Välj metod för enkel inloggning** väljer du **SAML/WS-utfodras** läge för att aktivera enkel inloggning.

    ![Skärm bild av Välj en enkel inloggnings metod med SAML markerat](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** väljer du Penn ikonen för att öppna dialog rutan **grundläggande SAML-konfiguration** .

    ![Skärm bild som visar hur du konfigurerar enkel inloggning med SAML-sidan med Penn ikonen markerad](common/edit-urls.png)

4. I avsnittet **grundläggande SAML-konfiguration** utför du följande steg:

    a. För **inloggnings-URL**anger du en URL som använder följande mönster: `https://instance.service-now.com/login_with_sso.do?glide_sso_id=<sys_id of the sso configuration>`

    b. För **identifierare (enhets-ID)** anger du en URL som använder följande mönster: `https://<instance-name>.service-now.com`

    c. Ange en av följande URL: er för **svars-URL**:

    | Svars-URL |
    |-----------|
    | `https://instancename.service-now.com/navpage.do` |
    | `https://instancename.service-now.com/customer.do` |

    > [!NOTE]
    > Dessa värden är inte verkliga. Du måste uppdatera dessa värden med den faktiska inloggnings-URL: en och identifieraren, som beskrivs senare i självstudien. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , väljer du **Hämta** för att ladda ned **certifikatet (base64)** från de angivna alternativen enligt ditt krav. Spara den på din dator.

    ![Skärm bild av avsnittet SAML-signerings certifikat med hämtning markerat](common/certificatebase64.png)

6. Du kan låta Azure AD konfigurera ServiceNow automatiskt för SAML-baserad autentisering. Om du vill aktivera den här tjänsten går du till avsnittet **Konfigurera ServiceNow** och väljer **Visa steg-för-steg-instruktioner** för att öppna fönstret **Konfigurera inloggning** .

    ![Skärm bild av avsnittet Konfigurera ServiceNow med Visa steg-för-steg-instruktioner markerade](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

7. I formuläret **Konfigurera inloggning** anger du namn på ServiceNow-instans, admin-användarnamn och administratörs lösen ord. Välj **Konfigurera nu**. Det angivna administratörs användar namnet måste ha rollen **security_admin** som har tilldelats i ServiceNow för att detta ska fungera. Om du vill konfigurera ServiceNow manuellt för att använda Azure AD som en SAML Identity-Provider väljer du **Konfigurera enkel inloggning manuellt**. Kopiera URL: **en för utloggning, Azure AD-identifierare och inloggnings webb adress** från avsnittet snabb referens.

    ![Skärm bild av Konfigurera inloggnings formulär med Konfigurera nu markerat](./media/servicenow-tutorial/configure.png "Konfigurera App-URL")

## <a name="configure-servicenow"></a>Konfigurera ServiceNow

1. Logga in på ditt ServiceNow-program som administratör.

1. Aktivera plugin-programmet för **integration – flera providers med enkel inloggning** genom att följa dessa steg:

    a. I det vänstra fönstret söker du efter **system definitions** avsnittet i sökrutan och väljer sedan **plugin**-program.

    ![Skärm bild av avsnittet system definition med system definition och plugin-program markerat](./media/servicenow-tutorial/tutorial_servicenow_03.png "Aktivera plugin")

    b. Sök efter **integration – flera providers-installationsprogram för enkel inloggning**.

     ![Skärm bild av sidan system-plugin-program med integration – flera providers-installationsprogram för enkel inloggning har marker ATS](./media/servicenow-tutorial/tutorial_servicenow_04.png "Aktivera plugin")

    c. Välj plugin-programmet. Högerklicka och välj **Aktivera/uppgradera**.

     ![Skärm bild av snabb meny för plugin-programmet, med aktivera/uppgradera markerat](./media/servicenow-tutorial/tutorial_activate.png "Aktivera plugin")

    d. Välj **Aktivera**.

     ![Skärm bild av dialog rutan Aktivera plugin-program med aktivera markerat](./media/servicenow-tutorial/tutorial_activate1.png "Aktivera plugin")

1. I det vänstra fönstret söker du efter **SSO-avsnittet för flera providrar** från Sök fältet och väljer sedan **Egenskaper**.

    ![Skärm bild av SSO-avsnittet för flera providers med SSO och egenskaper för flera providrar markerade](./media/servicenow-tutorial/tutorial_servicenow_06.png "Konfigurera App-URL")

1. I dialog rutan **Egenskaper för flera providers SSO-egenskaper** utför du följande steg:

    ![Skärm bild av dialog rutan Egenskaper för flera providers SSO-egenskaper](./media/servicenow-tutorial/ic7694981.png "Konfigurera App-URL")

    * Välj **Ja**för **aktivera flera Provider SSO**.
  
    * Välj **Ja**om du vill **Aktivera automatisk import av användare från alla identitets leverantörer till användar tabellen**.

    * Välj **Ja**om **du vill aktivera fel söknings loggning för flera Provider SSO-integrering**.

    * För **fältet i tabellen användare som...**, anger du **e-post**.
  
    * Välj **Spara**.

1. Du kan konfigurera ServiceNow automatiskt eller manuellt. Följ dessa steg om du vill konfigurera ServiceNow automatiskt:

    1. Gå tillbaka till sidan för enkel inloggning med **ServiceNow** i Azure Portal.

    1. Konfigurera tjänst med ett klick finns för ServiceNow. Om du vill aktivera den här tjänsten går du till avsnittet **konfiguration av ServiceNow** och väljer **Konfigurera ServiceNow** för att öppna fönstret **Konfigurera inloggning** .

        ![Skärm bild av konfiguration av ServiceNow med Visa stegvisa instruktioner markerade](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

    1. I formuläret **Konfigurera inloggning** anger du namn på ServiceNow-instans, admin-användarnamn och administratörs lösen ord. Välj **Konfigurera nu**. Det angivna administratörs användar namnet måste ha rollen **security_admin** som har tilldelats i ServiceNow för att detta ska fungera. Om du vill konfigurera ServiceNow manuellt för att använda Azure AD som en SAML Identity-Provider väljer du **Konfigurera enkel inloggning manuellt**. Kopiera URL: en för **utloggning, SAML Entity ID och SAML Single Sign-on** från avsnittet snabb referens.

        ![Skärm bild av Konfigurera inloggnings formulär med Konfigurera nu markerat](./media/servicenow-tutorial/configure.png "Konfigurera App-URL")

    1. Logga in på ditt ServiceNow-program som administratör.

       * I den automatiska konfigurationen konfigureras alla nödvändiga inställningar på **ServiceNow** -sidan, men **X. 509-certifikatet** är inte aktiverat som standard. Du måste mappa den manuellt till din identitets leverantör i ServiceNow. Följ de här stegen:

         1. I det vänstra fönstret söker du efter **SSO-avsnittet för flera providers** från sökrutan och väljer **identitets leverantörer**.

            ![Skärm bild av SSO-avsnittet för flera providers med identitets leverantörer markerade](./media/servicenow-tutorial/tutorial_servicenow_07.png "Konfigurera enkel inloggning")

         1. Välj den automatiskt genererade identitets leverantören.

            ![Skärm bild av identitets leverantörer, med automatiskt genererad identitets leverantör markerad](./media/servicenow-tutorial/tutorial_servicenow_08.png "Konfigurera enkel inloggning")

         1.  I avsnittet **Identity Provider** (Identitetsprovider) utför du följande steg:

             ![Skärm bild av avsnittet identitets leverantör](./media/servicenow-tutorial/automatic_config.png "Konfigurera enkel inloggning")

               * I **namn**anger du ett namn för din konfiguration (till exempel **Microsoft Azure federerad enkel inloggning**).

               * Ta bort den ifyllda **SingleLogoutRequest** -värdet från text rutan.

               * Kopiera värdet för **Start sidan för ServiceNow** och klistra in det i **inloggnings-URL: en** i avsnittet **ServiceNow Basic SAML-konfiguration** i Azure Portal.

                  > [!NOTE]
                  > Startsidan för ServiceNow-instansen är en sammanlänkning av din **URL för ServiceNow-klientorganisation** och **/navpage.do** (till exempel: `https://fabrikam.service-now.com/navpage.do`).

              * Kopiera **enhets-ID/Issuer-** värdet och klistra in det i **identifierare** i avsnittet **ServiceNow Basic SAML-konfiguration** i Azure Portal.

              * Bekräfta att **NameID-principen** är inställd på `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` Value. 

         1. Rulla ned till avsnittet **X. 509-certifikat** och välj **Redigera**.

             ![Skärm bild av avsnittet X. 509-certifikat med redigera markerat](./media/servicenow-tutorial/tutorial_servicenow_09.png "Konfigurera enkel inloggning")

         1. Välj certifikatet och välj högerpilen för att lägga till certifikatet

            ![Skärm bild av samlingen med certifikat och HÖGERPIL ikon markerad](./media/servicenow-tutorial/tutorial_servicenow_11.png "Konfigurera enkel inloggning")

          1. Välj **Spara**.

          1. I det övre högra hörnet på sidan väljer du **Testa anslutning**.

             ![Skärm bild av sidan med test anslutning markerat](./media/servicenow-tutorial/tutorial_activate2.png "Aktivera plugin")

             > [!NOTE]
             > Om test anslutningen Miss lyckas och du inte kan aktivera den här anslutningen, kommer ServiceNow att erbjuda override-växeln. Du måste ange **Sys_properties. LISTA** i **Sök navigeringen** så öppnas den nya sidan med system egenskaper. Här måste du skapa en ny egenskap med namnet som **Glide. autentisera. multisso. test. Connection. obligatoriskt** med **data typen** **True/false** och ange **värdet** som **falskt**.

             > ![Skärm bild av Testresultat sida](./media/servicenow-tutorial/testconnection-fail.png "Konfigurera enkel inloggning")
        
          1. När du uppmanas att ange dina autentiseringsuppgifter anger du dem. Du ser följande sida. **SSO-Utloggningen testresultat** ett fel förväntas. Ignorera felet och välj  **Aktivera**.

             ![Skärm bild av Testresultat sida](./media/servicenow-tutorial/servicenowactivate.png "Konfigurera enkel inloggning")
  
1. Följ dessa steg om du vill konfigurera **ServiceNow** manuellt:

    1. Logga in på ditt ServiceNow-program som administratör.

    1. Välj **identitets leverantörer**i det vänstra fönstret.

        ![Skärm bild av SSO med flera providers, med identitets leverantörer markerade](./media/servicenow-tutorial/tutorial_servicenow_07.png "Konfigurera enkel inloggning")

    1. I dialog rutan **identitets leverantörer** väljer du **nytt**.

        ![Skärm bild av dialog rutan identitets leverantörer med ny markerad](./media/servicenow-tutorial/ic7694977.png "Konfigurera enkel inloggning")

    1. I dialog rutan **identitets leverantörer** väljer du **SAML**.

        ![Skärm bild av dialog rutan identitets leverantörer med SAML markerat](./media/servicenow-tutorial/ic7694978.png "Konfigurera enkel inloggning")

    1. I **Importera identitetsprovider metadata**, utför följande steg:

        ![Skärm bild av metadata för import Identity Provider, med URL och importera markerat](./media/servicenow-tutorial/idp.png "Konfigurera enkel inloggning")

        1. Ange **URL: en för den app Federation-Metadata** som du har kopierat från Azure Portal.

        1. Välj **Importera**.

    1. Den läser URL: en för IdP metadata och fyller all fält information.

        ![Skärm bild av identitets leverantör](./media/servicenow-tutorial/ic7694982.png "Konfigurera enkel inloggning")

        * I **namn**anger du ett namn för din konfiguration (till exempel **Microsoft Azure federerad enkel inloggning**).

        * Ta bort den fyllda **identitets leverantörens SingleLogoutRequest** -värde från text rutan.

        * Kopiera värdet för **Start sidan för ServiceNow** . Klistra in den i **inloggnings-URL: en** i avsnittet **ServiceNow Basic SAML Configuration** i Azure Portal.

            > [!NOTE]
            > Startsidan för ServiceNow-instansen är en sammanlänkning av din **URL för ServiceNow-klientorganisation** och **/navpage.do** (till exempel: `https://fabrikam.service-now.com/navpage.do`).

        * Kopiera **enhets-ID/Issuer-** värdet. Klistra in den i **identifierare** i avsnittet **ServiceNow Basic SAML Configuration** i Azure Portal.

        * Bekräfta att **NameID-principen** är inställd på `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` Value.

        * Välj **Avancerat**. I **fältet användare**anger du **e-post**.

            > [!NOTE]
            > Du kan konfigurera Azure AD att generera antingen Azure AD-användarnamnet (User Principal Name) eller e-postadressen som unik identifierare i SAML-token. Det gör du genom att gå till avsnittet **ServiceNow**  >  **Attributes**  >  **-attribut för enkel inloggning** i Azure Portal och mappa det önskade fältet till attributet **NameIdentifier** . Värdet som lagras för det valda attributet i Azure AD (till exempel User Principal Name) måste matcha det värde som lagras i ServiceNow för det angivna fältet (till exempel user_name).

        * Välj **test anslutning** i det övre högra hörnet på sidan.

          > [!NOTE]
          > Om test anslutningen Miss lyckas och du inte kan aktivera den här anslutningen, kommer ServiceNow att erbjuda override-växeln. Du måste ange **Sys_properties. LISTA** i **Sök navigeringen** så öppnas den nya sidan med system egenskaper. Här måste du skapa en ny egenskap med namnet som **Glide. autentisera. multisso. test. Connection. obligatoriskt** med **data typen** **True/false** och ange **värdet** som **falskt**.

          > ![Skärm bild av Testresultat sida](./media/servicenow-tutorial/testconnection-fail.png "Konfigurera enkel inloggning")

        * När du uppmanas att ange dina autentiseringsuppgifter anger du dem. Du ser följande sida. **SSO-Utloggningen testresultat** ett fel förväntas. Ignorera felet och välj  **Aktivera**.

          ![Skärm bild av Testresultat sida](./media/servicenow-tutorial/servicenowactivate.png "Konfigurera enkel inloggning")

### <a name="create-servicenow-test-user"></a>Skapa ServiceNow-testanvändare

Syftet med det här avsnittet är att skapa en användare som kallas B. Simon i ServiceNow. ServiceNow stöder automatisk användar etablering, som är aktiverat som standard.

> [!NOTE]
> Om du behöver skapa en användare manuellt kontaktar du ServiceNow- [klientens support team](https://www.servicenow.com/support/contact-support.html).

### <a name="configure-servicenow-express-sso"></a>Konfigurera ServiceNow Express SSO

1. Logga in på ditt ServiceNow Express-program som administratör.

2. I det vänstra fönstret väljer du **enkel inloggning**.

    ![Skärm bild av ServiceNow Express-program med enkel inloggning markerat](./media/servicenow-tutorial/ic7694980ex.png "Konfigurera App-URL")

3. I dialog rutan **enkel inloggning** väljer du konfigurations ikonen längst upp till höger och anger följande egenskaper:

    ![Skärm bild av dialog rutan enkel inloggning](./media/servicenow-tutorial/ic7694981ex.png "Konfigurera App-URL")

    a. Växla **Enable multiple provider SSO** (Aktivera enkel inloggning med flera providrar) till höger.

    b. Växla **Enable debug logging for the multiple provider SSO integration** (Aktivera felsökningsloggning för integreringen av enkel inloggning med flera providrar) till höger.

    c. I **fältet i tabellen användare som...**, anger du **user_name**.

4. I dialog rutan **enkel inloggning** väljer du **Lägg till nytt certifikat**.

    ![Skärm bild av dialog rutan enkel inloggning med Lägg till nytt certifikat markerat](./media/servicenow-tutorial/ic7694973ex.png "Konfigurera enkel inloggning")

5. I dialog rutan **X. 509-certifikat** utför du följande steg:

    ![Skärm bild av dialog rutan X. 509-certifikat](./media/servicenow-tutorial/ic7694975.png "Konfigurera enkel inloggning")

    a. I **namn**anger du ett namn för din konfiguration (till exempel: **testsaml 2.0**).

    b. Välj **Active** (Aktiv).

    c. För **format**väljer du **PEM**.

    d. I **typ**väljer du **Trust Store-certifikat**.

    e. Öppna det Base64-kodade certifikatet som du laddade ned från Azure Portal i anteckningar. Kopiera innehållet i den till Urklipp och klistra in det i text rutan PEM- **certifikat** .

    f. Välj **Uppdatera**

6. I dialog rutan **enkel inloggning** väljer du **Lägg till ny IDP**.

    ![Skärm bild av dialog rutan enkel inloggning där Lägg till ny IdP är markerat](./media/servicenow-tutorial/ic7694976ex.png "Konfigurera enkel inloggning")

7. I dialog rutan **Lägg till ny identitetsprovider** , under **Konfigurera identitetsprovider**, utför du följande steg:

    ![Skärm bild av dialog rutan Lägg till ny identitetsprovider](./media/servicenow-tutorial/ic7694982ex.png "Konfigurera enkel inloggning")

    a. I **namn**anger du ett namn för din konfiguration (till exempel: **SAML 2,0**).

    b. För **URL för identitetsprovider**klistrar du in värdet för ID för identitets leverantören som du kopierade från Azure Portal.

    c. För **identitets leverantörens AuthnRequest**klistrar du in värdet för URL: en för autentiseringsbegäran som du kopierade från Azure Portal.

    d. För **identitets leverantörens SingleLogoutRequest**klistrar du in värdet för den utloggnings-URL som du kopierade från Azure Portal.

    e. För **Identity Provider-certifikat**väljer du det certifikat som du skapade i föregående steg.

8. Välj **Avancerade inställningar**. Under **Ytterligare egenskaper för identitets leverantör**utför du följande steg:

    ![Skärm bild av dialog rutan Lägg till ny identitets leverantör med avancerade inställningar markerade](./media/servicenow-tutorial/ic7694983ex.png "Konfigurera enkel inloggning")

    a. För **protokoll bindning för IDP-SingleLogoutRequest anger du** **urn: Oasis: Names: TC: SAML: 2.0: bindningar: http-omdirigering**.

    b. För **NameID-princip**anger du **urn: Oasis: Names: TC: SAML: 1.1: NameID-format: ospecificerad**.

    c. För **metoden AuthnContextClassRef**anger du `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password` .

    d. För att **skapa en AuthnContextClass**växlar du till av (omarkerat).

9. Under **Additional Service Provider Properties** (Ytterligare egenskaper för tjänstleverantör) utför du följande steg:

    ![Skärm bild av dialog rutan Lägg till ny identitetsprovider med olika egenskaper markerade](./media/servicenow-tutorial/ic7694984ex.png "Konfigurera enkel inloggning")

    a. För **ServiceNow start sida**anger du URL: en för din ServiceNow instance-startsida.

    > [!NOTE]
    > Startsidan för ServiceNow-instansen är en sammanlänkning av din **URL för ServiceNow-klientorganisation** och **/navpage.do** (till exempel: `https://fabrikam.service-now.com/navpage.do`).

    b. För **entitets-ID/utfärdare**anger du URL: en för din ServiceNow-klient.

    c. För **Målgrupps-URI**anger du URL: en för din ServiceNow-klient.

    d. För **klock skevning**anger du **60**.

    e. I **fältet användare**anger du **e-post**.

    > [!NOTE]
    > Du kan konfigurera Azure AD att generera antingen Azure AD-användarnamnet (User Principal Name) eller e-postadressen som unik identifierare i SAML-token. Det gör du genom att gå till avsnittet **ServiceNow**  >  **Attributes**  >  **-attribut för enkel inloggning** i Azure Portal och mappa det önskade fältet till attributet **NameIdentifier** . Värdet som lagras för det valda attributet i Azure AD (till exempel User Principal Name) måste matcha det värde som lagras i ServiceNow för det angivna fältet (till exempel user_name).

    f. Välj **Spara**.

## <a name="test-sso"></a>Testa SSO

När du väljer panelen ServiceNow på åtkomst panelen, bör du loggas in automatiskt på den ServiceNow som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="test-sso-for-servicenow-classic-mobile"></a>Testa SSO för ServiceNow Classic (mobil)

1. Öppna det **klassiska ServiceNow-programmet (mobil)** och utför följande steg:

    a. Välj plus tecknet i det nedre högra hörnet.

    ![Skärm bild av ServiceNow-klassiskt program med plus tecken markerat](./media/servicenow-tutorial/test03.png)

    b. Ange namnet på ServiceNow-instansen och välj **Fortsätt**.

    ![Skärm bild av sidan Lägg till instans med Fortsätt markerat](./media/servicenow-tutorial/test04.png)

    c. Utför följande steg på sidan **Logga** in:

    ![Skärm bild av inloggnings sidan med Använd extern inloggning markerad](./media/servicenow-tutorial/test01.png)

    *  Ange **användar namn**, t B.simon@contoso.com . ex..

    *  Välj **Använd extern inloggning**. Du omdirigeras till Azure AD-sidan för inloggning.

    *  Ange autentiseringsuppgifter. Om det finns någon tredjeparts autentisering eller någon annan säkerhetsfunktion som är aktive rad måste användaren svara på detta. **Start sidan** för programmet visas.

        ![Skärm bild av programmets start sida](./media/servicenow-tutorial/test02.png)

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Konfigurera användarförsörjning](servicenow-provisioning-tutorial.md)

- [Prova ServiceNow med Azure AD](https://aad.portal.azure.com)

- [Vad är session Control i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/protect-servicenow)

- [Så här skyddar du ServiceNow med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
