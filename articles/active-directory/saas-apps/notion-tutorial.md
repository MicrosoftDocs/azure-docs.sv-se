---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med begreppet | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och begreppet.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/05/2021
ms.author: jeedes
ms.openlocfilehash: 520eb25bcb138c96b24166816d3374255fb7c3b2
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493996"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-notion"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med begreppet

I den här självstudien får du lära dig att integrera begreppet med Azure Active Directory (Azure AD). När du integrerar begreppet med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till begreppet.
* Gör det möjligt för användarna att bli inloggade automatiskt för att definiera sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Begrepps aktive rad-prenumeration med enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Begreppet stöder **SP-och IDP** -INITIERAd SSO
* Begreppet stöder **just-in-Time** User-etablering
> [!NOTE]
> ID för det här programmet är ett fast sträng värde så att en teoretisk arbets yta kan konfigureras i en klient.


## <a name="adding-notion-from-the-gallery"></a>Lägga till begrepp från galleriet

Om du vill konfigurera en integrering av begreppet i Azure AD måste du lägga till begreppet från galleriet i listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. Skriv **begreppet** i rutan Sök i avsnittet **Lägg till från Galleri** .
1. Välj **begreppet** på resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-sso-for-notion"></a>Konfigurera och testa Azure AD SSO för begrepp

Konfigurera och testa Azure AD SSO med begreppet med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i begreppet.

Utför följande steg för att konfigurera och testa Azure AD SSO med begreppet:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera begrepp](#configure-notion-sso)** för enkel inloggning – för att konfigurera inställningar för enkel inloggning på program sidan.
    1. **[Skapa en teoretisk test användare](#create-notion-test-user)** – om du vill ha en motsvarighet till B. Simon i begreppet som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal **går du till sidan Definiera program** integration och letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

    I text rutan **svars-URL** anger du URL: en med följande mönster som du kan hämta från dina användardefinierade inställningar för arbets ytan **& medlemmar** > **säkerhet & ID** > **för enkel inloggnings-URL**:  `https://www.notion.so/sso/saml/<CUSTOM_ID>`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I text rutan **inloggnings-URL** anger du följande URL:  `https://www.notion.so/login`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med faktisk svars-URL och inloggnings-URL. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. I definitions programmet förväntas SAML-kontroller i ett särskilt format, vilket kräver att du lägger till anpassade attribut mappningar i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Förutom ovan förväntar sig ett teoretiskt program att få fler attribut att skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda, men du kan granska dem enligt dina krav.
    
    | Name |  Källattribut|
    | ----------- | --------- |
    | e-post | user.mail |
    | firstName | user.givenname |
    | lastName | user.surname |


1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , klickar du på Kopiera för att kopiera URL för **metadata för app Federation**. Gå till din **begrepps** arbets yta **Inställningar & medlemmar**  >  **säkerhet & identitet** och klistra in värdet som du kopierade i fältet **IDP metadata URL** .

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till begreppet.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **begrepp**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-notion-sso"></a>Konfigurera begrepp för enkel inloggning

Gå till din **begrepps** arbets yta **Inställningar & medlemmar**  >  **säkerhet & identitet** och klistra in URL-värdet för **appens federationsmetadata** som du kopierade till fältet **IDP metadata URL** .

På samma inställnings sida, under **e-postdomäner** klickar du på **kontakta support** för att lägga till din organisations e-postdomän.

När dina e-postdomäner har godkänts och lagts till aktiverar du SAML SSO med **Aktivera SAML** -växling.

När testet är klart kan du använda SAML SSO med hjälp av **tvingande SAML** -växling. Observera att din teoretiska arbets yta administrastrators behåller möjligheten att logga in med e-post, men alla andra medlemmar måste använda SAML SSO för att logga in på begreppet.

### <a name="create-notion-test-user"></a>Skapa en teoretisk test användare

I det här avsnittet skapas en användare som heter Britta Simon i begreppet. Begreppet stöder just-in-Time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i begreppet skapas en ny efter autentiseringen.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

#### <a name="sp-initiated"></a>SP initierad:

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till en teoretisk inloggnings-URL där du kan starta inloggnings flödet.  

* Gå till den teoretiska inloggnings-URL: en direkt och starta inloggnings flödet därifrån.

#### <a name="idp-initiated"></a>IDP initierad:

* Klicka på **testa det här programmet** i Azure Portal så bör du loggas in automatiskt på det begrepp som du konfigurerade SSO för. 

Du kan också använda Microsoft Mina appar för att testa programmet i vilket läge som helst. När du klickar på den teoretiska panelen i Mina appar, om det kon figurer ATS i SP-läge, kommer du att omdirigeras till programmets inloggnings sida för att initiera inloggnings flödet och om det kon figurer ATS i IDP-läge, bör du logga in automatiskt på det begrepp som du ställer in SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Nästa steg

När du har konfigurerat begreppet kan du framtvinga kontroll av sessioner, vilket skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).