---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med resurs Central | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och resurs Central.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/13/2021
ms.author: jeedes
ms.openlocfilehash: dbc148fcbcd9c3be86a29df1e08755611a347b07
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100586628"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-resource-central"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med resurs Central

I den här självstudien får du lära dig hur du integrerar resurs Central med Azure Active Directory (Azure AD). När du integrerar Resource Central med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till resurs Central.
* Gör det möjligt för användarna att logga in automatiskt till resurs Central med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Aktive rad prenumeration för enkel inloggning (SSO) för resurs Central.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Resurs Central stöder **SP** -INITIERAd SSO

* Resurs centralen stöder **just-in-Time** User-etablering

## <a name="add-resource-central-from-the-gallery"></a>Lägg till resurs Central från galleriet

Om du vill konfigurera en integrering av resurs centralen i Azure AD måste du lägga till resurs Central från galleriet i listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** i sökrutan anger du **Resource Central**.
1. Välj **resurs Central** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-resource-central"></a>Konfigurera och testa Azure AD SSO för Resource Central

Konfigurera och testa Azure AD SSO med resurs centralen med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i resurs centralen.

Utför följande steg för att konfigurera och testa Azure AD SSO med Resource Central:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
    1. **[Skapa resurs Central test användare](#create-resource-central-test-user)** – om du vill ha en motsvarighet till B. Simon i resurs centralen som är länkad till Azure AD-representation av användare.
1. **[Konfigurera resurs Central SSO](#configure-resource-central-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal går du till sidan för integrering av **resurs Central** program och letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I **grundläggande SAML-konfiguration** anger du värdena för följande fält:

   1. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<DOMAIN_NAME>/ResourceCentral`

   1. I text rutan **identifierare (enhets-ID)** anger du en URL med hjälp av följande mönster:  `https://<DOMAIN_NAME>/ResourceCentral`

   1. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<DOMAIN_NAME>/ResourceCentral/ExAuth/Saml2Authentication/Acs`

    > [!NOTE]
    > Dessa värden är inte litterala värden. Uppdatera värdena med den faktiska inloggnings-URL: en, identifierare och svars-URL-värden. Hämta de här värdena genom att kontakta [support teamet för resurs Central klienten](mailto:st@aod.vn) .  Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i **SAML-signeringscertifikat**, Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I **Konfigurera resurs Central** kopierar du lämpliga URL: er baserat på ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare som heter B. Simon i Azure Portal.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare** och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du `username@companydomain.extension` . Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till resurs Central.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **resurs Central**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i fönstret **Lägg till tilldelning** .
1. I fönstret **användare och grupper** väljer du **B. Simon** från listan **användare** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den i **Välj en roll**. Om ingen roll har ställts in för den här appen ser du **standard åtkomst** rollen vald.
1. Klicka på knappen **tilldela** i fönstret **Lägg till tilldelning** .

### <a name="create-resource-central-test-user"></a>Skapa resurs Central test användare

I det här avsnittet skapas en användare som heter **B. Simon** i **resurs centralen**.

1. I resurs Central väljer du **säkerhets**  >  **personer**  >  **ny**.
  
    :::image type="content" source="./media/resource-central/new-person.png" alt-text="Skärm bild som visar fönstret personer i Resource Central, med knappen nytt markerat.":::

1. I **person uppgifter**, för **visnings namn**, anger du User **B. Simon**. För **SMTP-adress** anger du användarens användar namn för Azure AD. Till exempel `B.Simon@contoso.com`.

    :::image type="content" source="./media/resource-central/person.png" alt-text="Skärm bild som visar person informations fönstret i Resource Central.":::

## <a name="configure-resource-central-sso"></a>Konfigurera resurs Central SSO

I det här avsnittet ska du konfigurera enkel inloggning i **resursens centrala system administratör**.

1. I resurs Central system administratör väljer du **extern autentisering**.
1.  Välj **Ja** för **att aktivera konfiguration**.

    ![Skärm bild som visar alternativet Aktivera konfiguration som marker ATS i fönstret extern autentisering i Resource Central.](./media/resource-central/enable.png)

1. I **autentiseringsprotokollet** väljer du **SAML2**. 

   :::image type="content" source="./media/resource-central/protocol.png" alt-text="Skärm bild som visar SAML2 som valts för autentiseringsprotokollet i Resource Central.":::

1. Under **SAML2 konfiguration** anger du värden för följande fält:

    1. För **identifierare (enhets-ID)**, **inloggnings-URL**, **utloggnings-URL** och **Azure AD-identifierare** anger du relevanta URL: er:

       :::image type="content" source="./media/resource-central/auth.png" alt-text="Skärm bild av konfigurations rutan SAML2 i Resource Central.":::

        Kopiera URL: erna från fönstret **Konfigurera resurs Central** :

        :::image type="content" source="./media/resource-central/setup.png" alt-text="Skärm bild av fönstret Konfigurera resurs Central i Resource Central.":::

   1. För **Retur-URL** anger du `https://<DOMAIN_NAME>/ResourceCentral/ExAuth/Saml2Authentication/CallbackHandler` .
  
1. För **certifikat** laddar du upp certifikatet och anger ditt lösen ord.

   ![Skärm bild av certifikat avsnittet i resurs centralen.](./media/resource-central/cert.png)
   
1. Välj **Spara**.

1. Gå tillbaka till **Azure-portalen**. I **SAML signerings certifikat**, laddar du upp certifikatet och anger ditt lösen ord.

   ![Skärm bild av fönstret Importera certifikat i Azure Portal.](./media/resource-central/cert2.png).

1. Välj **Lägg till**.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa konfigurationen för enkel inloggning i Azure AD. För att testa enkel inloggning har du tre alternativ:

* I Azure Portal väljer du **testa det här programmet**. Länken omdirigerar till resurs Central inloggnings-URL, där du kan initiera inloggning.

* Gå till resurs Central inloggnings-URL direkt och initiera inloggning.

   :::image type="content" source="./media/resource-central/test.png" alt-text="Skärm bild av webb sidan för enkel inloggnings test för resurs Central.":::

* Använd portalen Mina appar från Microsoft. I portalen Mina appar väljer du panelen **resurs Central** för att omdirigera till URL: en för resurs Central inloggning. Mer information finns i [Logga in och starta appar från portalen Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat resurs central för enkel inloggning med Azure AD kan du framtvinga sessionshantering, vilket skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
