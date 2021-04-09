---
title: 'Självstudie: Azure Active Directory integrering med jives | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och jives.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/02/2021
ms.author: jeedes
ms.openlocfilehash: 6f2d151ac5110a504d17e1c9e2835a339f31da8c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104581787"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-jive"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med jives

I den här självstudien får du lära dig hur du integrerar jives med Azure Active Directory (Azure AD). När du integrerar jives med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till jives.
* Gör det möjligt för användarna att logga in automatiskt till jives med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Jives för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Jives stöder **SP** -initierad SSO.
* Jives har stöd för [ **Automatisk** användar etablering](jive-provisioning-tutorial.md).

## <a name="add-jive-from-the-gallery"></a>Lägg till jives från galleriet

Om du vill konfigurera integreringen av jives i Azure AD måste du lägga till jives från galleriet i listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **jives** i sökrutan.
1. Välj **jives** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-sso-for-jive"></a>Konfigurera och testa Azure AD SSO för jives

Konfigurera och testa Azure AD SSO med jives med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i jives.

Utför följande steg för att konfigurera och testa Azure AD SSO med jives:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera JIVES SSO](#configure-jive-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa jives test User](#create-jive-test-user)** -om du vill ha en motsvarighet till B. Simon i jives som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal går du till sidan för program integrering i **jives** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

   a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<instance name>.jivecustom.com`

   b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: 
   ```http
   https://<instance name>.jiveon.com
   ```

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [jives client support team](https://www.jivesoftware.com/services-support/) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. I avsnittet **Konfigurera jives** kopierar du lämpliga URL: er enligt ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till jives.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **jives**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-jive-sso"></a>Konfigurera jives SSO

1. Om du vill konfigurera enkel inloggning på **jives** -sidan loggar du in på jives-klienten som administratör.

1. Klicka på **SAML** på menyn högst upp.

    ![Skärm bild som visar fliken SAML med aktive rad vald.](./media/jive-tutorial/jive-2.png)

    a. Välj **aktive rad** på fliken **Allmänt** .

    b. Klicka på knappen **Spara alla SAML-inställningar** .

1. Gå till fliken **IDP metadata** .

    ![Skärm bild som visar den SAML-flik I D P-METADATA som valts.](./media/jive-tutorial/jive-3.png)

    a. Kopiera innehållet i XML-filen med hämtade metadata och klistra sedan in den i text rutan **identitets leverantör (IdP) metadata** .

    b. Klicka på knappen **Spara alla SAML-inställningar** .

1. Välj fliken **Mappning av användar-attribut** .

    ![Skärm bild som visar fliken SAML med MAPPNING av användar-attribut markerat.](./media/jive-tutorial/jive-4.png)

    a. Kopiera och klistra in attributnamnet för **e** -postvärdet i text rutan **e-post** .

    b. Kopiera och klistra in attributnamnet för **givenName** -värde i text rutan för **förnamn** .

    c. I text rutan **efter namn** kopierar och klistrar du in attributnamnet för **värdet efter namn.**

### <a name="create-jive-test-user"></a>Skapa jives test användare

Syftet med det här avsnittet är att skapa en användare som kallas Britta Simon i jives. Jives stöder automatisk användar etablering, som är aktiverat som standard. Du hittar mer information [här](jive-provisioning-tutorial.md) om hur du konfigurerar automatisk användaretablering.

Om du behöver skapa användare manuellt arbetar du med [jives-klientens support team](https://www.jivesoftware.com/services-support/) för att lägga till användarna i jives-plattformen.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till jives-inloggnings-URL där du kan starta inloggnings flödet. 

* Gå till jives-inloggnings-URL: en direkt och starta inloggnings flödet därifrån.

* Du kan använda Microsoft Mina appar. När du klickar på panelen jives i Mina appar omdirigeras det till jives-inloggnings-URL. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat jives kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).