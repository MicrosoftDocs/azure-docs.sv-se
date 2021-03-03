---
title: 'Självstudie: Azure Active Directory integrering med Cherwell | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Cherwell.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/14/2021
ms.author: jeedes
ms.openlocfilehash: 5e2cf4ac43fa2a828ebe9e9a5cb5d2e1d630a59a
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101649392"
---
# <a name="tutorial-azure-active-directory-integration-with-cherwell"></a>Självstudie: Azure Active Directory integrering med Cherwell

I den här självstudien får du lära dig hur du integrerar Cherwell med Azure Active Directory (Azure AD). När du integrerar Cherwell med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Cherwell.
* Gör det möjligt för användarna att logga in automatiskt till Cherwell med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Cherwell behöver du följande:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).

* Cherwell-aktiverad prenumeration med enkel inloggning.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Cherwell stöder **SP**-initierad enkel inloggning

> [!NOTE]
> ID för det här programmet är ett fast sträng värde så att endast en instans kan konfigureras i en klient.

## <a name="add-cherwell-from-the-gallery"></a>Lägg till Cherwell från galleriet

För att konfigurera integreringen av Cherwell med Azure AD måste du lägga till Cherwell från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **Cherwell** i sökrutan.
1. Välj **Cherwell** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-cherwell"></a>Konfigurera och testa Azure AD SSO för Cherwell

Konfigurera och testa Azure AD SSO med Cherwell med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Cherwell.

Utför följande steg för att konfigurera och testa Azure AD SSO med Cherwell:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    2. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
2. **[Konfigurera CHERWELL SSO](#configure-cherwell-sso)** – om du vill konfigurera inställningar för enskilda Sign-On på program sidan.
    1. **[Skapa Cherwell test User](#create-cherwell-test-user)** -om du vill ha en motsvarighet till B. Simon i Cherwell som är länkad till Azure AD-representation av användare.
3. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal går du till sidan för program integrering i **Cherwell** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    a. I text rutan **inloggnings-URL** skriver du en URL med följande mönster: `https://<companyname>.cherwellondemand.com/cherwellclient`

    b. I textrutan **Svars-URL** skriver du in URL:en med hjälp av följande mönster: `https://*.cherwellondemand.com`
    
    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL: en och svars-URL: en. Kontakta [Cherwell-kundsupporten](https://cherwellsupport.com/CherwellPortal) och be om värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **Konfigurera Cherwell** kopierar du lämpliga URL:er baserat på dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare med namnet B. Simon i Azure Portal.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare** och väljer sedan **alla användare**.
1. Överst på skärmen väljer du **ny användare**.
1. I **användar** egenskaperna följer du de här stegen:
   1. I fältet **namn** anger du **B. Simon**.  
   1. I fältet **användar namn** anger du `<username>@<companydomain>.<extension>` . Exempel: `B.Simon@contoso.com`.
   1. Markera kryss rutan **Visa lösen ord** och anteckna värdet som visas i rutan **lösen ord** .
   1. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Cherwell.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan med program väljer du **Cherwell**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-cherwell-sso"></a>Konfigurera Cherwell SSO

För att konfigurera enkel inloggning på **Cherwell**-sidan behöver du skicka det nedladdade **certifikatet (Base64)** och lämpliga kopierade URL:er från Azure-portalen till [Cherwells supportteam](https://cherwellsupport.com/CherwellPortal). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

> [!NOTE]
> Cherwell-supporten måste utföra själva konfigurationen av enkel inloggning. Du får ett meddelande när enkel inloggning har aktiverats för din prenumeration.

### <a name="create-cherwell-test-user"></a>Skapa Cherwell-testanvändare

Om du vill att Azure AD-användare ska kunna logga in på Cherwell måste de tillhandahållas i Cherwell. När det gäller Cherwell måste användarkontona skapas av [Cherwells supportteam](https://cherwellsupport.com/CherwellPortal).

> [!NOTE]
> Du kan etablera Azure Active Directory-användarkonton med hjälp av andra API:er eller genereringsverktyg för Cherwell-användarkonton som tillhandahålls av Cherwell.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till Cherwell-inloggnings-URL där du kan starta inloggnings flödet. 

* Gå till Cherwell-inloggnings-URL: en direkt och starta inloggnings flödet därifrån.

* Du kan använda Microsoft Mina appar. När du klickar på panelen Cherwell i Mina appar, bör du loggas in automatiskt på den Cherwell som du ställer in SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Cherwell kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram sessions kontroll med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)