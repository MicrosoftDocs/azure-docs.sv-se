---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med PurelyHR | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och PurelyHR.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: jeedes
ms.openlocfilehash: 48db08f856407b2ceba32798ed0e39eab967b72d
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88553323"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-purelyhr"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med PurelyHR

I den här självstudien får du lära dig hur du integrerar PurelyHR med Azure Active Directory (Azure AD). När du integrerar PurelyHR med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till PurelyHR.
* Gör det möjligt för användarna att logga in automatiskt till PurelyHR med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* PurelyHR för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* PurelyHR stöder **SP-och IDP** -INITIERAd SSO
* PurelyHR stöder **just-in-Time** User-etablering

## <a name="adding-purelyhr-from-the-gallery"></a>Lägga till PurelyHR från galleriet

Om du vill konfigurera integreringen av PurelyHR i Azure AD måste du lägga till PurelyHR från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **PurelyHR** i sökrutan.
1. Välj **PurelyHR** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-purelyhr"></a>Konfigurera och testa enkel inloggning med Azure AD för PurelyHR

Konfigurera och testa Azure AD SSO med PurelyHR med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i PurelyHR.

Om du vill konfigurera och testa Azure AD SSO med PurelyHR, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    * **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera PURELYHR SSO](#configure-purelyhr-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    * **[Skapa PurelyHR test User](#create-purelyhr-test-user)** -om du vill ha en motsvarighet till B. Simon i PurelyHR som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **PurelyHR** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

    Skriv en URL i text rutan **svars-URL** med följande mönster:  `https://<companyID>.purelyhr.com/sso-consume`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<companyID>.purelyhr.com/sso-initiate`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med faktisk svars-URL och inloggnings-URL. Kontakta [PurelyHR client support team](https://support.purelyhr.com/) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera PurelyHR** kopierar du lämpliga URL: er baserat på ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare**och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till PurelyHR.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **PurelyHR**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-purelyhr-sso"></a>Konfigurera PurelyHR SSO

1. Om du vill automatisera konfigurationen i PurelyHR måste du installera **Mina appar säker inloggnings webb läsar tillägg** genom att klicka på **installera tillägget**.

    ![Mina Apps-tillägg](common/install-myappssecure-extension.png)

1. När du har lagt till tillägg i webbläsaren kan du klicka på **Konfigurera PurelyHR** för att dirigera dig till PurelyHR-programmet. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på PurelyHR. Webb läsar tillägget kommer automatiskt att konfigurera programmet åt dig och automatisera steg 3-5.

    ![Konfigurera konfiguration](common/setup-sso.png)

1. Om du vill konfigurera PurelyHR manuellt öppnar du ett nytt webbläsarfönster och loggar in på din PurelyHR-företags webbplats som administratör och utför följande steg:

1. Öppna **instrument panelen** från alternativen i verktygsfältet och klicka på **SSO-inställningar**.

1. Klistra in värdena i rutorna enligt beskrivningen nedan –

    ![Konfigurera enkel inloggning](./media/purelyhr-tutorial/purelyhr-dashboard-sso-settings.png)  

    a. Öppna **certifikatet (Bas64)** som laddats ned från Azure Portal i anteckningar och kopiera certifikatets värde. Klistra in det kopierade värdet i rutan **X. 509-certifikat** .

    b. I rutan **IDP Issuer-URL** klistrar du in **Azure AD-identifieraren** som kopierats från Azure Portal.

    c. I rutan **URL för IDP-slutpunkt** klistrar du in **inloggnings-URL:** en som kopierats från Azure Portal. 

    d. Markera kryss rutan **skapa användare automatiskt** om du vill aktivera automatisk användar etablering i PurelyHR.

    e. Spara inställningarna genom att klicka på **Spara ändringar** .

### <a name="create-purelyhr-test-user"></a>Skapa PurelyHR test användare

Det här steget krävs vanligt vis inte eftersom programmet bara stöder användar etablering i tid. Om den automatiska användar etableringen inte är aktive rad kan du utföra manuell skapande av användare enligt beskrivningen nedan.

Logga in på din Velpic SAML-företags webbplats som administratör och utför följande steg:

1. Klicka på fliken Hantera och gå till användare-avsnittet och klicka på knappen nytt för att lägga till användare.

    ![Lägg till användare](./media/velpicsaml-tutorial/velpic_7.png)

2. Utför följande steg på dialog sidan **Skapa ny användare** .

    ![användare](./media/velpicsaml-tutorial/velpic_8.png)

    a. Skriv det första namnet på B i text rutan för **förnamn** .

    b. I text rutan **efter namn** skriver du det sista namnet på Simon.

    c. Skriv användar namnet för B. Simon i text rutan **användar namn** .

    d. Skriv e-postadressen för kontot i text rutan för **e-post** B.Simon@contoso.com .

    e. Resten av informationen är valfri, du kan fylla det om det behövs.

    f. Klicka på **Spara**.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen PurelyHR på åtkomst panelen, bör du loggas in automatiskt på den PurelyHR som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova PurelyHR med Azure AD](https://aad.portal.azure.com/)
