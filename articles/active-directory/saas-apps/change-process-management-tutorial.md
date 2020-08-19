---
title: 'Självstudie: Azure AD SSO-integrering med ändrings process hantering'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och ändrings process hantering.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/07/2020
ms.author: jeedes
ms.openlocfilehash: 501f19b2022d221ad6facd66c483790b13491ba0
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88529686"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-change-process-management"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med hantering av ändrings processer

I den här självstudien får du lära dig hur du integrerar hantering av ändrings processer med Azure Active Directory (Azure AD). När du integrerar hantering av ändrings processer med Azure AD kan du:

* Använd Azure AD för att kontrol lera vem som har åtkomst till hantering av ändrings processer.
* Gör det möjligt för användarna att logga in automatiskt för att ändra process hantering med sina Azure AD-konton.
* Hantera dina konton på en central plats: Azure Portal.

Läs mer om SaaS-appintegrering med Azure AD i [Enkel inloggning till program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* En hanterings prenumeration för ändrings processen med enkel inloggning aktive rad.

## <a name="tutorial-description"></a>Beskrivning av självstudie

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

Ändrings process hantering har stöd för IDP-initierad SSO.

När du har konfigurerat hantering av ändrings processer kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Sessions kontroller utökas från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="add-change-process-management-from-the-gallery"></a>Lägg till hantering av ändrings processer från galleriet

Om du vill konfigurera integrering av ändrings process hantering i Azure AD måste du lägga till ändrings process hantering från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med ett arbets-eller skol konto eller med en personlig Microsoft-konto.
1. Välj **Azure Active Directory** i den vänstra rutan.
1. Gå till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett program.
1. I avsnittet **Lägg till från galleriet** , anger du **ändring av process hantering** i sökrutan.
1. Välj **ändrings process hantering** i panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-change-process-management"></a>Konfigurera och testa Azure AD SSO för ändrings process hantering

Du konfigurerar och testar Azure AD SSO med ändrings process hantering genom att använda en test användare med namnet B. Simon. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och motsvarande användare i ändrings process hantering.

Om du vill konfigurera och testa Azure AD SSO med ändrings process hantering ska du utföra dessa steg på hög nivå:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** så att användarna kan använda funktionen.
    1. **[Skapa en Azure AD test-användare](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD.
    1. **[Bevilja åtkomst till test användaren](#grant-access-to-the-test-user)** för att göra det möjligt för användaren att använda enkel inloggning med Azure AD.
1. **[Konfigurera SSO för hantering av ändrings processer](#configure-change-process-management-sso)** på program sidan.
    1. **[Skapa en test användare av ändrings process hantering](#create-a-change-process-management-test-user)** som en motsvarighet till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** för att verifiera att konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)på sidan **ändra process hantering** program integration i avsnittet **Hantera** väljer du **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** väljer du Penn knappen för **grundläggande SAML-konfiguration** för att redigera inställningarna:

   ![Penn knapp för grundläggande SAML-konfiguration](common/edit-urls.png)

1. Utför följande steg på sidan **Konfigurera enkel inloggning med SAML** :

    a. I rutan **identifierare** anger du en URL i följande mönster: `https://<hostname>:8443/`

    b. I rutan **svars-URL** anger du en URL i följande mönster: `https://<hostname>:8443/changepilot/saml/sso`

    > [!NOTE]
    > Föregående **ID** -och **SVARs-URL** -värden är inte de faktiska värden som du bör använda. Kontakta [support teamet för ändrings process hantering](mailto:support@realtech-us.com) för att få fram de faktiska värdena. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , väljer du länken **Hämta** för **certifikat (base64)** för att ladda ned certifikatet och spara det på datorn:

    ![Länk för nedladdning av certifikat](common/certificatebase64.png)

1. I avsnittet **Konfigurera hantering av ändrings processer** kopierar du lämplig URL eller URL, baserat på dina krav:

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja användaren åtkomst till ändrings process hantering.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **ändra process hantering**.
1. I avsnittet **Hantera** på appens översikts sida väljer du **användare och grupper**:

   ![Välj Användare och grupper](common/users-groups-blade.png)

1. Välj **Lägg till användare** och sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Välj Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** i listan **användare** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** väljer du **Tilldela**.

## <a name="configure-change-process-management-sso"></a>Konfigurera SSO för ändrings process hantering

Om du vill konfigurera enkel inloggning på sidan för hantering av ändrings processer måste du skicka det hämtade base64-certifikatet och lämpliga URL: er som du kopierade från Azure Portal till [support teamet för hantering av ändrings process](mailto:support@realtech-us.com). De konfigurerar SAML SSO-anslutningen så att den är korrekt på båda sidor.

### <a name="create-a-change-process-management-test-user"></a>Skapa en test användare för ändrings process hantering
 Arbeta med [support teamet för hantering av ändrings process](mailto:support@realtech-us.com) för att lägga till en användare med namnet B. Simon i ändrings process hanteringen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa din Azure AD SSO-konfiguration med hjälp av åtkomst panelen.

När du väljer panelen ändra process hantering i åtkomst panelen, bör du loggas in automatiskt på den ändrings process hanterings instans som du ställer in SSO för. Mer information om åtkomst panelen finns i [Introduktion till åtkomst panelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Försök ändra process hantering med Azure AD](https://aad.portal.azure.com/)

- [Vad är session Control i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Så här skyddar du ändrings process hantering med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)