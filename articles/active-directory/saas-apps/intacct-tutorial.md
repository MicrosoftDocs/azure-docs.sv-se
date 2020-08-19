---
title: 'Självstudie: Azure Active Directory integrering med Sage Intacct | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Sage Intacct.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/05/2020
ms.author: jeedes
ms.openlocfilehash: 79a77d3c55ee4ac545712329f0054123bec8fcae
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88535889"
---
# <a name="tutorial-integrate-sage-intacct-with-azure-active-directory"></a>Självstudie: integrera Sage Intacct med Azure Active Directory

I den här självstudien får du lära dig att integrera Sage-Intacct med Azure Active Directory (Azure AD). När du integrerar Sage-Intacct med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Sage-Intacct.
* Gör det möjligt för användarna att logga in automatiskt till Sage Intacct med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Sage Intacct-prenumeration med enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Sage Intacct stöder **IDP** INITIERAd SSO
* När du har konfigurerat Sage Intacct kan du framtvinga sessionshantering, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-sage-intacct-from-the-gallery"></a>Lägga till Sage Intacct från galleriet

Om du vill konfigurera integreringen av Sage-Intacct i Azure AD måste du lägga till Sage Intacct från galleriet till listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **Sage Intacct** i sökrutan.
1. Välj **Sage Intacct** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-sage-intacct"></a>Konfigurera och testa Azure AD SSO för Sage-Intacct

Konfigurera och testa Azure AD SSO med Sage Intacct med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Sage Intacct.

Om du vill konfigurera och testa Azure AD SSO med Sage Intacct slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
2. **[Konfigurera Sage INTACCT SSO](#configure-sage-intacct-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa Sage Intacct test User](#create-sage-intacct-test-user)** – om du vill ha en motsvarighet till B. Simon i Sage Intacct som är länkad till Azure AD-representation av användare.
6. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)på sidan **Sage Intacct** Application Integration letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    Skriv en URL i text rutan **svars-URL** :  `https://www.intacct.com/ia/acct/sso_response.phtml`

1. Sage Intacct-programmet förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut. Klicka på **Redigera** ikon för att öppna dialog rutan användarattribut..

    ![image](common/edit-attribute.png)

1. Utöver ovan förväntar sig Sage Intacct-program att fler attribut skickas tillbaka i SAML-svar. I dialog rutan **användarattribut & anspråk** utför du följande steg för att lägga till SAML-token-attributet som visas i tabellen nedan:

    | Attributnamn  |  Källattribut|
    | ---------------| --------------- |
    | Företagsnamn | **Sage Intacct företags-ID** |
    | name | Värdet ska vara detsamma som Sage Intacct **-användar-ID**, som du anger i **avsnittet Create Sage Intacct test User**, som beskrivs senare i självstudien |

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Lämna **Namnrymd** tom.

    d. Välj Källa som **Attribut**.

    e. I listan **källattribut** skriver eller väljer du det attributvärde som visas för den raden.

    f. Klicka på **OK**

    ex. Klicka på **Spara**.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera Sage Intacct** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Sage Intacct.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **Sage Intacct**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-sage-intacct-sso"></a>Konfigurera Sage Intacct SSO

1. Logga in på din Sage Intacct-företags webbplats som administratör i ett annat webbläsarfönster.

1. Klicka på fliken **företag** och klicka sedan på **företags information**.

    ![Firm](./media/intacct-tutorial/ic790037.png "Företag")

1. Klicka på fliken **säkerhet** och klicka sedan på **Redigera**.

    ![Säkerhet](./media/intacct-tutorial/ic790038.png "Säkerhet")

1. I avsnittet **enkel inloggning (SSO)** utför du följande steg:

    ![Enkel inloggning](./media/intacct-tutorial/ic790039.png "enkel inloggning")

    a. Välj **aktivera enkel inloggning**.

    b. Som **typ av identitetsprovider**väljer du **SAML 2,0**.

    c. I text rutan **utfärdar-URL** klistrar du in värdet för **Azure AD-identifierare**, som du har kopierat från Azure Portal.

    d. I text rutan för **inloggnings-URL** klistrar du in värdet för **inloggnings-URL: en**som du har kopierat från Azure Portal.

    e. Öppna ditt **bas-64-** kodade certifikat i anteckningar, kopiera innehållet i det till Urklipp och klistra in det i rutan **certifikat** .

    f. Klicka på **Spara**.

### <a name="create-sage-intacct-test-user"></a>Skapa Sage Intacct-test användare

Om du vill konfigurera Azure AD-användare så att de kan logga in på Sage Intacct, måste de tillhandahållas i Sage Intacct. För Sage-Intacct är etableringen en manuell uppgift.

**Utför följande steg för att etablera användar konton:**

1. Logga in på din **Sage Intacct** -klient.

1. Klicka på fliken **företag** och sedan på **användare**.

    ![Användare](./media/intacct-tutorial/ic790041.png "Användare")

1. Klicka på fliken **Lägg till** .

    ![Lägg till](./media/intacct-tutorial/ic790042.png "Lägg till")

1. I avsnittet **användar information** utför du följande steg:

    ![Användar information](./media/intacct-tutorial/ic790043.png "Användar information")

    a. Ange **användar-ID**, **efter namn**, **förnamn**, **e-postadress**, **titel**och **telefon** för ett Azure AD-konto som du vill etablera i avsnittet **användar information** .

    > [!NOTE]
    > Kontrol lera att **användar-ID: t** i ovanstående skärm bild och **källattributet** som är mappat till attributet **name** i avsnittet **användarattribut** i Azure Portal ska vara detsamma.

    b. Välj **administratörs behörighet** för ett Azure AD-konto som du vill etablera.

    c. Klicka på **Spara**. 
    
    d. Azure AD-kontonamnet tar emot ett e-postmeddelande och följer en länk för att bekräfta sitt konto innan det blir aktivt.

1. Klicka på fliken **enkel inloggning** och kontrol lera att **användar-ID: t för federerad SSO** i skärm bilden nedan och **källattributet** som är mappat med `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` i avsnittet **användarattribut** i Azure Portal ska vara detsamma.

    ![Användar information](./media/intacct-tutorial/ic790044.png "Användar information")

> [!NOTE]
> För att etablera Azure AD-användarkonton kan du använda andra Sage-Intacct för att skapa användar konton eller API: er som tillhandahålls av Sage Intacct.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Sage Intacct på åtkomst panelen, bör du loggas in automatiskt på Sage-Intacct som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vad är session Control i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
