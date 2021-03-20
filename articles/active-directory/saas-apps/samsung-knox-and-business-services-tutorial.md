---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med Samsung KNOX-och Business-tjänster | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory-och Samsung KNOX-och Business-tjänster.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/27/2021
ms.author: jeedes
ms.openlocfilehash: 3c1ec38e792987f4bd7208c3bf57a882a05f4f46
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "101648058"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-samsung-knox-and-business-services"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med Samsung KNOX och Business Services

I den här självstudien får du lära dig att integrera Samsung KNOX-och Business-tjänster med Azure Active Directory (Azure AD). När du integrerar Samsung KNOX-och Business-tjänster med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Samsung KNOX-och Business-tjänster.
* Gör det möjligt för användarna att logga in automatiskt till Samsung KNOX-och Business-tjänster med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Ett Samsung KNOX-konto.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Samsung KNOX och Business Services stöder **SP** -INITIERAd SSO

> [!NOTE]
> ID för det här programmet är ett fast sträng värde så att endast en instans kan konfigureras i en klient.

## <a name="adding-samsung-knox-and-business-services-from-the-gallery"></a>Lägga till Samsung KNOX-och Business-tjänster från galleriet

Om du vill konfigurera integreringen av Samsung KNOX och Business Services i Azure AD måste du lägga till Samsung KNOX-och Business-tjänster från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **Samsung KNOX och Business Services** i sökrutan.
1. Välj **Samsung KNOX och Business Services** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-samsung-knox-and-business-services"></a>Konfigurera och testa Azure AD SSO för Samsung KNOX och Business Services

Konfigurera och testa Azure AD SSO med Samsung KNOX-och Business-tjänster med hjälp av en test användare som kallas **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i [SamsungKnox.com](https://samsungknox.com/).

Utför följande steg för att konfigurera och testa Azure AD SSO med Samsung KNOX och Business Services:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera Samsung KNOX och Business Services SSO](#configure-samsung-knox-and-business-services-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa test användare av Samsung KNOX och Business Services](#create-samsung-knox-and-business-services-test-user)** för att få en motsvarighet till B. Simon i Samsung KNOX och Business Services som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. På sidan för **Samsung KNOX-och Business Services** -programintegration i Azure Portal letar du reda på avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    * I rutan **Inloggnings-URL** anger du följande URL: `https://www.samsungknox.com`
    * Skriv webb adressen i text rutan **svars-URL (intygs mottagar tjänst-URL)** : `https://central.samsungknox.com/ams/ad/saml/acs`
    
    ![Grundläggande konfigurations värden för SAML](https://docs.samsungknox.com/assets/merge/ad-sso/basic-saml-configuration.png)

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , klickar du på Kopiera för att kopiera **URL: en för appens Federations-metadata** och spara den på din dator.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Samsung KNOX-och Business-tjänster.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **Samsung KNOX och Business Services**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-samsung-knox-and-business-services-sso"></a>Konfigurera Samsung KNOX och Business Services SSO

1. Logga in på [SamsungKnox.com](https://samsungknox.com/) som administratör i ett annat webbläsarfönster.

1. Klicka på **avataren** i det övre högra hörnet.

    ![Samsung KNOX avatar](./media/samsung-knox-and-business-services-tutorial/avatar.png)

1. Klicka på **Active Directory-inställningar** i den vänstra sid panelen och utför följande steg.

    ![ACTIVE DIRECTORY-INSTÄLLNINGAR](https://docs.samsungknox.com/assets/merge/ad-sso/ad-5.png)

    a. I text rutan **identifierare (enhets-ID)** klistrar du in det **ID** -värde som du angav i Azure Portal.

    b. I text rutan **URL för app federationens metadata** klistrar du in URL-värdet för **appens federationens metadata** som du har kopierat från Azure Portal.

    c. Klicka på **Anslut till AD SSO**.

### <a name="create-samsung-knox-and-business-services-test-user"></a>Skapa test användare för Samsung KNOX och Business Services

I det här avsnittet skapar du en användare som heter Britta Simon i Samsung KNOX och Business Services. Information om hur du bjuder in en under administratör eller en test användare till din Samsung KNOX-organisation finns i administratörs guider för [Knox konfigurera](https://docs.samsungknox.com/admin/knox-configure/Administrators.htm) eller [Knox Mobile-registrering](https://docs.samsungknox.com/admin/knox-mobile-enrollment/kme-add-an-admin.htm) . Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till [SamsungKnox.com](https://samsungknox.com/), där du kan starta inloggnings flödet. 

* Gå till [SamsungKnox.com](https://samsungknox.com/) direkt och starta inloggnings flödet därifrån.

* Du kan använda Microsoft Mina appar. När du klickar på panelen Samsung KNOX och Business Services i Mina appar omdirigeras det till [SamsungKnox.com](https://samsungknox.com/). Mer information om Mina appar finns i [Introduktion till Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Samsung KNOX-och Business-tjänsterna kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).