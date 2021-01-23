---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med LinkedIn Learning | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och LinkedIn Learning.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: e5c6bf41e1a3bf92c9141c0d3b54dd58ead2bf3c
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2021
ms.locfileid: "98727307"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-linkedin-learning"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med LinkedIn-utbildning

I den här självstudien får du lära dig hur du integrerar LinkedIn-inlärning med Azure Active Directory (Azure AD). När du integrerar LinkedIn-inlärning med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till LinkedIn-inlärning.
* Gör det möjligt för användarna att logga in automatiskt till LinkedIn-utbildning med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* LinkedIn Learning-prenumeration med enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* LinkedIn Learning stöder **SP- och IDP**-initierad enkel inloggning
* LinkedIn Learning stöder **just-in-time**-användaretablering


## <a name="adding-linkedin-learning-from-the-gallery"></a>Lägga till LinkedIn Learning från galleriet

När du ska konfigurera integrering av LinkedIn Learning i Azure AD måste du lägga till LinkedIn Learning från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från Galleri skriver du** **LinkedIn-inlärning** i sökrutan.
1. Välj **LinkedIn-inlärning** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-sso-for-linkedin-learning"></a>Konfigurera och testa Azure AD SSO för LinkedIn-utbildning

Konfigurera och testa Azure AD SSO med LinkedIn-utbildning med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i LinkedIn-inlärning.

Utför följande steg för att konfigurera och testa Azure AD SSO med LinkedIn-inlärning:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera LinkedIn Learning SSO](#configure-linkedin-learning-sso)** – för att konfigurera inställningar för enkel inloggning på program sidan.
    1. **[Skapa LinkedIn Learning test User](#create-linkedin-learning-test-user)** – om du vill ha en motsvarighet till B. Simon på LinkedIn-utbildning som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal på sidan **LinkedIn Learning** Application Integration letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

     a. Ange det **entitets-ID** som kopierats från LinkedIn-portalen i textrutan **Identifierare**. 

    b. Ange den **ACS-URL** som kopierats från LinkedIn-portalen i textrutan **Svars-URL**.

    c. Om du vill konfigurera programmet i **SP-initierat** läge klickar du sedan på alternativet **Ange ytterligare URL:er** i avsnittet för den **Grundläggande SAML-konfiguration** där du ska ange din inloggnings-URL. Skapa din inloggnings-URL genom att kopiera **ACS-URL:en** och ersätta /saml/ med /login/. När du har gjort det ska inloggnings-URL:en ha följande mönster:

    `https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=learning&applicationInstanceId=<InstanceId>`

    ![Information om LinkedIn Learning-domän och URL:er för enkel inloggning](common/metadata-upload-additional-signon.png)

    > [!NOTE]
    > Dessa värden är inte verkliga värden. Du kommer att uppdatera dessa värden med den faktiska identifieraren och svars-URL, som beskrivs senare i avsnittet **Konfigurera LinkedIn-inlärning** i självstudier.

1. LinkedIn Learning-appen förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar av attribut i konfigurationen för SAML-token. Följande skärmbild visar en lista över standardattribut, där **nameidentifier** mappas med **user.userprincipalname**. LinkedIn Learning-programmet förväntar sig att **nameidentifier** mappas med **user.mail**, så du behöver redigera attributmappningen genom att klicka på ikonen **Redigera** och ändra attributmappningen.

    ![image](common/edit-attribute.png)

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. I avsnittet **Konfigurera LinkedIn-inlärning** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till LinkedIn-inlärning.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **LinkedIn-inlärning**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-linkedin-learning-sso"></a>Konfigurera LinkedIn-inlärning SSO

1. Logga in till din LinkedIn Learning-klient som administratör i ett annat webbläsarfönster.

2. Klicka på **Globala inställningar** under **Inställningar** i **Kontocenter**. Markera dessutom **Inlärning – standard** i den nedrullningsbara listan.

    ![Skärm bild som visar de globala inställningarna där du kan välja standard.](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_01.png)

3. Klicka på **ELLER klicka här om du vill läsa in och kopiera enskilda fält i formuläret** och kopiera **entitets-Id** och **ACS-URL** och klistra in dem i avsnittet **Grundläggande SAML-konfiguration** i Azure Portal.

    ![Skärm bild som visar enkla Sign-On där du kan ange de värden som beskrivs.](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_03.png)

4. Gå till avsnittet **LinkedIn-administratörsinställningar**. Överför den XML-fil du laddade ned från Azure Portal genom att klicka på alternativet **Ladda upp XML-fil**.

    ![Skärm bild som visar konfigurera LinkedIn-tjänst leverantören s O-inställningar där du kan ladda upp en X M L-fil.](./media/linkedinlearning-tutorial/tutorial_linkedin_metadata_03.png)

5. Aktivera enkel inloggning genom att klicka på **På**. SSO-statusen ändras från **Inte ansluten** till **Ansluten**

    ![Skärm bild som visar en enkel Sign-On där du kan aktivera autentisera användare med S O.](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_05.png)

### <a name="create-linkedin-learning-test-user"></a>Skapa en testanvändare för LinkedIn Learning

LinkedIn Learning-programmet stöder just-in-time-användaretablering och efter autentiseringen skapas användare automatiskt i programmet. På LinkedIn Learning-portalens sida för administratörsinställningar ändrar du växeln **Tilldela licenser automatiskt** till aktiv just-in-time-etablering, varvid använderen även tilldelas en licens.

   ![Skapa en testanvändare för Azure AD](./media/linkedinlearning-tutorial/LinkedinUserprovswitch.png)

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

#### <a name="sp-initiated"></a>SP initierad:

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till LinkedIn Learning-inloggnings-URL där du kan starta inloggnings flödet.  

* Gå till LinkedIn Learning inloggnings-URL direkt och starta inloggnings flödet därifrån.

#### <a name="idp-initiated"></a>IDP initierad:

* Klicka på **testa det här programmet** i Azure Portal så bör du loggas in automatiskt på den LinkedIn-utbildning som du ställer in SSO för. 

Du kan också använda Microsoft Mina appar för att testa programmet i vilket läge som helst. När du klickar på panelen LinkedIn-inlärning i Mina appar, om det kon figurer ATS i SP-läge, kommer du att omdirigeras till programmets inloggnings sida för att initiera inloggnings flödet och om det kon figurer ATS i IDP-läge, bör du logga in automatiskt på den LinkedIn-utbildning som du ställer in SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Nästa steg

När du har konfigurerat LinkedIn-inlärning kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).