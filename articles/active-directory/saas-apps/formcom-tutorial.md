---
title: 'Självstudie: Azure Active Directory integrering med Form.com | Microsoft Docs'
description: Lär dig att konfigurera enkel inloggning mellan Azure Active Directory och Form.com.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/05/2021
ms.author: jeedes
ms.openlocfilehash: 011f854c7c6885029468c1e93ce60af9a1c06b80
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516135"
---
# <a name="tutorial-azure-active-directory-integration-with-formcom"></a>Självstudie: Azure Active Directory integrering med Form.com

I den här självstudien lär du dig att integrera Form.com med Azure Active Directory (Azure AD). När du integrerar Form.com med Azure AD kan du:

* Kontrollera i Azure AD vem som har åtkomst till Form.com.
* Gör så att dina användare automatiskt loggas in på Form.com med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du skaffa ett [kostnadsfritt konto.](https://azure.microsoft.com/free/)
* Form.com-prenumeration med enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Form.com har stöd för **SP-initierad** enkel inloggning.

## <a name="add-formcom-from-the-gallery"></a>Lägga Form.com från galleriet

När du konfigurerar integreringen av Form.com i Azure AD, måste du lägga till Form.com från galleriet i din lista med hanterade SaaS-appar.

1. Logga in på Azure Portal med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigeringsfönstret väljer du **Azure Active Directory** tjänst.
1. Gå till **Företagsprogram** och välj sedan **Alla program.**
1. Om du vill lägga till ett nytt program väljer **du Nytt program**.
1. I avsnittet **Lägg till från** galleriet skriver **Form.com** i sökrutan.
1. Välj **Form.com** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-sso-for-formcom"></a>Konfigurera och testa enkel inloggning med Azure AD för Form.com

Konfigurera och testa enkel inloggning för Azure AD Form.com med hjälp av en testanvändare med namnet **B.Simon**. För att enkel inloggning ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Form.com.

Utför följande steg för att konfigurera och testa enkel inloggning Form.com Azure AD med hjälp av Form.com:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att B.Simon kan använda enkel inloggning med Azure AD.
1. **[Konfigurera Form.com enkel inloggning](#configure-formcom-sso)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
    1. **[Skapa Form.com testanvändare –](#create-formcom-test-user)** för att ha en motsvarighet för B.Simon i Form.com som är länkad till En Azure AD-representation av användaren.
1. **[Testa enkel inloggning](#test-sso)** – för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera enkel inloggning med Azure AD i Azure Portal.

1. I Azure Portal på sidan **Form.com** programintegrering hittar du avsnittet **Hantera** och väljer **enkel inloggning.**
1. På sidan **Välj en metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML klickar** du på pennikonen för Grundläggande **SAML-konfiguration för** att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    a. I **textrutan Inloggnings-URL** skriver du en URL med följande mönster: `https://<subdomain>.wa-form.com`

    b. I rutan **Identifierare** skriver du en URL med följande mönster: `https://<subdomain>.form.com`

    c. I **textrutan Svars-URL** skriver du en URL med något av följande mönster:
    
    ```http
    https://<subdomain>.wa-form.com/Member/UserAccount/SAML2.action
    https://<subdomain>.form.com/Member/UserAccount/SAML2.action
    ```

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska inloggnings-URL:en, identifieraren och svars-URL:en. Kontakta [Form.com-klientens supportteam](https://form.com/about/company/contact-us/) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**. Klicka på **Ladda ned** för att ladda ned **certifikatet (Base64)** och klicka på **ikonen** Kopiera för att kopiera **URL för appfederationsmetadata** från de angivna alternativen enligt dina behov och spara dem på datorn.

    ![Länk för nedladdning av certifikatet](./media/formcom-tutorial/certificatebase64-url.png)

6. I avsnittet **Konfigurera Form.com** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

I det här avsnittet skapar du en testanvändare i Azure Portal med namnet B.Simon.

1. I den vänstra rutan i Azure Portal väljer **du Azure Active Directory**, **väljer Användare** och sedan **Alla användare.**
1. Välj **Ny** användare överst på skärmen.
1. Följ dessa **steg** i Användaregenskaper:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I **fältet Användarnamn** anger du username@companydomain.extension . Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet gör du det möjligt för B.Simon att använda enkel inloggning med Azure genom att ge åtkomst till Form.com.

1. I Azure Portal väljer du **Företagsprogram** och sedan **Alla program**.
1. I listan med program väljer du **Form.com**.
1. På appens översiktssida hittar du avsnittet **Hantera** och väljer **Användare och grupper.**
1. Välj **Lägg till** användare och välj sedan Användare och **grupper** i dialogrutan Lägg **till** tilldelning.
1. I **dialogrutan Användare och** grupper väljer du **B.Simon** i  listan Användare och klickar sedan på knappen Välj längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas till användarna kan du välja den från **listrutan Välj en** roll. Om ingen roll har ställts in för den här appen visas rollen "Standardåtkomst" vald.
1. I dialogrutan **Lägg till** tilldelning klickar du på **knappen** Tilldela.

## <a name="configure-formcom-sso"></a>Konfigurera Form.com enkel inloggning

För att konfigurera enkel inloggning på **Form.com**-sidan behöver du skicka det nedladdade **certifikatet (Base64)**, **URL för appfederationsmetadata** samt lämpliga kopierade URL:er från Azure-portalen till [supportteamet för Form.com](https://form.com/about/company/contact-us/). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-formcom-test-user"></a>Skapa Form.com-testanvändare

I det här avsnittet ska du skapa en användare med namnet Britta Simon i Form.com. Ta hjälp [Form.com supportteamet för](https://form.com/about/company/contact-us/) att lägga till användare i Form.com plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen av enkel inloggning med Azure AD med följande alternativ. 

* Klicka på **Testa det här programmet** i Azure Portal. Detta omdirigerar till Form.com inloggnings-URL där du kan initiera inloggningsflödet. 

* Gå till Form.com inloggnings-URL direkt och initiera inloggningsflödet därifrån.

* Du kan använda Microsoft Mina appar. När du klickar Form.com panelen i Mina appar omdirigeras den till Form.com inloggnings-URL. Mer information om Mina appar finns i [Introduktion till Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Nästa steg

När du Form.com kan du framtvinga sessionskontroll, vilket skyddar exfiltrering och infiltrering av organisationens känsliga data i realtid. Sessionskontrollen utökas från villkorlig åtkomst. [Lär dig hur du framtvingar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
