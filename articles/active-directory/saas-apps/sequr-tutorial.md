---
title: 'Självstudie: Azure Active Directory integrering med Gena Access Control | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Gena Access Control.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/17/2021
ms.author: jeedes
ms.openlocfilehash: 82c05f77781abdaea3b2c84aa1071656c206439a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104669901"
---
# <a name="tutorial-azure-active-directory-integration-with-genea-access-control"></a>Självstudie: Azure Active Directory integrering med Gena Access Control

I den här självstudien får du lära dig att integrera gen-Access Control med Azure Active Directory (Azure AD). När du integrerar Gena Access Control med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till genera Access Control.
* Gör det möjligt för användarna att logga in automatiskt till att genera Access Control med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med gen-Access Control behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/)
* Gena Access Control-aktiverad prenumeration med enkel inloggning

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Gena Access Control stöder **SP-och IDP** -initierad SSO.
> [!NOTE]
> ID för det här programmet är ett fast sträng värde så att endast en instans kan konfigureras i en klient.


## <a name="adding-genea-access-control-from-the-gallery"></a>Lägga till genera Access Control från galleriet

Om du vill konfigurera integrering av Gena Access Control i Azure AD måste du lägga till Genning-Access Control från galleriet i listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **Gena Access Control** i sökrutan.
1. Välj **gena Access Control** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-genea-access-control"></a>Konfigurera och testa Azure AD SSO för genera Access Control

Konfigurera och testa Azure AD SSO med gen Access Control med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i gen-Access Control.

Utför följande steg för att konfigurera och testa Azure AD SSO med gen Access Control:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera gen Access Control SSO](#configure-genea-access-control-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa gen-Access Control testa användare](#create-genea-access-control-test-user)** – för att få en motsvarighet till B. Simon i gena-Access Control som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal går du till sidan för program integration i **gen-Access Control** och letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, utför följande steg:

    I textrutan **Identifierare** skriver du URL:en: `https://login.sequr.io`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    a. I text rutan **inloggnings-URL** anger du URL: en: `https://login.sequr.io`

    b. I text rutan för **vidarebefordran** visas detta värde, som beskrivs senare i självstudien.

6. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

7. I avsnittet **Konfigurera gen Access Control** kopierar du lämpliga URL: er enligt ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till genera Access Control.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **gena Access Control**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .
## <a name="configure-genea-access-control-sso"></a>Konfigurera genera Access Control SSO

1. Logga in på ditt gen-Access Control företags webbplats som administratör i ett annat webbläsarfönster.

1. Klicka på **integreringarna** på den vänstra navigerings panelen.

    ![Skärm bild som visar den integrering som valts på navigerings panelen.](./media/sequr-tutorial/configure-1.png)

1. Rulla ned till avsnittet **enkel inloggning** och klicka på **Hantera**.

    ![Skärm bild som visar avsnittet enkel inloggning med knappen Hantera valt.](./media/sequr-tutorial/configure-2.png)

1. I avsnittet **hantera enkel inloggning** utför du följande steg:

    ![Skärm bild som visar avsnittet hantera en enda Sign-On där du kan ange de värden som beskrivs.](./media/sequr-tutorial/configure-3.png)

    a. I text rutan för **identitets leverantörens enkla Sign-On URL** klistrar du in URL-värdet för **inloggning** som du har kopierat från Azure Portal.

    b. Dra och släpp **certifikat** filen som du har laddat ned från Azure Portal eller ange certifikatets innehåll manuellt.

    c. När du har sparat konfigurationen genereras värdet för relä status. Kopiera värdet för **relä status** och klistra in det i text rutan för **relä läge** i avsnittet **grundläggande SAML-konfiguration** i Azure Portal.

    d. Klicka på **Spara**.

### <a name="create-genea-access-control-test-user"></a>Skapa gen Access Control-test användare

I det här avsnittet ska du skapa en användare med namnet Britta Simon i gen-Access Control. Arbeta med [Access Control klient support teamet](mailto:support@sequr.io) för att lägga till användare i modulen gena Access Control. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

#### <a name="sp-initiated"></a>SP initierad:

* Klicka på **testa det här programmet** i Azure Portal. Detta kommer att omdirigeras till genera Access Control inloggnings-URL där du kan starta inloggnings flödet.  

* Gå till gen-Access Control inloggnings-URL: en direkt och starta inloggnings flödet därifrån.

#### <a name="idp-initiated"></a>IDP initierad:

* Klicka på **testa det här programmet** i Azure Portal så bör du loggas in automatiskt på gen-Access Control för vilka du konfigurerar SSO 

Du kan också använda Microsoft Mina appar för att testa programmet i vilket läge som helst. När du klickar på panelen Gena Access Control i Mina appar, om den har kon figurer ATS i SP-läge omdirigeras du till programmets inloggnings sida för att initiera inloggnings flödet och om det kon figurer ATS i IDP-läge, bör du logga in automatiskt på gen-Access Control för vilka du konfigurerar SSO. Mer information om Mina appar finns i [Introduktion till Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat gen Access Control kan du framtvinga kontroll av sessioner, vilket skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).