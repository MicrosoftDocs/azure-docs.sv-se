---
title: 'Självstudie: integrera Azure Active Directory enkel inloggning (SSO) med splan-besökare | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory-och splan-besökare.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/14/2020
ms.author: jeedes
ms.openlocfilehash: ed136d06f18190124abe4598d580f40e41bf8592
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98704227"
---
# <a name="tutorial-integrate-azure-active-directory-single-sign-on-sso-with-splan-visitor"></a>Självstudie: integrera Azure Active Directory enkel inloggning (SSO) med splan-besökare

I den här självstudien får du lära dig att integrera splan-besökare med Azure Active Directory (Azure AD). När du integrerar splan-besökare med Azure AD kan du:

* Använd Azure AD för att kontrol lera vem som har åtkomst till splan-besökare.
* Gör det möjligt för användare att logga in automatiskt till splan-besökare med sina Azure AD-konton.
* Hantera dina konton på en central plats, Azure Portal.

## <a name="prerequisites"></a>Krav

Du behöver följande för att komma igång:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* En prenumeration med enkel inloggning (SSO) med splan besök.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

Splan-besökare har stöd för IdP-initierad SSO.

## <a name="add-splan-visitor-from-the-gallery"></a>Lägg till splan-besökare från galleriet

Om du vill konfigurera integrering av splan-besökare i Azure AD lägger du till splan-besökare från galleriet i listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I den vänstra rutan väljer du tjänsten **Azure Active Directory** .
1. Gå till **Företagsprogram** och välj sedan **Alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , ange **splan-besökare** i sökrutan.
1. Välj **splan-besökare** på resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-splan-visitor"></a>Konfigurera och testa Azure AD SSO för splan-besökare

Konfigurera och testa Azure AD SSO med splan-besökare genom att använda en test användare med namnet **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren på splan-besökare.

Utför följande steg för att konfigurera och testa Azure AD SSO med splan-besökare:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD test-användare](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med test User B. Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** att aktivera B. Simon för att använda enkel inloggning i Azure AD.
1. **[Konfigurera splan-besökare SSO](#configure-splan-visitor-sso)** för att konfigurera inställningarna för enkel inloggning med splan-besökare.
    1. **[Skapa en splan-besökare](#create-a-splan-visitor-test-user)** för att få en motsvarighet till B. Simon på splan-besökare som är länkade till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal:

1. I Azure Portal på sidan **splan besöks** program integration letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera en enskild Sign-On med SAML** väljer du ikonen **Redigera/penna** för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Skärm bild som markerar ikonen Redigera/penna för grundläggande SAML-konfiguration.](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** är programmet förkonfigurerat och de nödvändiga URL: erna är förifyllda med Azure. Välj knappen **Spara** för att spara konfigurationen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata**. Välj **Ladda ned** för att ladda ned certifikatet och spara det på datorn.

    ![Skärm bild som markerar länken för nedladdning av federationsmetadata XML.](common/metadataxml.png)

1. I avsnittet **Konfigurera splan-besökare** kopierar du lämplig URL eller URL: er baserat på ditt krav.

    ![Skärm bild som markerar avsnittet konfigurations-URL: er.](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare med namnet B. Simon i Azure Portal.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare** och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I fältet **namn** anger du **B. Simon**.  
   1. I fältet **användar namn** anger du ditt användar namn i _username@companydomain.extension_ formatet. Ange till exempel **B.Simon@contoso.com** .
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till splan-besökare.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **splan besökare** för att öppna appens översikt.
1. Hitta avsnittet **Hantera** och välj sedan **användare och grupper**.
1. Välj **Lägg till användare** och sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan **användare** och klickar sedan på **Välj** längst ned på skärmen.
1. Om användaren ska tilldelas en roll väljer du den på list menyn **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen låter du **standard åtkomst** rollen vara vald.
1. I dialogrutan **Lägg till tilldelning** väljer du **Tilldela**.

## <a name="configure-splan-visitor-sso"></a>Konfigurera splan-besökare SSO

Om du vill konfigurera enkel inloggning med splan-besökare skickar du **XML-metadata för federationen** som du laddade ned och lämpliga kopierade URL: er från Azure Portal till [support teamet för splan besökare](mailto:support@splan.com). Detta säkerställer att SAML SSO-anslutningen är korrekt inställd på båda sidor.

### <a name="create-a-splan-visitor-test-user"></a>Skapa en splan-besökare test användare

Skapa en test användare med namnet **Britta Simon** på splan-besökare. Arbeta med [support teamet för splan-besökare](mailto:support@splan.com) för att lägga till användaren till splan-besökare. Du måste skapa och aktivera användaren innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO

Testa konfigurationen för enkel inloggning med Azure AD med något av följande alternativ:

* **Azure Portal**: Välj **testa det här programmet** för att automatiskt logga in på den splan-besökare som du ställer in SSO för.
* **Microsoft My Apps-portalen**: Välj panelen **splan besökare** för att automatiskt logga in på den splan-besökare som du ställer in SSO för. Mer information om mina apps-portalen finns i [Logga in och starta appar från portalen Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat splan-besökare kan du [lära dig hur du tvingar fram sessionsbaserade i Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app). Med hjälp av sessions kontroller skyddas exfiltrering och intrånget av organisationens känsliga data i real tid. Sessions kontroller utökas från villkorlig åtkomst.
