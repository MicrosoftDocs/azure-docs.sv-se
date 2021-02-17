---
title: 'Självstudie: Azure Active Directory-integrering med enkel inloggning (SSO) med SurveyMonkey Enterprise | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SurveyMonkey Enterprise.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: fe5f53d2d00b5bceb2298e1aba837e65b1f2927c
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100555331"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-surveymonkey-enterprise"></a>Självstudie: Azure Active Directory-integrering med enkel inloggning (SSO) med SurveyMonkey Enterprise

I den här självstudien får du lära dig att integrera SurveyMonkey Enterprise med Azure Active Directory (Azure AD). När du integrerar SurveyMonkey Enterprise med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till SurveyMonkey Enterprise.
* Gör det möjligt för användarna att logga in automatiskt till SurveyMonkey Enterprise med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* SurveyMonkey Enterprise Single Sign-on (SSO) aktive rad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* SurveyMonkey Enterprise stöder **IDP** initierad SSO.

> [!NOTE]
> ID för det här programmet är ett fast sträng värde så att endast en instans kan konfigureras i en klient.

## <a name="add-surveymonkey-enterprise-from-the-gallery"></a>Lägg till SurveyMonkey Enterprise från galleriet

Om du vill konfigurera integrationen av SurveyMonkey Enterprise i Azure AD måste du lägga till SurveyMonkey Enterprise från galleriet i listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **SurveyMonkey Enterprise** i sökrutan.
1. Välj **SurveyMonkey Enterprise** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-surveymonkey-enterprise"></a>Konfigurera och testa Azure AD SSO för SurveyMonkey Enterprise

Konfigurera och testa Azure AD SSO med SurveyMonkey Enterprise med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i SurveyMonkey Enterprise.

Utför följande steg för att konfigurera och testa Azure AD SSO med SurveyMonkey Enterprise:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera SurveyMonkey Enterprise SSO](#configure-surveymonkey-enterprise-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa SurveyMonkey Enterprise test User](#create-surveymonkey-enterprise-test-user)** – för att få en motsvarighet till B. Simon i SurveyMonkey Enterprise som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal på sidan **SurveyMonkey Enterprise** Application Integration letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** är programmet förkonfigurerat och de nödvändiga URL: erna är redan ifyllda med Azure. Användaren måste spara konfigurationen genom att klicka på knappen **Spara** .

1. SurveyMonkey Enterprise-program förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut.

    ![image](common/edit-attribute.png)

6. Utöver ovan förväntar sig SurveyMonkey Enterprise Application att fler attribut skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda, men du kan granska dem efter behov.

    | Name | Källattribut|
    | ---------------| --------------- |
    | E-post | user.mail |
    | FirstName | user.givenname |
    | LastName | user.surname |

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. I avsnittet **Konfigurera SurveyMonkey Enterprise** kopierar du lämpliga URL: er baserat på ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till SurveyMonkey Enterprise.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **SurveyMonkey Enterprise**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-surveymonkey-enterprise-sso"></a>Konfigurera SurveyMonkey Enterprise SSO

Om du vill konfigurera enkel inloggning på **SurveyMonkey Enterprise** sida måste du skicka den hämtade **XML-metadata för federationsmetadata** och lämpliga kopierade url: er från Azure Portal till [SurveyMonkey Enterprise Support Team](mailto:support@selerix.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-surveymonkey-enterprise-test-user"></a>Skapa SurveyMonkey Enterprise test User

Det är inte nödvändigt att skapa en test användare i SurveyMonkey Enterprise. Användar konton kommer att tillhandahållas om användaren väljer att skapa ett nytt konto, baserat på SAML-kontrollerad. SurveyMonkey Enterprise Customer Manager kommer att tillhandahålla steg för att slutföra den här processen när dina Azure-metadata har lagts till i SurveyMonkey Enterprise-konfigurationen och är redo att verifieras.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ.

* Klicka på testa det här programmet i Azure Portal så bör du loggas in automatiskt till det SurveyMonkey-företag som du ställer in SSO för.

* Du kan använda Microsoft Mina appar. När du klickar på panelen SurveyMonkey Enterprise i Mina appar, bör du loggas in automatiskt till det SurveyMonkey-företag som du ställer in SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat SurveyMonkey Enterprise kan du framtvinga sessionshantering, vilket skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).