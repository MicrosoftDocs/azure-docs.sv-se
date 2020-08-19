---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Raketa | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Raketa.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/28/2020
ms.author: jeedes
ms.openlocfilehash: f5a01d74d22d6ea010d80ef1ef8ce1a53fbd4422
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88548821"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-raketa"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Raketa

I den här självstudien får du lära dig hur du integrerar Raketa med Azure Active Directory (Azure AD). När du integrerar Raketa med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Raketa.
* Gör det möjligt för användarna att logga in automatiskt till Raketa med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Raketa för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Raketa stöder **SP** -initierad SSO.
* När du har konfigurerat Raketa kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-raketa-from-the-gallery"></a>Lägga till Raketa från galleriet

Om du vill konfigurera integreringen av Raketa i Azure AD måste du lägga till Raketa från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten för **Azure Active Directory** [1].

    ![rkt_1](./media/raketa-tutorial/azure-active-directory.png)

1. Navigera till **företags program** [2] och välj sedan **alla program** [3].

1. Om du vill lägga till ett nytt program väljer du **nytt program** [4]. 

    ![rkt_2](./media/raketa-tutorial/new-app.png)

1. I avsnittet **Lägg till från galleriet** [5] skriver du **Raketa** i sökrutan [6].

1. Välj **Raketa** från resultat panelen [7] och klicka sedan på knappen **Lägg till** [8]. 

    ![rkt_3](./media/raketa-tutorial/add-btn.png)


## <a name="configure-and-test-azure-ad-single-sign-on-for-raketa"></a>Konfigurera och testa enkel inloggning med Azure AD för Raketa

Konfigurera och testa Azure AD SSO med Raketa med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Raketa.

Om du vill konfigurera och testa Azure AD SSO med Raketa, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera RAKETA SSO](#configure-raketa-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa Raketa test User](#create-raketa-test-user)** -om du vill ha en motsvarighet till B. Simon i Raketa som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **Raketa** , letar upp avsnittet **Hantera** och väljer **enkel inloggning** [9].

    ![rkt_4](./media/raketa-tutorial/manage-sso.png)

1. På sidan **Välj metod för enkel inloggning** [9] väljer du **SAML** [10].

    ![rkt_5](./media/raketa-tutorial/saml.png)

1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** [11] för att redigera inställningarna.

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    1. Skriv webb adressen i text rutorna **identifierare (enhets-ID)** [12] och **inloggnings-URL** [14]: `https://raketa.travel/` .

    1. I text rutan **svars-URL** [13] skriver du en URL med följande mönster: `https://raketa.travel/sso/acs?clientId=<CLIENT_ID>` .  

    ![rkt_6](./media/raketa-tutorial/enter-urls.png)

    > [!NOTE]
    > Värdet för svars-URL:en är inte verkligt. Uppdatera värdet för med den faktiska svars-URL:en. Kontakta [Raketa client support team](mailto:help@raketa.travel) för att hämta värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **certifikat (base64)** och väljer **Hämta** [15] för att hämta certifikatet och spara det på datorn.

1. I avsnittet **Konfigurera Raketa** kopierar du lämpliga URL: er baserat på ditt krav.

    1. Inloggnings-URL [16] – URL: en för webb sidan för auktorisering, som används för att omdirigera användarna till Authentication-systemet.

    1. Azure AD-identifierare [17] – Azure AD-identifierare.

    1. Utloggnings-URL [18] – webb sidans URL, som används för att dirigera om användarna efter utloggning.

    ![rkt_7](./media/raketa-tutorial/copy-urls.png)


### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory** [1], väljer **användare** [19] och väljer sedan **alla användare** [20].

1. Välj **ny användare** [21] överst på skärmen.

    ![rkt_8](./media/raketa-tutorial/new-user.png)

1. I **användar** egenskaperna följer du de här stegen:

   1. I fältet **användar namn** [22] anger du username@companydomain.extension . Till exempel `B.Simon@contoso.com`.

   1. I fältet **namn** [23] anger du `B.Simon` .

   1. Markera kryss rutan **Visa lösen ord** [25] och skriv sedan ned värdet som visas i rutan **lösen ord** [24].

   1. Klicka på **skapa** [26]. 

    ![rkt_9](./media/raketa-tutorial/create-user.png)


### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Raketa.

1. I Azure Portal väljer du **företags program** [2] och väljer sedan **alla program** [3].

1. I listan program väljer du **Raketa** [27].  

    ![rkt_10](./media/raketa-tutorial/add-raketa.png)

1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper** [28]. 

    ![rkt_11](./media/raketa-tutorial/users-groups.png)

1. Välj **Lägg till användare** [29] och välj sedan **användare och grupper** [30] i dialog rutan **Lägg till tilldelning** .

    ![rkt_12](./media/raketa-tutorial/add-user-raketa.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** [31] i listan användare och klickar sedan på knappen **Välj** [32] längst ned på skärmen.

1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

1. I dialog rutan **Lägg till tilldelning** klickar du på knappen **tilldela** [33]. 

    ![rkt_13](./media/raketa-tutorial/assign-user.png)

## <a name="configure-raketa-sso"></a>Konfigurera Raketa SSO

Om du vill konfigurera enkel inloggning på **Raketa** sida måste du skicka det hämtade **certifikatet (base64)** och lämpliga kopierade url: er från Azure Portal till [support teamet för Raketa](mailto:help@raketa.travel). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-raketa-test-user"></a>Skapa Raketa test användare

I det här avsnittet skapar du en användare som heter B. Simon i Raketa. Arbeta med [Raketa support team](mailto:help@raketa.travel) för att lägga till användare i Raketa-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Raketa på åtkomst panelen, bör du loggas in automatiskt på den Raketa som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Raketa med Azure AD](https://aad.portal.azure.com/)

- [Vad är session Control i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Så här skyddar du Raketa med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)