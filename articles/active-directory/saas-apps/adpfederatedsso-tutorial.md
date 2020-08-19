---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med ADP | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och ADP.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/26/2019
ms.author: jeedes
ms.openlocfilehash: c170581496241ab3be0fe54bb484549ab4b1f976
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88542912"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-adp"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med ADP

I den här självstudien får du lära dig hur du integrerar ADP med Azure Active Directory (Azure AD). När du integrerar ADP med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till ADP.
* Gör det möjligt för användarna att logga in automatiskt till ADP med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Enkel inloggnings prenumeration med ADP (enkel inloggning).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* ADP stöder **IDP**-initierad enkel inloggning

> [!NOTE]
> ID för det här programmet är ett fast sträng värde så att endast en instans kan konfigureras i en klient.

## <a name="adding-adp-from-the-gallery"></a>Lägga till ADP från galleriet

För att konfigurera integreringen av ADP i Azure AD måste du lägga till ADP från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du **ADP** i sökrutan.
1. Välj **ADP** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-adp"></a>Konfigurera och testa enkel inloggning med Azure AD för ADP

Konfigurera och testa Azure AD SSO med ADP med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i ADP.

Om du vill konfigurera och testa Azure AD SSO med ADP, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
2. **[Konfigurera ADP SSO](#configure-adp-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa ADP-test användare](#create-adp-test-user)** – för att få en motsvarighet till B. Simon i ADP som är länkat till Azure AD-representation av användare.
3. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. Klicka på fliken **Egenskaper** på sidan för programintegrering med **ADP** på Azure-portalen och utför följande steg: 

    ![Egenskaper för enkel inloggning](./media/adpfederatedsso-tutorial/tutorial_adp_prop.png)

    a. Ange värdet för fältet **Aktiverat för användarinloggning** till **Ja**.

    b. Kopiera **URL:en för användaråtkomst** som du sedan ska klistra in i avsnittet **Konfigurera inloggnings-URL**, som beskrivs senare i självstudien.

    c. Ange värdet för fältet **Användartilldelning krävs** till **Ja**.

    d. Ange värdet för fältet **Synligt för användare** till **Nej**.

1. I [Azure Portal](https://portal.azure.com/)på sidan **ADP** -programintegration letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    I textrutan **Identifierare (entitets-ID)** anger du en URL: `https://fed.adp.com`

4. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. I avsnittet **Konfigurera ADP** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till ADP.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. Välj **ADP**i listan program.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-adp-sso"></a>Konfigurera ADP SSO

För att konfigurera enkel inloggning på **ADP**-sidan måste du ladda upp den nedladdade **XML:en med metadata** till [ADP-webbplatsen](https://adpfedsso.adp.com/public/login/index.fcc).

> [!NOTE]  
> Den här processen kan ta några dagar.

### <a name="configure-your-adp-services-for-federated-access"></a>Konfigurera dina ADP-tjänster för federerad åtkomst

>[!Important]
> Anställda som behöver federerad åtkomst till dina ADP-tjänster måste tilldelas till ADP-tjänstappen, och därefter måste användarna tilldelas till den specifika ADP-tjänsten.
När du har fått en bekräftelse av din ADP-representant konfigurerar du dina ADP-tjänster och tilldelar/hanterar användare för att styra användaråtkomsten till den specifika ADP-tjänsten.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du **ADP** i sökrutan.
1. Välj **ADP** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.
1. Klicka på fliken **Egenskaper** på sidan för programintegrering med **ADP** på Azure-portalen och utför följande steg:  

    ![Länkade egenskaper för enkel inloggning](./media/adpfederatedsso-tutorial/tutorial_adp_linkedproperties.png)

    a.  Ange värdet för fältet **Aktiverat för användarinloggning** till **Ja**.

    b.  Ange värdet för fältet **Användartilldelning krävs** till **Ja**.

    c.  Ange värdet för fältet **Synligt för användare** till **Ja**.

1. I [Azure Portal](https://portal.azure.com/)på sidan **ADP** -programintegration letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.

1. Välj **Läge** som **Länkat** i dialogrutan **Välj en metod för enkel inloggning**. för att länka ditt program till **ADP**.

    ![Länkat för enkel inloggning](./media/adpfederatedsso-tutorial/tutorial_adp_linked.png)

1. Navigera till avsnittet **Konfigurera inloggnings-URL** och utför följande steg:

    ![Egenskaper för enkel inloggning](./media/adpfederatedsso-tutorial/tutorial_adp_linkedsignon.png)

    a. Klistra in **URL:en för användaråtkomst**, som du har kopierat från fliken **Egenskaper** ovan (från ADP-huvudappen).
                                                             
    b. Här är de fem appar som stöder olika **URL:er för vidarebefordringsstatus**. Du måste lägga till lämpligt värde för **URL:en för vidarebefordringsstatus** för ett visst program manuellt i **URL:en för användaråtkomst**.
    
    * **ADP Workforce Now**
        
        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?WFN`

    * **ADP Workforce Now Enhanced Time**
        
        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?EETDC2`
    
    * **ADP Vantage HCM**
        
        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?ADPVANTAGE`

    * **ADP Enterprise HR**

        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?PORTAL`

    * **MyADP**

        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?REDBOX`

9. **Spara** ändringarna.

10. När du har fått en bekräftelse från din ADP-representant börjar du testa med en eller två användare.

    a. Tilldela några användare till ADP-tjänstappen för att testa federerad åtkomst.

    b. Testningen lyckas om användarna kommer åt ADP-tjänstappen i galleriet och kan komma åt ADP-tjänsten.
 
11. När du har bekräftat att testningen lyckades tilldelar du den federerade ADP-tjänsten till enskilda användare eller användargrupper, vilket beskrivs senare i självstudien och distribuerar den till dina anställda.

### <a name="create-adp-test-user"></a>Skapa ADP-testanvändare

Syftet med det här avsnittet är att skapa en användare som kallas B. Simon i ADP. Arbeta med [ADP-supportteamet](https://www.adp.com/contact-us/overview.aspx) för att lägga till användarna till ADP-kontot. 

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på ADP-panelen på åtkomstpanelen bör du automatiskt loggas in i ADP-programmet som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova ADP med Azure AD](https://aad.portal.azure.com)
