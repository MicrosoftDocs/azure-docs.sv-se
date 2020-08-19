---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med GitHub Enterprise Cloud-Enterprise Account | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och GitHub Enterprise Cloud-Enterprise-konto.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/29/2020
ms.author: jeedes
ms.openlocfilehash: 4e23c8a8497459ce7cb3cab3d1469359f80ad846
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88550783"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-github-enterprise-cloud---enterprise-account"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med GitHub Enterprise Cloud-Enterprise-konto

I den här självstudien får du lära dig att integrera GitHub Enterprise Cloud-Enterprise-konto med Azure Active Directory (Azure AD). När du integrerar GitHub Enterprise Cloud-Enterprise-konto med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till GitHub Enterprise Cloud-Enterprise-konto.
* Gör det möjligt för användarna att logga in automatiskt till GitHub Enterprise Cloud-Enterprise-konto med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* GitHub Enterprise Cloud – Enterprise Account Single Sign-on (SSO) aktive rad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* GitHub Enterprise Cloud-Enterprise-konto stöder **SP** -och **IDP** -initierad SSO
* GitHub Enterprise Cloud – Enterprise-konto stöder **just-in-Time** User-etablering
* När du har konfigurerat GitHub Enterprise Cloud-Enterprise-kontot kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-github-enterprise-cloud---enterprise-account-from-the-gallery"></a>Lägga till GitHub Enterprise Cloud-Enterprise-konto från galleriet

Om du vill konfigurera integrationen av GitHub Enterprise Cloud-Enterprise-kontot i Azure AD måste du lägga till GitHub Enterprise Cloud-Enterprise-konto från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du **GitHub Enterprise Cloud-Enterprise Account** i sökrutan.
1. Välj **GitHub Enterprise Cloud – Enterprise Account** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-sso-for-github-enterprise-cloud---enterprise-account"></a>Konfigurera och testa Azure AD SSO för GitHub Enterprise Cloud-Enterprise Account

Konfigurera och testa Azure AD SSO med GitHub Enterprise Cloud-Enterprise-konto med en test användare som kallas **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och en relaterad användare i GitHub Enterprise Cloud-Enterprise-konto.

Om du vill konfigurera och testa Azure AD SSO med GitHub Enterprise Cloud-Enterprise-konto, fyller du i följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera GitHub Enterprise Cloud-Enterprise Account SSO](#configure-github-enterprise-cloud-enterprise-account-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa GitHub Enterprise Cloud-Enterprise Account test User](#create-github-enterprise-cloud-enterprise-account-test-user)** -för att ha en motsvarighet till B. Simon i GitHub Enterprise Cloud-Enterprise-konto som är länkat till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)på sidan **GitHub Enterprise Cloud-Enterprise konto** Application Integration letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

    a. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://github.com/enterprises/<ENTERPRISE-SLUG>`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://github.com/enterprises/<ENTERPRISE-SLUG>/saml/consume`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

     I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://github.com/enterprises/<ENTERPRISE-SLUG>/sso`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska inloggnings-URL: en, svars-URL och identifierare. Kontakta [GitHub Enterprise Cloud – Enterprise account support team](mailto:support@github.com) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificateBase64.png)

1. I avsnittet **Konfigurera GitHub Enterprise Cloud-Enterprise Account** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till GitHub Enterprise Cloud-Enterprise-konto.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **GitHub Enterprise Cloud-Enterprise Account**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-github-enterprise-cloud-enterprise-account-sso"></a>Konfigurera GitHub Enterprise Cloud-Enterprise Account SSO

Om du vill konfigurera enkel inloggning på **GitHub Enterprise Cloud-Enterprise-konto** sidan måste du skicka det nedladdade **certifikatet (base64)** och lämpliga kopierade url: er från Azure Portal till [GitHub Enterprise Cloud-Enterprise account support team](mailto:support@github.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-github-enterprise-cloud-enterprise-account-test-user"></a>Skapa GitHub Enterprise Cloud-Enterprise Account test User

I det här avsnittet skapas en användare som heter B. Simon i GitHub Enterprise Cloud-Enterprise-konto. GitHub Enterprise Cloud-Enterprise-kontot har stöd för just-in-Time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i GitHub Enterprise Cloud-Enterprise-kontot, skapas en ny efter autentiseringen.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen GitHub Enterprise Cloud-Enterprise Account på åtkomst panelen, bör du loggas in automatiskt till det GitHub Enterprise Cloud-Enterprise-konto som du har konfigurerat SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova GitHub Enterprise Cloud-Enterprise-konto med Azure AD](https://aad.portal.azure.com/)

- [Vad är session Control i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Skydda GitHub Enterprise Cloud-Enterprise-konto med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)