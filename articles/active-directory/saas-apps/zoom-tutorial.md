---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med zoom | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Zoom.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: jeedes
ms.openlocfilehash: d257935aa3e9ad54b64b0f416119931661809172
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88545972"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zoom"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med zoom

I den här självstudien får du lära dig hur du integrerar zoom med Azure Active Directory (Azure AD). När du integrerar zoom med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till zoom.
* Gör det möjligt för användarna att logga in automatiskt för att zooma med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Enkel inloggning (SSO) aktive rad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Zoom stöder **SP** -initierad SSO och 
* Zoom har stöd för [ **Automatisk** användar etablering](https://docs.microsoft.com/azure/active-directory/saas-apps/zoom-provisioning-tutorial).

## <a name="adding-zoom-from-the-gallery"></a>Lägga till Zoom från galleriet

För att kunna konfigurera integreringen av Zoom i Azure AD måste du lägga till Zoom från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du **Zooma** i sökrutan.
1. Välj **Zooma** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-zoom"></a>Konfigurera och testa enkel inloggning med Azure AD för zoomning

Konfigurera och testa Azure AD SSO med Zooma med en test användare som kallas **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i zoom.

Om du vill konfigurera och testa Azure AD SSO med zoom, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
2. **[Konfigurera zooma in enkel](#configure-zoom-sso)** inloggning – om du vill konfigurera inställningar för enkel inloggning på program sidan.
    1. **[Skapa zoom test User](#create-zoom-test-user)** -om du vill ha en motsvarighet till B. Simon i zoom som är länkad till Azure AD-representation av användare.
3. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan **Zooma** program integration och letar reda på avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<companyname>.zoom.us`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `<companyname>.zoom.us`

    c. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<companyname>.zoom.us`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [Zooms supportteam](https://support.zoom.us/hc/) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera zoomning** kopierar du lämpliga URL: er baserat på ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

> [!NOTE]
> Information om hur du konfigurerar roller i Azure AD finns i [Konfigurera roll anspråk som utfärdats i SAML-token för företags program](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management).

> [!NOTE]
> Zoomning kan förvänta ett grupp anspråk i SAML-nyttolasten. Om du har skapat några grupper kan du kontakta [support teamet för zoomnings klienten](https://support.zoom.us/hc/) med grupp informationen så att de kan konfigurera grupp informationen på deras slut. Du måste också ange objekt-ID: t för att [Zooma klient support teamet](https://support.zoom.us/hc/) så att de kan konfigurera objekt-ID: t i slutet. Information om hur du hämtar objekt-ID finns i [Konfigurera zoom med Azure](https://support.zoom.us/hc/articles/115005887566).

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till zoom.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. Välj **Zooma**i listan program.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-zoom-sso"></a>Konfigurera zooma in enkel inloggning

1. I ett annat webbläsarfönster loggar du in på din zoomnings företags webbplats som administratör.

2. Klicka på fliken **Enkel inloggning**.

    ![Fliken enkel inloggning](./media/zoom-tutorial/zoom-sso1.png "Enkel inloggning")

3. Klicka på fliken **Säkerhetskontroll** och gå sedan till inställningarna för **Enkel inloggning**.

4. Gör följande i avsnittet Enkel inloggning:

    ![Avsnittet enkel inloggning](./media/zoom-tutorial/zoom-sso2.png "Enkel inloggning")

    a. I textrutan **Sign-in page URL** (Inloggnings-URL) klistrar du in värdet för den **inloggnings-URL** som du har kopierat från Azure-portalen.

    b. För **inloggnings sidans URL** -värde måste du gå till Azure Portal och klicka på **Azure Active Directory** till vänster och sedan gå till **Appregistreringar**.

    ![Azure Active Directory-knappen](./media/zoom-tutorial/appreg.png)

    c. Klicka på **slut punkter**

    ![Slut punkts knappen](./media/zoom-tutorial/endpoint.png)

    d. Kopiera **slut punkten för SAML-P-utloggning** och klistra in den i URL-textrutan för **utloggnings sidan** .

    ![Knappen Kopiera slut punkt](./media/zoom-tutorial/endpoint1.png)

    e. Öppna ditt Base64-kodade certifikat i Anteckningar, kopiera innehållet till Urklipp och klistra in det i textrutan **Certifikat för identitetsprovider**.

    f. I text rutan **utfärdare** klistrar du in värdet för **Azure AD-identifieraren** som du kopierade från Azure Portal. 

    ex. Klicka på **Spara ändringar**.

    > [!NOTE]
    > Mer information finns i dokumentationen för zoomning [https://zoomus.zendesk.com/hc/articles/115005887566](https://zoomus.zendesk.com/hc/articles/115005887566)

### <a name="create-zoom-test-user"></a>Skapa Zoom-testanvändare

Syftet med det här avsnittet är att skapa en användare som kallas B. Simon i zoom. Zoom stöder automatisk användar etablering, som är aktiverat som standard. Du hittar mer information [här](https://docs.microsoft.com/azure/active-directory/saas-apps/zoom-provisioning-tutorial) om hur du konfigurerar automatisk användaretablering.

> [!NOTE]
> Om du behöver skapa en användare manuellt måste du kontakta [support teamet för zoomnings klient](https://support.zoom.us/hc/)

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Zoom-panelen i åtkomstpanelen bör du automatiskt loggas in på Zoom som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova att zooma med Azure AD](https://aad.portal.azure.com/)
