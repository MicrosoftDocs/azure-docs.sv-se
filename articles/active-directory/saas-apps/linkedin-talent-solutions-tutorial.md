---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med LinkedIn personal-lösningar | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory-och LinkedIn personal-lösningar.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/29/2020
ms.author: jeedes
ms.openlocfilehash: 160c7514b095a330a52dd1607dca6f2eb87215d8
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2021
ms.locfileid: "98727354"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-linkedin-talent-solutions"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med LinkedIn personal-lösningar

I den här självstudien får du lära dig hur du integrerar LinkedIn personal-lösningar med Azure Active Directory (Azure AD). När du integrerar LinkedIn personal-lösningar med Azure AD kan du:

* Kontroll i Azure AD som har till gång till LinkedIn personal-lösningar.
* Gör det möjligt för användarna att logga in automatiskt till LinkedIn personal-lösningar med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Åtkomst till konto Center på din personal-instrument panel för LinkedIn-lösningar

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* LinkedIn personal-lösningar har stöd för **SP-och IDP** -INITIERAd SSO
* LinkedIn personal-lösningar har stöd **för just vid tidpunkten för** användar etablering


## <a name="adding-linkedin-talent-solutions-from-the-gallery"></a>Lägga till LinkedIn personal-lösningar från galleriet

Om du vill konfigurera integrering av LinkedIn personal-lösningar i Azure AD måste du lägga till LinkedIn personal-lösningar från galleriet i listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **LinkedIn personal-lösningar** i sökrutan.
1. Välj **LinkedIn personal-lösningar** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-sso-for-linkedin-talent-solutions"></a>Konfigurera och testa Azure AD SSO för LinkedIn personal-lösningar

Konfigurera och testa Azure AD SSO med LinkedIn personal-lösningar med hjälp av en test användare som kallas **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i LinkedIn personal-lösningar.

Utför följande steg för att konfigurera och testa Azure AD SSO med LinkedIn personal-lösningar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera LinkedIn personal-lösningar SSO](#configure-linkedin-talent-solutions-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa LinkedIn personal-lösningar testa användare](#create-linkedin-talent-solutions-test-user)** – för att få en motsvarighet till B. Simon på LinkedIn personal-lösningar som är länkade till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal på sidan **LinkedIn personal Solutions** Application Integration letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg om du har **metadatafilen för tjänstleverantör**:

    a. Klicka på **Ladda upp metadatafil**.

    ![image1](common/upload-metadata.png)

    b. Klicka på **mappikonen** för att välja metadatafilen och klicka på **Ladda upp**.

    ![image2](common/browse-upload-metadata.png)

    c. När metadatafilen har laddats upp, fylls **ID** och **svars-URL** -värden automatiskt i avsnittet grundläggande SAML-konfiguration:

    ![image3](common/idp-intiated.png)

    > [!Note]
    > Om värdena **Identifierare** och **Svars-URL** inte fylls i automatiskt fyller du i värdena manuellt enligt dina krav.

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I rutan **Inloggnings-URL** anger du följande URL: `https://www.linkedin.com/talent/`

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. I avsnittet **Konfigurera LinkedIn personal-lösningar** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till LinkedIn personal-lösningar.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **LinkedIn personal-lösningar**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-linkedin-talent-solutions-sso"></a>Konfigurera LinkedIn personal-lösningar SSO

1. Logga in på LinkedIn personal-lösningar webbplats som administratör.
1. Gå till **konto Center**. 
1. Välj fliken **Inställningar** i navigerings fältet.

    ![Sidan Inställningar](./media/linkedin-talent-solutions-tutorial/settings.png)

1. Expandera avsnittet **enkel Sign-On (SSO)** . 

1. Klicka på knappen **Ladda ned** för att ladda ned **metadatafilen** eller klicka på **eller klicka här för att läsa in och kopiera enskilda fält från formulär** länken för att Visa konfigurations data.

    ![ konfigurations data](./media/linkedin-talent-solutions-tutorial/sso-settings.png)

1. Utför följande steg för att kopiera de enskilda fälten från formuläret.

    ![ konfiguration med indata](./media/linkedin-talent-solutions-tutorial/configuration.png)

    a. Kopiera **entitets-ID** -värde, klistra in det här värdet i text rutan **Azure AD-identifierare** i avsnittet **grundläggande SAML-konfiguration** i Azure Portal.

    b. Kopiera **ACS URL** -värde, klistra in det här värdet i text rutan **SVARs-URL** i avsnittet **grundläggande SAML-konfiguration** i Azure Portal.

    c. Kopiera innehållet i text rutan **SP X. 509-certifikat (signering)** till anteckningar och spara den på din dator.

1. Klicka på **Ladda upp XML-fil** för att ladda upp **XML-** filen för federationsmetadata som du har kopierat från Azure Portal.

    ![ Ladda upp XML-fil](./media/linkedin-talent-solutions-tutorial/xml-file.png)

### <a name="create-linkedin-talent-solutions-test-user"></a>Skapa LinkedIn personal-lösningar test användare

I det här avsnittet skapas en användare som kallas Britta Simon i LinkedIn personal-lösningar. LinkedIn personal-lösningar har stöd för just-in-Time User-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i LinkedIn personal-lösningar skapas en ny efter autentisering.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

#### <a name="sp-initiated"></a>SP initierad:

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till LinkedIn personal-lösningar för att logga in på URL där du kan initiera inloggnings flödet.  

* Gå till LinkedIn personal-lösningar inloggnings-URL direkt och starta inloggnings flödet därifrån.

#### <a name="idp-initiated"></a>IDP initierad:

* Klicka på **testa det här programmet** i Azure Portal så bör du loggas in automatiskt på LinkedIn personal-lösningar för vilka du konfigurerar SSO 

Du kan också använda Microsoft Mina appar för att testa programmet i vilket läge som helst. När du klickar på panelen LinkedIn personal-lösningar i Mina appar, om det kon figurer ATS i SP-läge, omdirigeras du till programmets inloggnings sida för att initiera inloggnings flödet och om det kon figurer ATS i IDP-läge, bör du logga in automatiskt på de LinkedIn personal-lösningar som du ställer in SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat LinkedIn personal-lösningar kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).