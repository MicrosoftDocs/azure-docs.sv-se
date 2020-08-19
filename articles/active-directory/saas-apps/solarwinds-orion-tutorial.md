---
title: 'Självstudie: Azure Active Directory-integrering med enkel inloggning (SSO) med SolarWinds Orion | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SolarWinds Orion.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/24/2020
ms.author: jeedes
ms.openlocfilehash: 723546d6a997d895c1a1df39b558aabb797d0a11
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88545105"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-solarwinds-orion"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med SolarWinds Orion

I den här självstudien får du lära dig att integrera SolarWinds-Orion med Azure Active Directory (Azure AD). När du integrerar SolarWinds-Orion med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till SolarWinds-Orion.
* Gör det möjligt för användarna att logga in automatiskt till SolarWinds Orion med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* SolarWinds Orion-prenumeration med enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* SolarWinds Orion stöder **SP-och IDP** -INITIERAd SSO
* När du har konfigurerat SolarWinds Orion kan du framtvinga sessionshantering, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-solarwinds-orion-from-the-gallery"></a>Lägga till SolarWinds Orion från galleriet

Om du vill konfigurera integreringen av SolarWinds-Orion i Azure AD måste du lägga till SolarWinds Orion från galleriet till listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **SolarWinds Orion** i sökrutan.
1. Välj **SolarWinds Orion** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-sso-for-solarwinds-orion"></a>Konfigurera och testa Azure AD SSO för SolarWinds-Orion

Konfigurera och testa Azure AD SSO med SolarWinds Orion med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i SolarWinds Orion.

Om du vill konfigurera och testa Azure AD SSO med SolarWinds Orion slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera SolarWinds Orion SSO](#configure-solarwinds-orion-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa SolarWinds Orion test User](#create-solarwinds-orion-test-user)** – om du vill ha en motsvarighet till B. Simon i SolarWinds Orion som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)på sidan **SolarWinds Orion** Application Integration letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://<ORION-HOSTNAME-OR-EXTERNAL-URL>`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<ORION-HOSTNAME-OR-EXTERNAL-URL>/Orion/SAMLLogin.aspx`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<ORION-HOSTNAME-OR-EXTERNAL-URL>/Orion/Login.aspx`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [SolarWinds Orion client support team](mailto:technicalsupport@solarwinds.com) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. SolarWinds Orion-programmet förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Utöver ovan förväntar sig SolarWinds Orion-program att fler attribut skickas tillbaka i SAML-svar, som visas nedan. Dessa attribut är också förifyllda, men du kan granska dem enligt dina krav.
    
    | Name |  Källattribut|
    | ----------- | --------- |
    | FirstName | user.givenname |
    | LastName | user.surname |
    | E-post |user.mail |

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera SolarWinds Orion** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till SolarWinds Orion.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **SolarWinds Orion**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-solarwinds-orion-sso"></a>Konfigurera SolarWinds Orion SSO

1. Logga in på SolarWinds-Orion och gå till **Inställningar**  ->  **alla inställningar**.

    ![ Konfiguration SolarWinds Orion ](./media/solarwinds-orion-tutorial/settings.png)

1. I avsnittet **användar konton** väljer du **SAML-konfiguration**.

    ![ Konfiguration SolarWinds Orion ](./media/solarwinds-orion-tutorial/configure-user-accounts.png)

1. Klicka på **Lägg till identitets leverantör**.

    ![ Konfiguration SolarWinds Orion ](./media/solarwinds-orion-tutorial/configure-add-identity-provider.png)

1. Utför följande steg på sidan **Lägg till identitetsprovider** :

    ![ Konfiguration SolarWinds Orion ](./media/solarwinds-orion-tutorial/configure-solarwinds.png)

    a. Gå till fliken **Konfigurera** .

    b. Ange ett giltigt namn som i text rutan **namn på identitets leverantör** `My SSO service` .

    c. I text rutan för **SSO-mål-URL** klistrar du in värdet för **inloggnings-URL** : en som du har kopierat från Azure Portal.

    d.  I text rutan **utfärdar-URL** klistrar du in värdet för **Azure AD-identifieraren** , som du har kopierat från Azure Portal.

    e. Öppna det hämtade **certifikatet (base64)** från Azure Portal i anteckningar och klistra in innehållet i text rutan för **signerings certifikat för X. 509** .

    f. Klicka på **Spara**.

### <a name="create-solarwinds-orion-test-user"></a>Skapa SolarWinds Orion-test användare

1. Logga in på SolarWinds Orion-webbplatsen och gå till **Inställningar**  ->  **alla inställningar**.

    ![ SolarWinds Orion skapa test användare ](./media/solarwinds-orion-tutorial/settings.png)

1. I avsnittet **användar konton** väljer du **Hantera konton**.

    ![ SolarWinds Orion skapa test användare ](./media/solarwinds-orion-tutorial/user-accounts.png)

1. På fliken **enskilda konton** klickar du på **Lägg till nytt konto**.

    ![ SolarWinds Orion skapa test användare ](./media/solarwinds-orion-tutorial/create-user.png)

1. Välj den typ av konto som du behöver för att skapa antingen SAML-enskilda användare eller grupper.

    ![ SolarWinds Orion skapa test användare ](./media/solarwinds-orion-tutorial/create-user-new-account.png)

1.  I text rutan **namn-ID** anger du det namn som måste matcha användar namnet eller grupp namnet exakt som i Azure AD.

1.  Klicka på **Nästa** och skicka sedan sidan.

    ![ SolarWinds Orion skapa test användare ](./media/solarwinds-orion-tutorial/create-user-name-id.png)

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen SolarWinds Orion på åtkomst panelen, bör du loggas in automatiskt på SolarWinds-Orion som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova SolarWinds Orion med Azure AD](https://aad.portal.azure.com/)

- [Vad är session Control i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Så här skyddar du SolarWinds-Orion med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

