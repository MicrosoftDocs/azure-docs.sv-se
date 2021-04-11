---
title: 'Självstudie: Azure Active Directory integrering med LaunchDarkly | Microsoft Docs'
description: Läs hur du konfigurerar enkel inloggning mellan Azure Active Directory och LaunchDarkly.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: jeedes
ms.openlocfilehash: d9db86e400d862dd67582ede0bf44b9e9fd1c893
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104954810"
---
# <a name="tutorial-azure-active-directory-integration-with-launchdarkly"></a>Självstudie: Azure Active Directory integrering med LaunchDarkly

I den här självstudien får du lära dig hur du integrerar LaunchDarkly med Azure Active Directory (Azure AD). När du integrerar LaunchDarkly med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till LaunchDarkly.
* Gör det möjligt för användarna att logga in automatiskt till LaunchDarkly med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

    > [!NOTE]
    > LaunchDarkly-Azure Active Directory-integreringen är enkelriktad. När du har konfigurerat integrationen kan du använda Azure AD för att hantera användare, SSO och konton i LaunchDarkly, men du **kan inte** använda LaunchDarkly för att hantera användare, SSO och konton i Azure.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* LaunchDarkly-aktiverad prenumeration med enkel inloggning.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* LaunchDarkly stöder **IDP** initierad SSO.
* LaunchDarkly stöder **just-in-Time** User-etablering.

> [!NOTE]
> ID för det här programmet är ett fast sträng värde så att endast en instans kan konfigureras i en klient.

## <a name="add-launchdarkly-from-the-gallery"></a>Lägg till LaunchDarkly från galleriet

Om du vill konfigurera integrering av LaunchDarkly i Azure AD så behöver du lägga till LaunchDarkly från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **LaunchDarkly** i sökrutan.
1. Välj **LaunchDarkly** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-launchdarkly"></a>Konfigurera och testa Azure AD SSO för LaunchDarkly

Konfigurera och testa Azure AD SSO med LaunchDarkly med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i LaunchDarkly.

Utför följande steg för att konfigurera och testa Azure AD SSO med LaunchDarkly:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera LAUNCHDARKLY SSO](#configure-launchdarkly-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa LaunchDarkly test User](#create-launchdarkly-test-user)** -om du vill ha en motsvarighet till B. Simon i LaunchDarkly som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal går du till sidan för program integrering i **LaunchDarkly** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    a. Skriv webb adressen i text rutan **identifierare** : `app.launchdarkly.com`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://app.launchdarkly.com/trust/saml2/acs/<customers-unique-id>`

    > [!NOTE]
    > Värdet för svars-URL:en är inte verkligt. Du uppdaterar värdet med den faktiska svars-URL:en, som förklaras senare i självstudiekursen. Om du har för avsikt att använda programmet i **IDP**-läge så behöver du lämna fältet **Inloggnings-URL** tomt annars kommer du inte att kunna initiera inloggningen från **IDP**. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

7. I avsnittet **Konfigurera LaunchDarkly** kopierar du lämpliga URL:er enligt dina behov.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till LaunchDarkly.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I programlistan väljer du **LaunchDarkly**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-launchdarkly-sso"></a>Konfigurera LaunchDarkly SSO

1. I ett annat webbläsarfönster så loggar du in på din LaunchDarkly-företagswebbplats som en administratör.

2. Välj **Kontoinställningar** från den vänstra navigeringspanelen.

    ![Skärm bild som visar objektet konto inställningar som valts under produktion.](./media/launchdarkly-tutorial/configure-1.png)

3. Klicka på fliken **Säkerhet**.

    ![Skärm bild som visar fliken säkerhet i konto inställningarna.](./media/launchdarkly-tutorial/configure-2.png)

4. Klicka på **ENABLE SSO** och därefter **EDIT SAML CONFIGURATION**.

    ![Skärm bild som visar sidan för enkel inloggning där du kan aktivera S S O och redigera SAML-konfiguration.](./media/launchdarkly-tutorial/configure-3.png)

5. I avsnittet **Redigera din SAML-konfiguration** utför du följande steg:

    ![Skärm bild som visar avsnittet redigera ditt SAML-konfiguration där du kan göra ändringarna som beskrivs här.](./media/launchdarkly-tutorial/configure-4.png)

    a. Kopiera **SAML URL för konsumenttjänst** för din instans och klistra in den i svars-URL-textrutan i avsnittet **LaunchDarkly-domän och URL:er** på Azure-portalen.

    b. I textrutan **Inloggnings-URL** klistrar du in värdet för **Inloggnings-URL** som du har kopierat från Azure-portalen.

    c. Öppna det hämtade certifikatet från Azure-portalen till Anteckningar, kopiera innehållet och klistra därefter in det i rutan **X.509-certifikat** eller så kan du direkt överföra certifikatet genom att klicka på **överför en**.

    d. Klicka på **Spara**.

### <a name="create-launchdarkly-test-user"></a>Skapa en LaunchDarkly-testanvändare

I det här avsnittet skapas en användare som heter B. Simon i LaunchDarkly. LaunchDarkly stöder just-in-Time-etablering, som är aktiverat som standard. Det finns inget åtgärds objekt i det här avsnittet. Om en användare inte redan finns i LaunchDarkly skapas en ny efter autentiseringen.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ.

* Klicka på testa det här programmet i Azure Portal så bör du loggas in automatiskt på den LaunchDarkly som du ställer in SSO för.

* Du kan använda Microsoft Mina appar. När du klickar på panelen LaunchDarkly i Mina appar, bör du loggas in automatiskt på den LaunchDarkly som du ställer in SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat LaunchDarkly kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).