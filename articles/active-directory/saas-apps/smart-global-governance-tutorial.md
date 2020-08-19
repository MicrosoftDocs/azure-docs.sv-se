---
title: 'Självstudie: Azure AD SSO-integrering med Smart global styrning'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och smart global styrning.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/04/2020
ms.author: jeedes
ms.openlocfilehash: d93cd2efe49f819c8adeb53674b6d4f465312e06
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88527428"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-smart-global-governance"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med Smart global styrning

I den här självstudien får du lära dig hur du integrerar Smart global styrning med Azure Active Directory (Azure AD). När du integrerar Smart global styrning med Azure AD kan du:

* Använd Azure AD för att kontrol lera vem som har åtkomst till Smart global styrning.
* Gör det möjligt för användarna att logga in automatiskt till Smart global styrning med sina Azure AD-konton.
* Hantera dina konton på en central plats: Azure Portal.

Läs mer om SaaS-appintegrering med Azure AD i [Enkel inloggning till program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* En smart global styrnings prenumeration med enkel inloggning aktive rad (SSO).

## <a name="tutorial-description"></a>Beskrivning av självstudie

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

Smart global styrning stöder SP-initierad och IDP SSO.

När du har konfigurerat Smart global styrning kan du framtvinga kontroll av sessioner, vilket skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Sessions kontroller utökas från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="add-smart-global-governance-from-the-gallery"></a>Lägg till Smart global styrning från galleriet

Om du vill konfigurera integreringen av Smart global styrning i Azure AD måste du lägga till Smart global styrning från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med ett arbets-eller skol konto eller med en personlig Microsoft-konto.
1. Välj **Azure Active Directory** i den vänstra rutan.
1. Gå till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett program.
1. I avsnittet **Lägg till från galleriet** , ange **Smart global styrning** i sökrutan.
1. Välj **Smart global styrning** på resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-smart-global-governance"></a>Konfigurera och testa Azure AD SSO för smart global styrning

Du konfigurerar och testar Azure AD SSO med Smart global styrning genom att använda en test användare med namnet B. Simon. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och motsvarande användare i Smart global styrning.

Om du vill konfigurera och testa Azure AD SSO med Smart global styrning tar du följande steg på hög nivå:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** så att användarna kan använda funktionen.
    1. **[Skapa en Azure AD test-användare](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD.
    1. **[Bevilja åtkomst till test användaren](#grant-access-to-the-test-user)** för att göra det möjligt för användaren att använda enkel inloggning med Azure AD.
1. **[Konfigurera Smart global styrning med enkel inloggning](#configure-smart-global-governance-sso)** på program sidan.
    1. **[Skapa en smart global styrnings test användare](#create-a-smart-global-governance-test-user)** som motsvarighet till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** för att verifiera att konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal:

1. I [Azure Portal](https://portal.azure.com/)på sidan **Smart global styrning** program integration i avsnittet **Hantera** väljer du **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** väljer du Penn knappen för **grundläggande SAML-konfiguration** för att redigera inställningarna:

   ![Penn knapp för grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i IDP läge, utför du följande steg.

    a. I rutan **identifierare** anger du en av följande URL: er:

    - `https://eu-fr-south.console.smartglobalprivacy.com/platform/authentication-saml2/metadata`
    - `https://eu-fr-south.console.smartglobalprivacy.com/dpo/authentication-saml2/metadata`

    b. I rutan **svars-URL** anger du en av följande URL: er:

    - `https://eu-fr-south.console.smartglobalprivacy.com/platform/authentication-saml2/acs`
    - `https://eu-fr-south.console.smartglobalprivacy.com/dpo/authentication-saml2/acs`

1. Om du vill konfigurera programmet i SP-initierat läge väljer du **Ange ytterligare URL: er** och Slutför följande steg.

   - I rutan **inloggnings-URL** anger du en av följande URL: er:

    - `https://eu-fr-south.console.smartglobalprivacy.com/dpo`
    - `https://eu-fr-south.console.smartglobalprivacy.com/platform`

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , väljer du länken **Hämta** för **certifikat (RAW)** för att ladda ned certifikatet och spara det på datorn:

    ![Länk för nedladdning av certifikat](common/certificateraw.png)

1. I avsnittet **Konfigurera Smart global styrning** kopierar du lämplig URL eller URL, baserat på dina krav:

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare som heter B. Simon i Azure Portal.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**. Välj **användare**och välj sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna slutför du de här stegen:
   1. I rutan **namn** anger du **B. Simon**.  
   1. I rutan **användar namn** anger du \<username> @ \<companydomain> . \<extension> . Till exempel `B.Simon@contoso.com`.
   1. Välj **Visa lösen ord**och skriv sedan ned värdet som visas i rutan **lösen ord** .
   1. Välj **Skapa**.

### <a name="grant-access-to-the-test-user"></a>Bevilja åtkomst till test användaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja användaren åtkomst till Smart global styrning.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **Smart global styrning**.
1. I avsnittet **Hantera** på appens översikts sida väljer du **användare och grupper**:

   ![Välj Användare och grupper](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** :

    ![Välj Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** i listan **användare** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** väljer du **Tilldela**.

## <a name="configure-smart-global-governance-sso"></a>Konfigurera Smart global styrning enkel inloggning

Om du vill konfigurera enkel inloggning på den smarta globala styrnings sidan måste du skicka det hämtade RAW-certifikatet och lämpliga URL: er som du kopierade från Azure Portal till [support teamet för smart global styrning](mailto:support.tech@smartglobal.com). De konfigurerar SAML SSO-anslutningen så att den är korrekt på båda sidor.

### <a name="create-a-smart-global-governance-test-user"></a>Skapa en smart global styrning test användare

Arbeta med [support teamet för smart global styrning](mailto:support.tech@smartglobal.com) för att lägga till en användare med namnet B. Simon i Smart global styrning. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa din Azure AD SSO-konfiguration med hjälp av åtkomst panelen.

När du väljer panelen Smart global styrning i åtkomst panelen, bör du loggas in automatiskt till den smarta globala styrnings instansen som du ställer in SSO för. Mer information om åtkomst panelen finns i [Introduktion till åtkomst panelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Smart global styrning med Azure AD](https://aad.portal.azure.com/)

- [Vad är session Control i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Så här skyddar du smart global styrning med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)