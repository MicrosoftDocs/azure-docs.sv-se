---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Workday | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och arbets dagar.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.openlocfilehash: 4867a1735f091085f64bbe7010969bd086f820a1
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88527182"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workday"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Workday

I den här självstudien får du lära dig hur du integrerar Workday med Azure Active Directory (Azure AD). När du integrerar Workday med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Workday.
* Gör det möjligt för användarna att logga in automatiskt till Workday med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Prenumeration med enkel inloggning (SSO) för Workday.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Workday stöder **SP** -initierad SSO.

* När du har konfigurerat Workday kan du genomdriva en fjärrstyrningssession som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen utökas från villkorlig åtkomst. [Lär dig hur du tvingar fram sessions kontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-workday-from-the-gallery"></a>Lägga till Workday från galleriet

Om du vill konfigurera en integrering av workday i Azure AD måste du lägga till Workday från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. Skriv **Workday** i sökrutan i avsnittet **Lägg till från galleriet** .
1. Välj **arbets dag** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-workday"></a>Konfigurera och testa enkel inloggning med Azure AD för Workday

Konfigurera och testa Azure AD SSO med arbets dagar med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i arbets dagen.

Om du vill konfigurera och testa Azure AD SSO med Workday slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** för att testa enkel inloggning i Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** att aktivera B. Simon för att använda enkel inloggning i Azure AD.
2. **[Konfigurera arbets dagar](#configure-workday)** för att konfigurera SSO-inställningarna på program sidan.
    1. **[Skapa en arbets dag test användare](#create-workday-test-user)** som ska ha en motsvarighet till B. Simon på arbets dagen som är länkad till Azure AD-representation av användare.
3. **[Testa SSO](#test-sso)** för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. På sidan för program integration i [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering för **arbets dag** och letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. På sidan **grundläggande SAML-konfiguration** anger du värden för följande fält:

    a. I text rutan **inloggnings-URL** skriver du en URL med följande mönster: `https://impl.workday.com/<tenant>/login-saml2.flex`

    b. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `http://www.workday.com`

    c. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://impl.workday.com/<tenant>/login-saml.htmld`

    > [!NOTE]
    > De här värdena är inte verkliga. Uppdatera värdena med den faktiska inloggnings-URL: en och svars-URL: en. Svars-URL: en måste ha en under domän till exempel: www, WD2, WD3, WD3-implementering, WD5, WD5-implementering).
    > Använd något som liknar `http://www.myworkday.com` Works men inte `http://myworkday.com` . Kontakta [support teamet för Workday](https://www.workday.com/en-us/partners-services/services/support.html) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. Ditt Workday-program förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar av attribut i konfigurationen för SAML-token. Följande skärmbild visar en lista över standardattribut, där **nameidentifier** mappas med **user.userprincipalname**. Workday-programmet förväntar sig att **NameIdentifier** mappas med **User. mail**, **UPN**osv. så du måste redigera mappningen av attribut genom att klicka på ikonen **Redigera** och ändra attributet mappning.

    ![image](common/edit-attribute.png)

    > [!NOTE]
    > Här har vi mappat namn-ID med UPN (User. UserPrincipalName) som standard. Du måste mappa namn-ID: t med det faktiska användar-ID: t i ditt Workday-konto (din e-post, UPN osv.) för att lyckas med att arbeta med SSO.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

   ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. Om du vill ändra **signerings** alternativen enligt ditt krav klickar du på knappen **Redigera** för att öppna dialog rutan certifikat för **SAML-signering** .

    ![image](common/edit-certificate.png) 

    ![image](./media/workday-tutorial/signing-option.png)

    a. Välj **signera SAML-svar och bekräftelse** för **signerings alternativ**.

    b. Klicka på **Spara**

1. I avsnittet **Konfigurera arbets dagar** kopierar du lämpliga URL: er baserat på ditt krav.

   ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till arbets dagar.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **arbets dag**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-workday"></a>Konfigurera arbets dag

1. Logga in på din Workday-företags webbplats som administratör i ett annat webbläsarfönster.

2. Sök i **sökrutan** med namnet **Redigera klient organisations konfiguration – säkerhet** på den övre vänstra sidan av start sidan.

    ![Redigera klient säkerhet](./media/workday-tutorial/IC782925.png "Redigera klient säkerhet")

3. I avsnittet **omdirigerings-URL: er** , utför följande steg:

    ![URL: er för omdirigering](./media/workday-tutorial/IC7829581.png "URL: er för omdirigering")

    a. Klicka på **Lägg till rad**.

    b. I rutan **inloggnings-URL**för omdirigering, **timeout för URL** och **mobil omdirigering** , klistra in **inloggnings-URL:** en som du har kopierat från avsnittet **Konfigurera arbets dagar** i Azure Portal.

    c. I text rutan **utloggnings omdirigerings-URL** klistrar du in den **URL för utloggning** som du har kopierat från avsnittet **Konfigurera arbets dagar** i Azure Portal.

    d. I text rutan **används för miljöer** väljer du miljö namnet.  

   > [!NOTE]
   > Värdet för attributet Environment är kopplat till värdet för klient-URL:  
   > – Om domän namnet för arbets dag innehavarens URL börjar med implementering, till exempel: * https://www.myworkday.com/ "klient"/login-saml2.htmLD*), måste **miljöattributet** vara inställt på implementation.  
   > – Om domän namnet börjar med något annat måste du kontakta [support teamet för Workday-klienten](https://www.workday.com/en-us/partners-services/services/support.html) för att hämta det matchande **miljö** värdet.

4. I avsnittet **SAML-installation** utför du följande steg:

    ![SAML-konfiguration](./media/workday-tutorial/IC782926.png "SAML-konfiguration")

    a.  Välj **Aktivera SAML-autentisering**.

    b.  Klicka på **Lägg till rad**.

5. I avsnittet **SAML Identity providers** , utför följande steg:

    ![SAML-identitetsprovider](./media/workday-tutorial/IC7829271.png "SAML-identitetsprovider")

    a. I text rutan **namn på identitetsprovider** anger du ett providernamn (till exempel: *SPInitiatedSSO*).

    b. I Azure Portal, i avsnittet **Konfigurera arbets dagar** , kopierar du värdet för **Azure AD-identifieraren** och klistrar in det i text rutan **utfärdare** .

    ![SAML-identitetsprovider](./media/workday-tutorial/IC7829272.png "SAML-identitetsprovider")

    c. I Azure Portal går du till avsnittet **Konfigurera arbets dag** , kopierar URL-värdet för **utloggning** och klistrar in det i text rutan för **utloggnings svarets URL** .

    d. I Azure Portal i avsnittet **Konfigurera arbets dagar** kopierar du värdet för **inloggnings-URL** och klistrar in det i text rutan för **IDP SSO-tjänstens URL** .

    e. I text rutan **används för miljöer** väljer du miljö namnet.

    f. Klicka på **certifikat för offentlig nyckel för identitets leverantör**och klicka sedan på **skapa**.

    ![Skapa](./media/workday-tutorial/IC782928.png "Skapa")

    ex. Klicka på **skapa X509 offentlig nyckel**.

    ![Skapa](./media/workday-tutorial/IC782929.png "Skapa")

6. I avsnittet **Visa den offentliga nyckeln för x509** , utför följande steg:

    ![Visa offentlig x509-nyckel](./media/workday-tutorial/IC782930.png "Visa offentlig x509-nyckel")

    a. I text rutan **namn** anger du ett namn för ditt certifikat (till exempel: *skyddsutrustning \_ SP*).

    b. I text rutan **giltig från** anger du det giltiga värdet från attributvärdet för ditt certifikat.

    c.  I text rutan **giltig till** anger du det giltiga värdet för attributvärdet för ditt certifikat.

    > [!NOTE]
    > Du kan hämta det giltiga från-datumet och det giltiga till-datumet från det hämtade certifikatet genom att dubbelklicka på det.  Datumen visas på fliken **information** .
    >
    >

    d.  Öppna ditt bas-64-kodade certifikat i anteckningar och kopiera innehållet i det.

    e.  I text rutan **certifikat** klistrar du in innehållet i Urklipp.

    f.  Klicka på **OK**.

7. Utför följande steg:

    ![SSO-konfiguration](./media/workday-tutorial/WorkdaySSOConfiguratio.png "SSO-konfiguration")

    a.  I text rutan **Service Provider-ID** skriver du **http://www.workday.com** .

    b. Välj **gör inte om den SP-initierade autentiseringsbegäran**.

    c. Som **signatur för autentiseringsbegäran**väljer du **SHA256**.

    ![Metodsignatur för autentiseringsbegäran](./media/workday-tutorial/WorkdaySSOConfiguration.png "Metodsignatur för autentiseringsbegäran")

    d. Klicka på **OK**.

    ![OK](./media/workday-tutorial/IC782933.png "OK")

    > [!NOTE]
    > Kontrol lera att du har konfigurerat enkel inloggning på rätt sätt. Om du aktiverar enkel inloggning med felaktig konfiguration kanske du inte kan ange programmet med dina autentiseringsuppgifter och bli utelåst. I den här situationen tillhandahåller arbets dagar en säkerhets kopierings-URL där användare kan logga in med sitt normala användar namn och lösen ord i följande format: [din Workday-URL]/login.Flex? Redirect = n

### <a name="create-workday-test-user"></a>Skapa test användare för Workday

I det här avsnittet skapar du en användare som heter B. Simon på arbets dagen. Arbeta med [support teamet för Workday](https://www.workday.com/en-us/partners-services/services/support.html) för att lägga till användare i Workday-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO

När du väljer panelen arbets dag i åtkomst panelen, bör du loggas in automatiskt på den arbets dag som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Workday med Azure AD](https://aad.portal.azure.com)

- [Vad är session Control i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Skydda arbets dagar med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/protect-workday)