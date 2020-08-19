---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Cloud Academy – SSO | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Cloud Academy-SSO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/16/2020
ms.author: jeedes
ms.openlocfilehash: 88e626f9b3069b3b43d525914c017caf763a9047
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88551769"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cloud-academy---sso"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Cloud Academy – SSO

I den här självstudien får du lära dig hur du integrerar Cloud Academy-SSO med Azure Active Directory (Azure AD). När du integrerar Cloud Academy-SSO med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Cloud Academy – SSO.
* Gör det möjligt för användarna att logga in automatiskt till Cloud Academy-SSO med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Cloud Academy-inloggning med enkel inloggning (SSO) som är aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Cloud Academy – SSO stöder **SP** -INITIERAd SSO

* När du har konfigurerat Cloud Academy-SSO kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen utökas från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-cloud-academy---sso-from-the-gallery"></a>Lägga till Cloud Academy – SSO från galleriet

Om du vill konfigurera integrering av Cloud Academy-SSO i Azure AD måste du lägga till Cloud Academy-SSO från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **Cloud Academy-SSO** i sökrutan.
1. Välj **Cloud Academy – SSO** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-sso-for-cloud-academy---sso"></a>Konfigurera och testa Azure AD SSO för Cloud Academy – SSO

Konfigurera och testa Azure AD SSO med Cloud Academy-SSO med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Cloud Academy-SSO.

Om du vill konfigurera och testa Azure AD SSO med Cloud Academy-SSO, fyller du i följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera Cloud Academy – SSO SSO](#configure-cloud-academy-sso-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa Academy för enkel inloggning i molnet](#create-cloud-academy-sso-test-user)** – om du vill ha en motsvarighet till B. Simon i Cloud Academy-SSO som är länkat till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)på sidan **Cloud Academy-SSO-** program integration letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    I rutan **Inloggnings-URL** anger du följande URL: `https://cloudacademy.com/login/enterprise/`

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , klickar du på Kopiera för att kopiera **URL: en för appens Federations-metadata** och spara den på din dator.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Cloud Academy-SSO.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **Cloud Academy-SSO**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-cloud-academy-sso-sso"></a>Konfigurera Cloud Academy – SSO SSO

1. Logga in på din Cloud Academy-SSO-företags webbplats som administratör i ett annat webbläsarfönster.

1. Klicka på företagets namn och välj **inställningar & integrationer** på menyn.

    ![Konfiguration ](./media/cloud-academy-sso-tutorial/config-1.PNG)

1. På sidan **inställningar & integrationer** går du till fliken **integrationer** och klickar på **SSO** -kort.

    ![Konfiguration ](./media/cloud-academy-sso-tutorial/config-2.PNG)

1. Utför följande steg på följande sida:

    ![Konfiguration ](./media/cloud-academy-sso-tutorial/config-3.PNG)

    a. I text rutan **entitets-ID-URL** klistrar du in det **entitets-ID-** värde som du har kopierat från Azure Portal.

    b. I text rutan **SSO-URL** klistrar du in värdet för **inloggnings-URL:** en som du har kopierat från Azure Portal.

    c. Öppna det hämtade **certifikatet (base64)** från Azure Portal i anteckningar och klistra in innehållet i text rutan **certifikat** .

    d. I text rutan **namn-ID-format** är standardvärdet `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`

1. Klicka på knappen **Spara** .

    > [!NOTE]
    > Mer information om hur du konfigurerar Cloud Academy-SSO finns i [Support artikeln](https://support.cloudacademy.com/hc/articles/360043908452-Setting-Up-Single-Sign-On).

### <a name="create-cloud-academy-sso-test-user"></a>Skapa moln Academy – SSO-testanvändare

1. Logga in på **Cloud Academy-SSO** .

1. Klicka på företagets namn och välj **medlemmar** på menyn.

    ![ Skapa test användare ](./media/cloud-academy-sso-tutorial/create-user.PNG)

1. Klicka på **Bjud in medlemmar** och välj **Bjud in en enskild medlem**.

    ![ Skapa test användare ](./media/cloud-academy-sso-tutorial/create-user-1.PNG)

1. Ange de obligatoriska fälten och klicka på **Bjud in**.

    ![ Skapa test användare ](./media/cloud-academy-sso-tutorial/create-user-2.PNG)

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Cloud Academy-SSO på åtkomst panelen, bör du loggas in automatiskt på den Cloud Academy-SSO som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Testa Cloud Academy – SSO med Azure AD](https://aad.portal.azure.com/)

- [Vad är session Control i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Så här skyddar du Cloud Academy-SSO med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)