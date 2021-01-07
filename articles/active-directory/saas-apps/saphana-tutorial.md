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
ms.date: 12/27/2020
ms.author: jeedes
ms.openlocfilehash: f6066997b5a63a9ffefc1371851c7200d7655c9c
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/07/2021
ms.locfileid: "97962626"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-hana"></a>Självstudie: Azure Active Directory integration med SAP HANA

I den här självstudien får du lära dig hur du integrerar SAP HANA med Azure Active Directory (Azure AD). När du integrerar SAP HANA med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till SAP HANA.
* Gör det möjligt för användarna att logga in automatiskt för att SAP HANA med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Krav

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

> [!NOTE]
> ID för det här programmet är ett fast sträng värde så att endast en instans kan konfigureras i en klient.


## <a name="adding-sap-hana-from-the-gallery"></a>Att lägga till SAP HANA från galleriet

Om du vill konfigurera integreringen av SAP HANA i Azure Active Directory måste du lägga till SAP HANA från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du **SAP HANA** i sökrutan.
1. Välj **SAP HANA** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-sap-hana"></a>Konfigurera och testa Azure AD SSO för SAP HANA

Konfigurera och testa Azure AD SSO med SAP HANA med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i SAP HANA.

Utför följande steg för att konfigurera och testa Azure AD SSO med SAP HANA:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
2. **[Konfigurera SAP HANA SSO](#configure-sap-hana-sso)** – för att konfigurera enskilda Sign-On-inställningar på program sidan.
    1. **[Skapa en testanvändare för SAP HANA](#create-sap-hana-test-user)** – så att du får en motsvarighet till Britta Simon i SAP HANA som är länkad till användarens Azure Active Directory-representation.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal går du till sidan för **SAP HANA** program integrering och letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    Skriv en URL i text rutan **svars-URL** med följande mönster:  `https://<Customer-SAP-instance-url>/sap/hana/xs/saml/login.xscfunc`

    > [!NOTE]
    > Värdet för svars-URL:en är inte verkligt. Uppdatera värdet för med den faktiska svars-URL:en. Kontakta [SAP HANA klient support teamet](https://cloudplatform.sap.com/contact.html) för att hämta värdena. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. SAP HANA-program som förväntar sig SAML-intyg i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut i avsnittet **Användarattribut** på sidan för programintegrering. På sidan **Konfigurera enkel inloggning med SAML** klickar du på knappen **Redigera** för att öppna dialogrutan **Användarattribut**.

    ![Skärm bild som visar avsnittet "användarattribut" med ikonen "redigera" markerad.](common/edit-attribute.png)

6. I avsnittet **användarattribut** i dialog rutan användarattribut **& Claims** , utför följande steg:
 
    a. Öppna dialogrutan **Hantera användaranspråk** genom att klicka på **redigeringsikonen**.

    ![Skärm bild som visar dialog rutan "User attributes & claims" med ikonen "redigera" markerad.](./media/saphana-tutorial/tutorial_usermail.png)

    ![image](./media/saphana-tutorial/tutorial_usermailedit.png)

    b. Välj **ExtractMailPrefix()** i listan **Transformering**.

    c. Från listan **Parameter 1** väljer du **user.mail**.

    d. Klicka på **Spara**.

7. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare** och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Ett exempel är `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till SAP HANA.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **SAP HANA**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-sap-hana-sso"></a>Konfigurera SAP HANA SSO

1. Om du vill konfigurera enkel inloggning för SAP HANA, loggar du in på **HANA XSA-webbkonsolen** genom att gå till respektive HTTPS-slutpunkt.

    > [!NOTE]
    > I standardkonfigurationen omdirigerar URL:en begäran till en inloggningsskärm, som kräver autentiseringsuppgifter av en autentiserad SAP HANA-databasanvändare. Den användare som loggar in måste ha behörighet att utföra administrationsuppgifter i SAML.

2. Gå till i XSA-webbgränssnittet **SAML-identitetsprovider**. Därifrån väljer du **+** knappen längst ned på skärmen för att visa **informations fönstret Lägg till identitets leverantör** . Utför sedan följande steg:

    ![Lägg till identitetsprovider](./media/saphana-tutorial/sap1.png)

    a. I fönstret **Lägg till information om identitetsprovider** klistrar du in innehållet i Metadata XML (som du laddade ner från Azure Portal) i rutan **Metadata**.

    ![Skärm bild som visar fönstret "Lägg till information om identitets leverantör" med rutorna "metadata" och "namn" markerade.](./media/saphana-tutorial/sap2.png)

    b. Om innehållet i XML-dokumentet är giltigt, extraherar parsningsprocessen den information som krävs för fälten **Ämne, Entitets-ID och Utfärdare** till skärmområdet **Allmänna data**. Den extraherar också den information som krävs för URL-fälten i skärmområdet **Mål**, till exempel fälten **Bas-URL och SingleSignOn URL (*)**.

    ![Lägg till identitetsproviderinställningar](./media/saphana-tutorial/sap3.png)

    c. I rutan **Namn** i skärmområdet **Allmänna data** anger du ett namn för den nya SAML SSO-identitetsprovidern.

    > [!NOTE]
    > Namnet på SAML IDP är obligatoriskt och måste vara unikt. Det visas i listan över tillgängliga SAML IDP:er som visas när du väljer SAML som autentiseringsmetod för SAP HANA XS-programmen som ska användas. Du kan exempelvis göra detta i skärmområdet **Autentisering** för XS-artefaktens administrationsverktyg.

3. Välj **Spara** för att spara information om SAML-identitetsprovidern och lägga till den nya SAML IDP i listan över kända SAML IDP:er.

    ![Knappen Spara](./media/saphana-tutorial/sap4.png)

4. I HANA Studio i systemegenskaperna för fliken **Konfiguration**, filtrerar du inställningarna med **saml**. Justera sedan **assertion_timeout** från **10 sek** till **120 sek**.

    ![assertion_timeout-inställning](./media/saphana-tutorial/sap7.png)


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

### <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ.

* Klicka på testa det här programmet i Azure Portal så ska du loggas in automatiskt på den SAP HANA som du har konfigurerat SSO för

* Du kan använda Microsoft Mina appar. När du klickar på panelen SAP HANA i Mina appar, bör du loggas in automatiskt på den SAP HANA som du ställer in SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Nästa steg

När du har konfigurerat SAP HANA du använda sessionshantering, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).