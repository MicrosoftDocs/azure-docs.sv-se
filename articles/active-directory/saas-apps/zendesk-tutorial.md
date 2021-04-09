---
title: 'Självstudie: Azure Active Directory integrering med Zendesk | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Zendesk.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: 58e9cc2fda7779cf0d62db8e0b6f78e5bb4d95f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98731826"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zendesk"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Zendesk

I den här självstudien får du lära dig hur du integrerar Zendesk med Azure Active Directory (Azure AD). När du integrerar Zendesk med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Zendesk.
* Gör det möjligt för användarna att logga in automatiskt till Zendesk med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.


## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Zendesk för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Zendesk stöder **SP**-initierad enkel inloggning
* Zendesk har stöd för [**automatisk** användaretablering](zendesk-provisioning-tutorial.md)


## <a name="adding-zendesk-from-the-gallery"></a>Lägga till Zendesk från galleriet

För att konfigurera integrering av Zendesk i Azure AD måste du lägga till Zendesk från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **Zendesk** i sökrutan.
1. Välj **Zendesk** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-zendesk"></a>Konfigurera och testa Azure AD SSO för Zendesk

Konfigurera och testa Azure AD SSO med Zendesk med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Zendesk.

Utför följande steg för att konfigurera och testa Azure AD SSO med Zendesk:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera ZENDESK SSO](#configure-zendesk-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa Zendesk test User](#create-zendesk-test-user)** -om du vill ha en motsvarighet till B. Simon i Zendesk som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal går du till sidan för program integrering i **Zendesk** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<subdomain>.zendesk.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<subdomain>.zendesk.com`

    c. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<subdomain>.zendesk.com/access/saml`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med faktisk inloggnings-URL, identifierare och svars-URL. Kontakta [Zendesk-klients supportteam](https://support.zendesk.com/hc/en-us/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. Zendesk-program förväntar SAML-försäkran i ett visst format. Det finns inga obligatoriska SAML-attribut, men du kan också hantera från avsnittet **Användarattribut** på sidan för programintegrering. På sidan **Konfigurera enkel inloggning med SAML** klickar du på knappen **Redigera** för att öppna dialogrutan **Användarattribut**.

    ![Skärm bild som visar användarattribut med redigerings ikonen vald.](common/edit-attribute.png)

    > [!NOTE]
    > Du kan använda tilläggsattribut för att lägga till attribut som inte ingår i Azure AD som standard. Klicka på [Användarattribut som kan konfigureras i SAML](https://support.zendesk.com/hc/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise-) för att hämta den fullständiga listan med SAML-attribut som **Zendesk** accepterar.

1. I avsnittet **SAML-signeringscertifikat** klickar du på knappen **Redigera** för att öppna dialogrutan **SAML-signeringscertifikat**.

    ![Redigera SAML-signeringscertifikat](common/edit-certificate.png)

1. I avsnittet **SAML-signeringscertifikat** , kopierar du **värdet tumavtryck** och sparar det på din dator.

    ![Kopiera tumavtrycksvärdet](common/copy-thumbprint.png)

1. I avsnittet **Konfigurera Zendesk** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Zendesk.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **Zendesk**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-zendesk-sso"></a>Konfigurera Zendesk SSO

1. Om du vill automatisera konfigurationen i **Zendesk** måste du installera **Mina appar säker inloggnings webb läsar tillägg** genom att klicka på **installera tillägget**.

    ![Skärm bild som visar knappen Installera tillägg.](./media/target-process-tutorial/install_extension.png)

1. När du har lagt till tillägg i webbläsaren, klickar du på **installations Zendesk** för att dirigera dig till Zendesk-programmet. Därifrån anger du administratörsautentiseringsuppgifterna för att logga in på Zendesk. Webb läsar tillägget kommer automatiskt att konfigurera programmet åt dig och automatisera steg 3-6.

    ![Konfigurera konfiguration](common/setup-sso.png)

1. Om du vill konfigurera Zendesk manuellt öppnar du ett nytt webbläsarfönster och loggar in på din Zendesk-företags webbplats som administratör och utför följande steg:

1. I **Zendesk administrations Center** klickar du på **säkerhets inställningar** på fliken **säkerhet** .

    ![Skärm bild som visar Zendesk administrations Center med valda säkerhets inställningar.](./media/zendesk-tutorial/settings.png "Säkerhet")

1. Gå till sidan **enkel inloggning** och klicka på **Redigera** i **SAML**.

    ![Skärm bild som visar sidan enkel inloggning med redigera valt.](./media/zendesk-tutorial/saml-sso.png "Säkerhet")

1. Utför följande steg på **SSO** -sidan.

    ![Enkel inloggning](./media/zendesk-tutorial/saml-configuration.png "Enkel inloggning")

    a. I textrutan **SAML SSO URL** klistrar du in det värde för **Inloggnings-URL** som du har kopierat från Azure-portalen.

    b. I textrutan **Certificate Fingerprint** (Fingeravtryck för certifikat) klistrar du in **tumavtrycksvärdet** för certifikatet som du har kopierat från Azure-portalen.

    c. I textrutan för **URL för fjärrutloggning** klistrar du in värdet för den **utloggnings-URL** som du har kopierat från Azure-portalen.

    d. Klicka på **Spara**.

### <a name="create-zendesk-test-user"></a>Skapa Zendesk-testanvändare

Målet med det här avsnittet är att skapa en användare med namnet Britta Simon i Zendesk. Zendesk stöder automatisk användaretablering, vilket är aktiverat som standard. Du hittar mer information [här](Zendesk-provisioning-tutorial.md) om hur du konfigurerar automatisk användaretablering.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till Zendesk-inloggnings-URL där du kan starta inloggnings flödet. 

* Gå till Zendesk-inloggnings-URL: en direkt och starta inloggnings flödet därifrån.

* Du kan använda Microsoft Mina appar. När du klickar på panelen Zendesk i Mina appar omdirigeras det till Zendesk-inloggnings-URL. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Zendesk kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).