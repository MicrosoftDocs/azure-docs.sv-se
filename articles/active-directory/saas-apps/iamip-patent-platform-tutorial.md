---
title: 'Självstudie: Azure Active Directory-integrering med enkel inloggning (SSO) med IamIP patent plattform | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och IamIP patent plattform.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/10/2020
ms.author: jeedes
ms.openlocfilehash: 1a2139b7a0f7d51b6c759a98c93c5250666f15a9
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88525164"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-iamip-patent-platform"></a>Självstudie: Azure Active Directory-integrering med enkel inloggning (SSO) med IamIP patent plattform

I den här självstudien får du lära dig att integrera IamIP patent plattform med Azure Active Directory (Azure AD). När du integrerar IamIP patent Platform med Azure AD kan du:

* Använd Azure AD för att kontrol lera vem som har åtkomst till IamIP patent plattform.
* Gör det möjligt för användarna att logga in automatiskt till IamIP patent plattform med sina Azure AD-konton.
* Hantera dina konton på en central plats: Azure Portal.

Läs mer om SaaS-appintegrering med Azure AD i [Enkel inloggning till program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* En IamIP patent plattforms prenumeration med enkel inloggning aktive rad (SSO).

## <a name="tutorial-description"></a>Beskrivning av självstudie

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

IamIP patent Platform stöder SP-initierad och IDP-initierad SSO.

När du har konfigurerat patent plattformen IamIP kan du framtvinga kontroll av sessioner, vilket skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Sessions kontroller utökas från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


## <a name="add-iamip-patent-platform-from-the-gallery"></a>Lägg till IamIP patent plattform från galleriet

Om du vill konfigurera integrationen av IamIP patent Platform i Azure AD måste du lägga till IamIP patent Platform från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med ett arbets-eller skol konto eller med en personlig Microsoft-konto.
1. Välj **Azure Active Directory** i den vänstra rutan.
1. Gå till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **IamIP patent plattform** i sökrutan.
1. Välj **IamIP patent plattform** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-iamip-patent-platform"></a>Konfigurera och testa Azure AD SSO för IamIP patent plattform

Du konfigurerar och testar Azure AD SSO med IamIP patent plattform genom att använda en test användare med namnet B. Simon. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och motsvarande användare i IamIP patent plattform.

Om du vill konfigurera och testa Azure AD SSO med IamIP patent plattform tar du följande steg på hög nivå:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** så att användarna kan använda funktionen.
    * **[Skapa en Azure AD test-användare](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD.
    * **[Bevilja åtkomst till test användaren](#grant-access-to-the-test-user)** för att göra det möjligt för användaren att använda enkel inloggning med Azure AD.

1. **[Konfigurera IamIP patent Platform SSO](#configure-iamip-patent-platform-sso)** på program sidan.
    * **[Skapa en IamIP patent Platform-testanvändare](#create-iamip-patent-platform-test-user)** som motsvarighet till Azure AD-representationen av användaren.

1. **[Testa SSO](#test-sso)** för att verifiera att konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal:

1. I [Azure Portal](https://portal.azure.com/)på sidan **IamIP patent Platform** Application Integration i avsnittet **Hantera** väljer du **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** väljer du Penn knappen för **grundläggande SAML-konfiguration** för att redigera inställningarna:

   ![Penn knapp för grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du har en metadatafil för tjänst leverantörer och vill konfigurera SSO i IDP läge, så gör du följande:

    a. Välj **Ladda upp metadatafil**:

    ![Ladda upp metadatafil](common/upload-metadata.png)

    b. Välj knappen mapp, Välj metadatafilen och välj sedan **Ladda upp**:

    ![Mapp-och överförings knappar](common/browse-upload-metadata.png)

    c. När metadatafilen har laddats upp fylls **ID** -och **svars-URL** -värdena automatiskt i avsnittet **grundläggande SAML-konfiguration** :

    ![Identifierare och svars-URL-värden](common/idp-intiated.png)

    > [!Note]
    > Om **ID** -och **svars-URL** -värdena inte fylls i automatiskt anger du värdena manuellt enligt dina krav.

1. Välj **Ange ytterligare URL: er** och Slutför följande steg om du vill konfigurera programmet i SP-initierat läge:

    I rutan **inloggnings-URL** anger du  **https: \/ /patents.iamip.com/login-User**.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , väljer du länken **Hämta** för **certifikat (RAW)** för att ladda ned certifikatet och spara det på datorn:

    ![Länk för nedladdning av certifikat](common/certificateraw.png)

1. I avsnittet **Konfigurera IamIP patent Platform** kopierar du lämplig URL eller URL, baserat på dina krav:

    ![Kopiera konfigurations-URL:er](common/idp-intiated.png))

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare med namnet B. Simon i Azure Portal.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**. Välj **användare**och välj sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna slutför du de här stegen:
   1. I rutan **namn** anger du **B. Simon**.  
   1. I rutan **användar namn** anger du \<username> @ \<companydomain> . \<extension> . Till exempel `B.Simon@contoso.com`.
   1. Välj **Visa lösen ord**och skriv sedan ned värdet som visas i rutan **lösen ord** .
   1. Välj **Skapa**.

### <a name="grant-access-to-the-test-user"></a>Bevilja åtkomst till test användaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja användaren åtkomst till IamIP patent plattform.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **IamIP patent plattform**.
1. I avsnittet **Hantera** på appens översikts sida väljer du **användare och grupper**:

   ![Välj Användare och grupper](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** :

    ![Välj Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** i listan **användare** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. I dialog rutan **Lägg till tilldelning** väljer du knappen **tilldela** .

## <a name="configure-iamip-patent-platform-sso"></a>Konfigurera IamIP patent Platform SSO

Om du vill konfigurera enkel inloggning på den IamIP patent plattforms sidan måste du skicka det hämtade RAW-certifikatet och lämpliga URL: er som du kopierade från Azure Portal till [IamIP patent Platform support team](mailto:info@iamip.com). De konfigurerar SAML SSO-anslutningen så att den är korrekt på båda sidor.

### <a name="create-iamip-patent-platform-test-user"></a>Skapa IamIP patent Platform test användare

Arbeta med [IamIP patent Platform support team](mailto:info@iamip.com) för att lägga till en användare med namnet B. Simon i IamIP patent plattform. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet ska du testa din Azure AD SSO-konfiguration med hjälp av åtkomst panelen.

När du väljer den IamIP patent plattforms panelen på åtkomst panelen, bör du loggas in automatiskt på den IamIP patent plattforms instans som du ställer in SSO för. Mer information om åtkomst panelen finns i [Introduktion till åtkomst panelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova IamIP patent Platform med Azure AD](https://aad.portal.azure.com/)

- [Vad är session Control i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
