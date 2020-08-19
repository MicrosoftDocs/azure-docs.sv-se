---
title: 'Självstudie: Azure Active Directory integrering med Palo-nätverk – admin UI | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Palo Alto Networks – Admin UI.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/12/2020
ms.author: jeedes
ms.openlocfilehash: 8bd41034d6d4cfa444ae4c0711fd46cb2924d009
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88554102"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---admin-ui"></a>Självstudie: Azure Active Directory integrering med Palo-nätverk – admin UI

I den här självstudien får du lära dig hur du integrerar Palo Alto Networks – Admin UI med Azure Active Directory (Azure AD).
Integreringen av Palo Alto Networks – Admin UI med Azure AD medför följande fördelar:

* Du kan styra vem som har åtkomst till Palo Alto Networks – Admin UI från Azure AD.
* Du kan konfigurera inställningar så att dina användare automatiskt loggas in i Palo Alto Networks – Admin UI (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Palo Alto Networks – Admin UI behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* En prenumeration på Palo Alto Networks – Admin UI med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Palo Alto Networks – Admin UI stöder **SP**-initierad enkel inloggning
* Palo Alto Networks – Admin UI stöder **JIT**-etablering (Just In Time)

## <a name="adding-palo-alto-networks---admin-ui-from-the-gallery"></a>Lägga till Palo Alto Networks – Admin UI från galleriet

För att konfigurera integreringen av Palo Alto Networks – Admin UI med Azure AD måste du lägga till Palo Alto Networks – Admin UI från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du **Palo-nätverk-admin UI** i sökrutan.
1. Välj **Palo-nätverk – admin-gränssnittet** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning i Azure AD med Palo-nätverk-admin-användar gränssnitt baserat på en test användare som kallas **B. Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Palo Alto Networks – Admin UI upprättas.

För att konfigurera och testa enkel inloggning i Azure AD med Palo Alto Networks – Admin UI måste du utföra följande uppgifter:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    * **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera Palo-nätverk – admin UI SSO](#configure-palo-alto-networks---admin-ui-sso)** – om du vill konfigurera inställningar för enkel inloggning på program sidan.
    * **[Skapa Palo-nätverk – administratörs användar gränssnitt test användare](#create-palo-alto-networks---admin-ui-test-user)** -om du vill ha en motsvarighet till B. Simon i Palo-nätverk – admin-användar gränssnitt som är länkat till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

För att konfigurera enkel inloggning i Azure AD med Palo Alto Networks – Admin UI måste du utföra följande steg:

1. Välj **Enkel inloggning** på sidan för programintegrering av **Palo Alto Networks – Admin UI** på [Azure-portalen](https://portal.azure.com/).

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

1. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    a. I text rutan **inloggnings-URL** skriver du en URL med följande mönster: `https://<Customer Firewall FQDN>/php/login.php`

    b. I rutan **identifierare** anger du en URL med följande mönster: `https://<Customer Firewall FQDN>:443/SAML20/SP`

    c. Ange ACS-URL:en (Assertion Consumer Service) i textrutan **Svars-URL** i följande format: `https://<Customer Firewall FQDN>:443/SAML20/SP/ACS`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska inloggnings-URL:en, identifieraren och svars-URL:en. Kontakta [Palo Alto Networks – Admin UI-kundsupporten](https://support.paloaltonetworks.com/support) och be om dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.
    >
    > Port 443 krävs på **identifieraren** och **svars-URL: en** eftersom dessa värden är hårdkodad i Palo-brand väggen. Om du tar bort port numret uppstår ett fel under inloggningen om det tas bort.

    > Port 443 krävs på **identifieraren** och **svars-URL: en** eftersom dessa värden är hårdkodad i Palo-brand väggen. Om du tar bort port numret uppstår ett fel under inloggningen om det tas bort.

1. Palo-nätverk-admin UI-appen förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar för attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

   > [!NOTE]
   > Eftersom attributvärdena bara är exempel mappar du relevanta värden för *username* och *adminrole*. Det finns ett annat valfritt attribut, *accessdomain*, som används för att begränsa administratörsåtkomst till specifika virtuella datorer i brandväggen.

1. Utöver ovan förväntas fler attribut skickas tillbaka i SAML-svar som visas nedan i Palo--nätverk-admin UI-programmet. Dessa attribut är också förifyllda, men du kan granska dem enligt dina krav.

    | Name |  Källattribut|
    | --- | --- |
    | användarnamn | user.userprincipalname |
    | adminrole | customadmin |
    | | |

    > [!NOTE]
    > Mer information om attributen finns i följande artiklar:
    > * [Profil för administrativ roll för Admin UI (adminrole)](https://www.paloaltonetworks.com/documentation/80/pan-os/pan-os/firewall-administration/manage-firewall-administrators/configure-an-admin-role-profile)
    > * [Enhetsåtkomstdomän för Admin UI (accessdomain)](https://docs.paloaltonetworks.com/pan-os/8-0/pan-os-web-interface-help/device/device-access-domain.html)

1. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. I avsnittet **Konfigurera Palo Alto Networks – Admin UI** kopierar du lämpliga URL:er baserat på dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare**och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Palo-nätverk-admin-ANVÄNDARGRÄNSSNITTET.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. Välj **Palo Alto Networks – Admin UI** i programlistan.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

### <a name="configure-palo-alto-networks---admin-ui-sso"></a>Konfigurera Palo-nätverk – admin UI SSO

1. Öppna Palo Alto Networks-brandväggens administratörsgränssnitt som administratör i ett nytt fönster.

2. Välj fliken **Enhet**.

    ![Fliken Enhet](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin1.png)

3. Välj **SAML-identitetsprovider** och välj sedan **Importera** för att importera metadatafilen.

    ![Knapp för att importera metadatafil](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin2.png)

4. Gör följande i fönstret **Importera serverprofil för SAML-identitetsprovider**:

    ![Fönstret Importera serverprofil för SAML-identitetsprovider](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp.png)

    a. Ange ett namn (t.ex. **AzureAD Admin UI**) i rutan **Profilnamn**.

    b. Välj **Bläddra** under **Metadata för identitetsprovider** och välj metadata.xml-filen som du hämtade tidigare från Azure-portalen.

    c. Avmarkera kryssrutan för att **verifiera certifikat för identitetsprovider**.

    d. Välj **OK**.

    e. Välj **Genomför** för att tillämpa konfigurationen på brandväggen.

5. Välj **SAML-identitetsprovider** i den vänstra rutan och välj sedan profilen för SAML-identitetsprovidern (t.ex. **AzureAD Admin UI**) som du skapade i föregående steg.

    ![Profil för SAML-identitetsprovider](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp_select.png)

6. Gör följande i fönstret **Serverprofil för SAML-identitetsprovider**:

    ![Fönstret Serverprofil för SAML-identitetsprovider](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_slo.png)
  
    a. I rutan för **SLO-URL för identitetsprovider** ersätter du den tidigare importerade SLO-URL:en med följande URL: `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0`
  
    b. Välj **OK**.

7. Välj **Enhet** i Palo Alto Networks-brandväggens administratörsgränssnitt och välj sedan **Administratörsroller**.

8. Välj knappen **Lägg till**.

9. Ange ett namn för administratörsrollen (t.ex. **fwadmin**) i rutan **Namn** i fönstret **Profil för administratörsroll**. Namnet på administratörsrollen bör matcha namnet på attributet för SAML-administratörsrollen som skickades av identitetsprovidern. Namnet och värdet för administratörsrollen skapades i avsnittet **Användarattribut** på Azure-portalen.

    ![Konfigurera administratörsrollen för Palo Alto Networks](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_adminrole.png)
  
10. Välj **Enhet** i brandväggens administratörsgränssnitt och välj sedan **Autentiseringsprofil**.

11. Välj knappen **Lägg till**.

12. Gör följande i fönstret **Autentiseringsprofil**: 

    ![Fönstret Autentiseringsprofil](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authentication_profile.png)

    a. Ange ett namn (t.ex. **AzureSAML_Admin_AuthProfile**) i rutan **Namn**.

    b. Välj **SAML** i listrutan **Typ**. 

    c. I listrutan **Serverprofil för IdP** väljer du lämplig serverprofil för SAML-identitetsprovidern (t.ex. **AzureAD Admin UI**).

    c. Markera kryssrutan **Aktivera enkel utloggning**.

    d. Ange attributnamnet (t.ex. **adminrole**) i rutan **Attribut för administratörsroll**.

    e. Välj fliken **Avancerat** och välj sedan **Lägg till** under **Listan Tillåt**.

    ![Knappen Lägg till på fliken Avancerat](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_allowlist.png)

    f. Markera kryssrutan **Alla** eller välj de användare och grupper som kan autentiseras med den här profilen.  
    När en användare autentiseras matchar brandväggen det associerade användarnamnet eller den associerade gruppen mot posterna i den här listan. Om du inte lägger till poster kan inga användare autentiseras.

    ex. Välj **OK**.

13. Om du vill att administratörer ska kunna använda SAML SSO med hjälp av Azure väljer du **enhets**  >  **konfiguration**. I fönstret **Installation** väljer du fliken **Hantering** och väljer sedan knappen **Inställningar** (”kugghjulsikon”) under **Autentiseringsinställningar**.

    ![Knappen Inställningar](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsetup.png)

14. Välj den SAML-autentiseringsprofil som du skapade i fönstret Autentiseringsprofil (t.ex. **AzureSAML_Admin_AuthProfile**).

    ![Fältet Autentiseringsprofil](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsettings.png)

15. Välj **OK**.

16. Tillämpa konfigurationen genom att välja **Genomför**.

### <a name="create-palo-alto-networks---admin-ui-test-user"></a>Skapa testanvändare för Palo Alto Networks – Admin UI

Palo Alto Networks – Admin UI stöder JIT-etablering (Just-In-Time) Om en användare inte redan finns skapas den automatiskt i systemet efter en lyckad autentisering. Ingen åtgärd krävs av dig för att skapa användaren.

### <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen för Palo Alto Networks – Admin UI på åtkomstpanelen bör du loggas in automatiskt i programmet för Palo Alto Networks – Admin UI som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Testa Palo-nätverk – admin UI med Azure AD](https://aad.portal.azure.com/)

- [Vad är session Control i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Så här skyddar du Palo-nätverk – admin-gränssnittet med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
