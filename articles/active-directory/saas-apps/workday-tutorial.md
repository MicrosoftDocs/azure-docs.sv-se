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
ms.date: 08/31/2020
ms.author: jeedes
ms.openlocfilehash: af001ddd8dc468d06706e63eaf092d1179fe3fdc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96181381"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workday"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Workday

I den här självstudien får du lära dig hur du integrerar Workday med Azure Active Directory (Azure AD). När du integrerar Workday med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Workday.
* Gör det möjligt för användarna att logga in automatiskt till Workday med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Prenumeration med enkel inloggning (SSO) för Workday.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Workday stöder **SP** -initierad SSO.

* Arbets dagar Mobile Application kan nu konfigureras med Azure AD för att aktivera SSO. Om du vill ha mer information om hur du konfigurerar ska du följa [den här](workday-mobile-tutorial.md) länken.

> [!NOTE]
> ID för det här programmet är ett fast sträng värde så att endast en instans kan konfigureras i en klient.

## <a name="adding-workday-from-the-gallery"></a>Lägga till Workday från galleriet

Om du vill konfigurera en integrering av workday i Azure AD måste du lägga till Workday från galleriet i listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. Skriv **Workday** i sökrutan i avsnittet **Lägg till från galleriet** .
1. Välj **arbets dag** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-workday"></a>Konfigurera och testa Azure AD SSO för Workday

Konfigurera och testa Azure AD SSO med arbets dagar med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i arbets dagen.

Utför följande steg för att konfigurera och testa Azure AD SSO med Workday:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** för att testa enkel inloggning i Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** att aktivera B. Simon för att använda enkel inloggning i Azure AD.
2. **[Konfigurera arbets dagar](#configure-workday)** för att konfigurera SSO-inställningarna på program sidan.
    1. **[Skapa en arbets dag test användare](#create-workday-test-user)** som ska ha en motsvarighet till B. Simon på arbets dagen som är länkad till Azure AD-representation av användare.
3. **[Testa SSO](#test-sso)** för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. På sidan för program integration i Azure Portal går du till sidan för program integrering för **arbets dag** och letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera en enskild Sign-On med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. På sidan **grundläggande SAML-konfiguration** anger du värden för följande fält:

    a. I text rutan **inloggnings-URL** skriver du en URL med följande mönster: `https://impl.workday.com/<tenant>/login-saml2.flex`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://impl.workday.com/<tenant>/login-saml.htmld`

    c. I text rutan **utloggnings-URL** skriver du en URL med följande mönster: `https://impl.workday.com/<tenant>/login-saml.htmld`

    > [!NOTE]
    > De här värdena är inte verkliga. Uppdatera värdena med den faktiska inloggnings-URL: en, svars-URL och utloggnings-URL. Svars-URL: en måste ha en under domän till exempel: www, WD2, WD3, WD3-implementering, WD5, WD5-implementering).
    > Använd något som liknar `http://www.myworkday.com` Works men inte `http://myworkday.com` . Kontakta [support teamet för Workday](https://www.workday.com/en-us/partners-services/services/support.html) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. Ditt Workday-program förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar av attribut i konfigurationen för SAML-token. Följande skärmbild visar en lista över standardattribut, där **nameidentifier** mappas med **user.userprincipalname**. Workday-programmet förväntar sig att **NameIdentifier** mappas med **User. mail**, **UPN** osv. så du måste redigera mappningen av attribut genom att klicka på ikonen **Redigera** och ändra attributet mappning.

    ![Skärm bild som visar användarattribut med redigerings ikonen vald.](common/edit-attribute.png)

    > [!NOTE]
    > Här har vi mappat namn-ID med UPN (User. UserPrincipalName) som standard. Du måste mappa namn-ID: t med det faktiska användar-ID: t i ditt Workday-konto (din e-post, UPN osv.) för att lyckas med att arbeta med SSO.

1. På sidan **Konfigurera en enskild Sign-On med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du reda på **certifikat (base64)** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

   ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. Om du vill ändra **signerings** alternativen enligt ditt krav klickar du på knappen **Redigera** för att öppna dialog rutan certifikat för **SAML-signering** .

    ![Certifikat](common/edit-certificate.png) 

    ![SAML-signeringscertifikat](./media/workday-tutorial/signing-option.png)

    a. Välj **signera SAML-svar och bekräftelse** för **signerings alternativ**.

    b. Klicka på **Spara**

1. I avsnittet **Konfigurera arbets dagar** kopierar du lämpliga URL: er baserat på ditt krav.

   ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare** och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till arbets dagar.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **arbets dag**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har ställts in för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-workday"></a>Konfigurera arbets dag

1. Logga in på din Workday-företags webbplats som administratör i ett annat webbläsarfönster.

1. Sök i **sökrutan** med namnet **Redigera klient organisations konfiguration – säkerhet** på den övre vänstra sidan av start sidan.

    ![Redigera klient säkerhet](./media/workday-tutorial/IC782925.png "Redigera klient säkerhet")


1. I avsnittet **SAML-installation** utför du följande steg:

    ![SAML-konfiguration](./media/workday-tutorial/IC782926.png "SAML-konfiguration")

    a.  Välj **Aktivera SAML-autentisering**.

    b.  Klicka på **Lägg till rad**.

1. I avsnittet **SAML Identity providers** utför du följande åtgärder för den nya raden.

    a. Utför följande åtgärder för fälten som visas nedan.

    ![SAML Identity Provider 1](./media/workday-tutorial/IC7829271.png "SAML-identitetsprovider")

    * I text rutan **namn på identitetsprovider** anger du ett providernamn (till exempel: *SPInitiatedSSO*).

    * I Azure Portal, i avsnittet **Konfigurera arbets dagar** , kopierar du värdet för **Azure AD-identifieraren** och klistrar in det i text rutan **utfärdare** .

    * Öppna det hämtade **certifikatet** från Azure Portal i anteckningar och klistra in innehållet i text rutan **x. 509-certifikat** .

    b. Utför följande åtgärder för fälten som visas nedan.

    ![SAML Identity-providers 2](./media/workday-tutorial/saml-identity-provider-2.png "SAML-identitetsprovider")

    * Klicka på kryss rutan **Aktivera IDP initierad utloggning** .

    * I text rutan för **utloggnings svarets URL** skriver du **http://www.workday.com** .

    * I text rutan **utloggnings förfrågan URL** klistrar du in URL-värdet för **utloggning** som du har kopierat från Azure Portal.

    * Klicka på **SP initierad** kryss ruta.

    * I text rutan **Service Provider-ID** skriver du **http://www.workday.com** .


    * Välj **gör inte om den SP-initierade autentiseringsbegäran**.

    c. Utför följande åtgärder för fälten som visas nedan.

    ![SAML Identity providers 3](./media/workday-tutorial/saml-identity-provider-3.png "SAML-identitetsprovider")

    * I Azure Portal i avsnittet **Konfigurera arbets dagar** kopierar du värdet för **inloggnings-URL** och klistrar in det i text rutan för **IDP SSO-tjänstens URL** .

    * I text rutan **används för miljöer** väljer du lämpliga miljö namn i list rutan.

1. Utför följande steg i bilden nedan.

    ![Workday](./media/workday-tutorial/service-provider.png "SAML-identitetsprovider")

    a. I text rutan **Service Provider-ID (kommer att vara inaktuell)** skriver du **http://www.workday.com** .

    b. I text rutan för **IDP SSO-tjänsten (kommer att bli inaktuell)** skriver du **in URL** -värde för inloggning.

    c. Markera **Ta inte bort den SP-initierade autentiseringsbegäran (kommer att bli inaktuell)**.

    d. För **signatur för autentiseringsbegäran** väljer du **SHA256**.

    e. Klicka på **OK**.

    > [!NOTE]
    > Kontrol lera att du har konfigurerat enkel inloggning på rätt sätt. Om du aktiverar enkel inloggning med felaktig konfiguration kanske du inte kan ange programmet med dina autentiseringsuppgifter och bli utelåst. I den här situationen tillhandahåller arbets dagar en säkerhets kopierings-URL där användare kan logga in med sitt normala användar namn och lösen ord i följande format: [din Workday-URL]/login.Flex? Redirect = n

### <a name="create-workday-test-user"></a>Skapa test användare för Workday

1. Logga in på din Workday-företags webbplats som administratör.

1. Klicka på **profil** i det övre högra hörnet, Välj **Start** och klicka på **katalog** på fliken **program** . 

1. På sidan **katalog** väljer du **hitta arbetare** på fliken Visa.

    ![Hitta arbetare](./media/workday-tutorial/user-directory.png)

1.  På sidan **hitta arbetare** väljer du användaren från resultaten.

1. På följande sida väljer du **jobb > Worker Security** och **kontot för arbets dagar** måste stämma överens med Azure Active Directory som **namn-ID-** värde.

    ![Arbets säkerhet](./media/workday-tutorial/worker-security.png)

> [!NOTE]
> Om du vill ha mer information om hur du skapar en test användare för en arbets dag kontaktar du [support teamet för Workday-klienten](https://www.workday.com/en-us/partners-services/services/support.html).

## <a name="test-sso"></a>Testa SSO

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

1. Klicka på **testa det här programmet** i Azure Portal. Detta omdirigerar till inloggnings-URL: en för arbets dagar där du kan starta inloggnings flödet. 

2. Gå till inloggnings-URL: en för arbets dagar direkt och starta inloggnings flödet därifrån.

3. Du kan använda Microsoft Access-panelen. När du klickar på panelen arbets dag i åtkomst panelen bör du loggas in automatiskt på den arbets dag som du har konfigurerat SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat arbets dag kan du framtvinga kontroll av sessioner, vilket skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram sessions kontroll med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)