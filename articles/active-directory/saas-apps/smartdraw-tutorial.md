---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med SmartDraw | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SmartDraw.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/02/2020
ms.author: jeedes
ms.openlocfilehash: f99993792c2d5f4300025f26d8409207382553d9
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88527629"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-smartdraw"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med SmartDraw

I den här självstudien får du lära dig hur du integrerar SmartDraw med Azure Active Directory (Azure AD). När du integrerar SmartDraw med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till SmartDraw.
* Gör det möjligt för användarna att logga in automatiskt till SmartDraw med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* SmartDraw för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* SmartDraw stöder **SP-och IDP** -INITIERAd SSO
* SmartDraw stöder **just-in-Time** User-etablering

## <a name="adding-smartdraw-from-the-gallery"></a>Lägga till SmartDraw från galleriet

Om du vill konfigurera integreringen av SmartDraw i Azure AD måste du lägga till SmartDraw från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **SmartDraw** i sökrutan.
1. Välj **SmartDraw** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-smartdraw"></a>Konfigurera och testa enkel inloggning med Azure AD för SmartDraw

Konfigurera och testa Azure AD SSO med SmartDraw med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i SmartDraw.

Om du vill konfigurera och testa Azure AD SSO med SmartDraw, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    * **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera SMARTDRAW SSO](#configure-smartdraw-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    * **[Skapa SmartDraw test User](#create-smartdraw-test-user)** -om du vill ha en motsvarighet till B. Simon i SmartDraw som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **SmartDraw** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** behöver användaren inte utföra några steg eftersom appen redan är förintegrerad med Azure.

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://cloud.smartdraw.com/sso/saml/login/<domain>`

    > [!NOTE]
    > Inloggnings-URL-värdet är inte verkligt. Du uppdaterar värdet för inloggnings-URL med den faktiska inloggnings-URL:en, vilket förklaras senare i självstudien. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. Klicka på **Spara**.

1. SmartDraw-programmet förväntar sig SAML-intyg i ett särskilt format, vilket innebär att du kan lägga till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Utöver ovan förväntar sig SmartDraw-programmet att fler attribut skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda, men du kan granska dem enligt dina krav.

    | Name | Källattribut|
    | ---------------| --------------- |
    | FirstName | user.givenname |
    | LastName | user.surname |
    | E-post | user.mail |
    | Grupper | användare. grupper |

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. I avsnittet **Konfigurera SmartDraw** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till SmartDraw.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **SmartDraw**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-smartdraw-sso"></a>Konfigurera SmartDraw SSO

1. Om du vill automatisera konfigurationen i SmartDraw måste du installera **Mina appar säker inloggnings webb läsar tillägg** genom att klicka på **installera tillägget**.

    ![Mina Apps-tillägg](common/install-myappssecure-extension.png)

1. När du har lagt till tillägg i webbläsaren kan du klicka på **Konfigurera SmartDraw** för att dirigera dig till SmartDraw-programmet. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på SmartDraw. Webb läsar tillägget kommer automatiskt att konfigurera programmet åt dig och automatisera steg 3-5.

    ![Konfigurera konfiguration](common/setup-sso.png)

1. Om du vill konfigurera SmartDraw manuellt öppnar du ett nytt webbläsarfönster och loggar in på din SmartDraw-företags webbplats som administratör och utför följande steg:

1. Klicka på **enkel inloggning** under hantera din SmartDraw-licens.

    ![SmartDraw-konfiguration](./media/smartdraw-tutorial/configure01.png)

1. Utför följande steg på sidan konfiguration:

    ![SmartDraw-konfiguration](./media/smartdraw-tutorial/configure02.png)

    a. Skriv din domän i text rutan **din domän (t. ex. Acme.com)** .

    b. Kopiera **din inloggnings-URL för SP-initierad inloggning** för din instans och klistra in den i text rutan för inloggnings-URL i **grundläggande SAML-konfiguration** på Azure Portal.

    c. Skriv **alla**i rutan **säkerhets grupper för att tillåta SmartDraw-åtkomst** .

    d. I text rutan **URL för SAML-utfärdare** klistrar du in värdet för **Azure AD-identifieraren** som du kopierade från Azure Portal.

    e. I anteckningar öppnar du XML-filen med metadata som du laddade ned från Azure Portal, kopierar innehållet och klistrar in det i rutan **med dina SAML-metadata** .

    f. Klicka på **Spara konfiguration** 

### <a name="create-smartdraw-test-user"></a>Skapa SmartDraw test användare

I det här avsnittet skapas en användare som heter B. Simon i SmartDraw. SmartDraw stöder just-in-Time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i SmartDraw skapas en ny efter autentiseringen.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen SmartDraw på åtkomst panelen, bör du loggas in automatiskt på den SmartDraw som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova SmartDraw med Azure AD](https://aad.portal.azure.com/)