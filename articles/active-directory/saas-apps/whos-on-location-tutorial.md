---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med WhosOnLocation | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och WhosOnLocation.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/30/2020
ms.author: jeedes
ms.openlocfilehash: d0e5134da9083e97b3977b05d601c2cfba25f5d4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92636688"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-whosonlocation"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med WhosOnLocation

I den här självstudien får du lära dig hur du integrerar WhosOnLocation med Azure Active Directory (Azure AD). När du integrerar WhosOnLocation med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till WhosOnLocation.
* Gör det möjligt för användarna att logga in automatiskt till WhosOnLocation med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* WhosOnLocation för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* WhosOnLocation stöder **SP** -INITIERAd SSO

* När du har konfigurerat WhosOnLocation kan du framtvinga sessionshantering, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen utökas från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-whosonlocation-from-the-gallery"></a>Lägga till WhosOnLocation från galleriet

Om du vill konfigurera integreringen av WhosOnLocation i Azure AD måste du lägga till WhosOnLocation från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **WhosOnLocation** i sökrutan.
1. Välj **WhosOnLocation** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-sso-for-whosonlocation"></a>Konfigurera och testa Azure AD SSO för WhosOnLocation

Konfigurera och testa Azure AD SSO med WhosOnLocation med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i WhosOnLocation.

Om du vill konfigurera och testa Azure AD SSO med WhosOnLocation, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera WHOSONLOCATION SSO](#configure-whosonlocation-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa WhosOnLocation test User](#create-whosonlocation-test-user)** -om du vill ha en motsvarighet till B. Simon i WhosOnLocation som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **WhosOnLocation** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://login.whosonlocation.com/saml/login/<CUSTOM_ID>`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://login.whosonlocation.com/saml/metadata/<CUSTOM_ID>`

    c. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://login.whosonlocation.com/saml/acs/<CUSTOM_ID>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska inloggnings-URL: en, svars-URL och identifierare. Kontakta [WhosOnLocation client support team](mailto:support@whosonlocation.com) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera WhosOnLocation** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till WhosOnLocation.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **WhosOnLocation**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-whosonlocation-sso"></a>Konfigurera WhosOnLocation SSO

1. Logga in på din WhosOnLocation företags webbplats som administratör i ett annat webbläsarfönster.

2. Klicka på **verktyg**  ->  **konto**.

    ![Skärm bild som visar det konto som valts på menyn Verktyg på WhosOnLocation-webbplatsen.](./media/WhosOnLocation-tutorial/config1.png)

3. I den vänstra navigerings sidan väljer du **åtkomst till anställda**.

    ![Skärm bild visar medarbetare-åtkomst som valts från konto profilen.](./media/WhosOnLocation-tutorial/config2.png)

4. Utför följande steg på följande sida.

    ![Skärm bild som visar employess-åtkomst-fliken där du kan ange användar data.](./media/WhosOnLocation-tutorial/config3.png)

    a. Ändra **enkel inloggning med SAML** till **Ja**.

    b. I text rutan **utfärdar-URL** klistrar du in det **entitets-ID-** värde som du har kopierat från Azure Portal.

    c. I text rutan **SSO-slutpunkt** klistrar du in URL-värdet för **inloggning** som du har kopierat från Azure Portal.

    d. Öppna det hämtade **certifikatet (base64)** från Azure Portal i anteckningar och klistra in innehållet i text rutan **certifikat** .

    e. Klicka på **Spara SAML-konfiguration**.

### <a name="create-whosonlocation-test-user"></a>Skapa WhosOnLocation test användare

I det här avsnittet skapar du en användare som heter B. Simon i WhosOnLocation. Arbeta med [WhosOnLocation support team](mailto:support@whosonlocation.com) för att lägga till användare i WhosOnLocation-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen WhosOnLocation på åtkomst panelen, bör du loggas in automatiskt på den WhosOnLocation som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](./tutorial-list.md)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Vad är villkorlig åtkomst i Azure Active Directory?](../conditional-access/overview.md)

- [Prova WhosOnLocation med Azure AD](https://aad.portal.azure.com/)

- [Vad är session Control i Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Så här skyddar du WhosOnLocation med avancerad synlighet och kontroller](/cloud-app-security/proxy-intro-aad)