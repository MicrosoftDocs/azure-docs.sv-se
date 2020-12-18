---
title: 'Självstudie: Azure Active Directory-integrering med enkel inloggning (SSO) med data säkerhet för Imperva | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Imperva data säkerhet.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/17/2020
ms.author: jeedes
ms.openlocfilehash: 0ea08f8ec98ff8ddd4a9f15f12a0224db440b7b0
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2020
ms.locfileid: "97676066"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-imperva-data-security"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med Imperva data säkerhet

I den här självstudien får du lära dig hur du integrerar Imperva Data Security med Azure Active Directory (Azure AD). När du integrerar Imperva data säkerhet med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Imperva data säkerhet.
* Gör det möjligt för användarna att logga in automatiskt för att Imperva data säkerhet med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Imperva-prenumeration med enkel inloggning (SSO) för data säkerhet.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Imperva Data Security stöder **IDP** INITIERAd SSO

## <a name="adding-imperva-data-security-from-the-gallery"></a>Lägga till Imperva data säkerhet från galleriet

Om du vill konfigurera integreringen av Imperva data säkerhet i Azure AD måste du lägga till Imperva Data Security från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **Imperva Data Security** i sökrutan.
1. Välj **Imperva Data Security** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-sso-for-imperva-data-security"></a>Konfigurera och testa Azure AD SSO för Imperva data säkerhet

Konfigurera och testa Azure AD SSO med Imperva Data Security med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Imperva Data Security.

Utför följande steg för att konfigurera och testa Azure AD SSO med Imperva data säkerhet:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera Imperva Data Security SSO](#configure-imperva-data-security-sso)** – om du vill konfigurera inställningar för enkel inloggning på program sidan.
    1. **[Skapa Imperva Data Security test User](#create-imperva-data-security-test-user)** -om du vill ha en motsvarighet till B. Simon i Imperva Data Security som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal går du till sidan för **Imperva data säkerhets** program integrering, letar reda på avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. På sidan **Konfigurera enkel inloggning med SAML** anger du värdena för följande fält:

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://<IMPERVA_DNS_NAME>:8443`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<IMPERVA_DNS_NAME>:8443`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren och svars-URL. Kontakta [Imperva Data Security Support-teamet](mailto:support@jsonar.imperva.com) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera Imperva data säkerhet** kopierar du lämpliga URL: er baserat på ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)
### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare** och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Ett exempel är `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Imperva Data Security.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **Imperva Data Security**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-imperva-data-security-sso"></a>Konfigurera Imperva Data Security SSO

Om du vill konfigurera enkel inloggning på **Imperva data säkerhets** sida måste du skicka det hämtade **certifikatet (base64)** och lämpliga kopierade url: er från Azure Portal till [Imperva Data Security Support Team](mailto:support@jsonar.imperva.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-imperva-data-security-test-user"></a>Skapa Imperva Data Security test User

I det här avsnittet skapar du en användare som kallas Britta Simon i Imperva Data Security. Arbeta med [Imperva Data Security Support Team](mailto:support@jsonar.imperva.com) för att lägga till användare i data säkerhets plattformen Imperva. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ.

* Klicka på testa det här programmet i Azure Portal så ska du loggas in automatiskt på den Imperva data säkerhet som du ställer in för SSO

* Du kan använda Microsoft Mina appar. När du klickar på panelen Imperva data säkerhet i Mina appar, bör du loggas in automatiskt på den Imperva data säkerhet som du ställer in SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Imperva Data Security kan du framtvinga kontroll av sessioner, vilket skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


