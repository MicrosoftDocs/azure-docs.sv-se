---
title: 'Självstudie: Azure Active Directory integration med SAP HANA | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SAP HANA.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: jeedes
ms.openlocfilehash: fc3b5b096e339d0c0f4bca7afc92d7315246fcd4
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88552296"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-hana"></a>Självstudie: Azure Active Directory integration med SAP HANA

I den här självstudien får du lära dig hur du integrerar SAP HANA med Azure Active Directory (Azure AD).
När du integrerar SAP HANA med Azure Active Directory innebär det följande fördelar:

* Du kan styra vem som har åtkomst till SAP HANA i Azure Active Directory.
* Du kan göra så att dina användare automatiskt loggas in på SAP HANA (enkel inloggning) med sina Azure Active Directory-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure Active Directory-integrering med SAP HANA behöver du följande objekt:

- En Azure AD-prenumeration
- En SAP HANA-prenumeration som är aktiverad med enkel inloggning (SSO)
- En HANA-instans som körs på alla offentliga IaaS, lokala, virtuella Azure-datorer eller SAP på stora instanser i Azure
- XSA Administration-webbgränssnittet, samt HANA Studio installerat på HANA-instansen

> [!NOTE]
> Vi rekommenderar inte att använda en SAP HANA-produktionsmiljö för att testa stegen i den här självstudien. Testa integrationen först i utvecklings- eller mellanlagringsmiljön för programmet och använd sedan produktionsmiljön.

Följ dessa rekommendationer för att testa stegen i den här självstudien:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* SAP HANA-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* SAP HANA stöder **IDP**-initierad enkel inloggning
* SAP HANA stöder **just-in-time**-användarförsörjning

## <a name="adding-sap-hana-from-the-gallery"></a>Att lägga till SAP HANA från galleriet

Om du vill konfigurera integreringen av SAP HANA i Azure Active Directory måste du lägga till SAP HANA från galleriet till din lista över hanterade SaaS-appar.

**Lägg till SAP HANA från galleriet genom att göra följande:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **SAP HANA** i sökrutan, välj **SAP HANA** på resultatpanelen och lägg sedan till programmet genom att klicka på knappen **Lägg till**.

     ![SAP HANA i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet får du konfigurera och testa enkel inloggning i Azure Active Directory med SAP HANA baserat på testanvändaren **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure Active Directory-användare och den relaterade användaren i SAP HANA upprättas.

Om du vill konfigurera och testa enkel inloggning i Azure Active Directory med SAP HANA måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för SAP HANA](#configure-sap-hana-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa en testanvändare för SAP HANA](#create-sap-hana-test-user)** – så att du får en motsvarighet till Britta Simon i SAP HANA som är länkad till användarens Azure Active Directory-representation.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Konfigurera enkel inloggning i Azure Active Directory med SAP HANA genom att göra följande:

1. Välj [Enkel inloggning](https://portal.azure.com/) på programintegreringssidan för **SAP HANA** i **Azure Portal**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. På sidan **Konfigurera enkel inloggning med SAML** utför du följande steg:

    ![Information om SAP HANA-domän och URL:er för enkel inloggning](common/idp-intiated.png)

    a. I textrutan **Identifierare** skriver du följande: `HA100`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<Customer-SAP-instance-url>/sap/hana/xs/saml/login.xscfunc`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren och svars-URL. Skaffa dess värden genom att kontakta [supportteamet för SAP HANA-klienten](https://cloudplatform.sap.com/contact.html). Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. SAP HANA-program som förväntar sig SAML-intyg i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut i avsnittet **Användarattribut** på sidan för programintegrering. På sidan **Konfigurera enkel inloggning med SAML** klickar du på knappen **Redigera** för att öppna dialogrutan **Användarattribut**.

    ![image](common/edit-attribute.png)

6. I avsnittet **användarattribut** i dialog rutan användarattribut **& Claims** , utför följande steg:
 
    a. Öppna dialogrutan **Hantera användaranspråk** genom att klicka på **redigeringsikonen**.

    ![image](./media/saphana-tutorial/tutorial_usermail.png)

    ![image](./media/saphana-tutorial/tutorial_usermailedit.png)

    b. Välj **ExtractMailPrefix()** i listan **Transformering**.

    c. Från listan **Parameter 1** väljer du **user.mail**.

    d. Klicka på **Spara**.

7. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

### <a name="configure-sap-hana-single-sign-on"></a>Konfigurera enkel inloggning för SAP HANA

1. Om du vill konfigurera enkel inloggning för SAP HANA, loggar du in på **HANA XSA-webbkonsolen** genom att gå till respektive HTTPS-slutpunkt.

    > [!NOTE]
    > I standardkonfigurationen omdirigerar URL:en begäran till en inloggningsskärm, som kräver autentiseringsuppgifter av en autentiserad SAP HANA-databasanvändare. Den användare som loggar in måste ha behörighet att utföra administrationsuppgifter i SAML.

2. Gå till i XSA-webbgränssnittet **SAML-identitetsprovider**. Därifrån väljer du **+** knappen längst ned på skärmen för att visa **informations fönstret Lägg till identitets leverantör** . Utför sedan följande steg:

    ![Lägg till identitetsprovider](./media/saphana-tutorial/sap1.png)

    a. I fönstret **Lägg till information om identitetsprovider** klistrar du in innehållet i Metadata XML (som du laddade ner från Azure Portal) i rutan **Metadata**.

    ![Lägg till identitetsproviderinställningar](./media/saphana-tutorial/sap2.png)

    b. Om innehållet i XML-dokumentet är giltigt, extraherar parsningsprocessen den information som krävs för fälten **Ämne, Entitets-ID och Utfärdare** till skärmområdet **Allmänna data**. Den extraherar också den information som krävs för URL-fälten i skärmområdet **Mål**, till exempel fälten **Bas-URL och SingleSignOn URL (*)**.

    ![Lägg till identitetsproviderinställningar](./media/saphana-tutorial/sap3.png)

    c. I rutan **Namn** i skärmområdet **Allmänna data** anger du ett namn för den nya SAML SSO-identitetsprovidern.

    > [!NOTE]
    > Namnet på SAML IDP är obligatoriskt och måste vara unikt. Det visas i listan över tillgängliga SAML IDP:er som visas när du väljer SAML som autentiseringsmetod för SAP HANA XS-programmen som ska användas. Du kan exempelvis göra detta i skärmområdet **Autentisering** för XS-artefaktens administrationsverktyg.

3. Välj **Spara** för att spara information om SAML-identitetsprovidern och lägga till den nya SAML IDP i listan över kända SAML IDP:er.

    ![Knappen Spara](./media/saphana-tutorial/sap4.png)

4. I HANA Studio i systemegenskaperna för fliken **Konfiguration**, filtrerar du inställningarna med **saml**. Justera sedan **assertion_timeout** från **10 sek** till **120 sek**.

    ![assertion_timeout-inställning](./media/saphana-tutorial/sap7.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **användar namn** skriver du **brittasimon \@ yourcompanydomain. extension**  
    Till exempel BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet får du aktivera Britta Simon till att använda enkel inloggning i Azure genom att bevilja henne åtkomst till SAP HANA.

1. I Azure Portal väljer du **Företagsprogram**, **Alla program** och sedan **SAP HANA**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan över program skriver och väljer du **SAP HANA**.

    ![SAP HANA-länken i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-sap-hana-test-user"></a>Skapa testanvändare för SAP HANA

Om du vill göra det möjligt för Azure Active Directory-användare att logga in till SAP HANA, måste du etablera dem i SAP HANA.
SAP HANA har stöd för **just-in-time-etablering**, som är aktiverat som standard.

Om du behöver skapa en användare manuellt så gör du följande:

>[!NOTE]
>Du kan ändra den externa autentisering som användaren använder. De kan autentisera sig med ett externt system, till exempel Kerberos. Kontakta din [domänadministratör](https://cloudplatform.sap.com/contact.html) om du vill ha detaljerad information om externa identiteter.

1. Öppna [SAP HANA Studio](https://help.sap.com/viewer/a2a49126a5c546a9864aae22c05c3d0e/2.0.01/en-us) som administratör, och aktivera sedan DB-användaren för SAML SSO.

    ![Skapa användare](./media/saphana-tutorial/sap5.png)

2. Markera den osynliga kryssrutan till vänster om **SAML**, och välj sedan länken **Konfigurera**.

3. Välj **Lägg till** för att lägga till SAML IDP.  Välj lämplig SAML IDP och välj sedan **OK**.

4. Lägg till **Extern identitet** (i det här fallet BrittaSimon) eller välj **Alla**. Välj sedan **OK**.

   > [!Note]
   > Om kryssrutan **Alla** inte är markerad måste användarnamnet i HANA exakt matcha namnet på användaren i UPN innan domänsuffixet. (Till exempel blir BrittaSimon@contoso.com BrittaSimon i HANA.)

5. I testsyfte kan du tilldela alla **XS**-roller till användaren.

    ![Tilldelar roller](./media/saphana-tutorial/sap6.png)

    > [!TIP]
    > Du bör ge behörigheter som är lämpliga för dina användningar endast.

6. Spara användaren.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på SAP HANA-panelen i åtkomstpanelen så bör du automatiskt loggas in på den SAP HANA som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

