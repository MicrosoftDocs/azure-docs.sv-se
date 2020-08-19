---
title: 'Självstudie: Azure Active Directory-integrering med Zscaler Private Access (ZPA) | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Zscaler Private Access (ZPA).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/23/2019
ms.author: jeedes
ms.openlocfilehash: 8c7347aabb3aa6f122ea82a46ad8a09f53e271bf
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88545700"
---
# <a name="tutorial-integrate-zscaler-private-access-zpa-with-azure-active-directory"></a>Självstudie: integrera Zscaler Private Access (ZPA) med Azure Active Directory

I den här självstudien får du lära dig hur du integrerar Zscaler Private Access (ZPA) med Azure Active Directory (Azure AD). När du integrerar Zscaler Private Access (ZPA) med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Zscaler Private Access (ZPA).
* Gör det möjligt för användarna att logga in automatiskt till Zscaler Private Access (ZPA) med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Zscaler Private Access (ZPA)-prenumeration med enkel inloggning aktive rad (SSO).

> [!NOTE]
> Den här integreringen är också tillgänglig för användning från Azure AD amerikanska myndigheters moln miljö. Du hittar det här programmet i Cloud App-galleriet för Azure AD amerikanska myndigheter och konfigurerar det på samma sätt som du gör från det offentliga molnet.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö. Zscaler Private Access (ZPA) stöder **SP** -initierad SSO.

## <a name="adding-zscaler-private-access-zpa-from-the-gallery"></a>Lägga till Zscaler Private Access (ZPA) från galleriet

Om du vill konfigurera integrationen av Zscaler Private Access (ZPA) i Azure AD måste du lägga till Zscaler Private Access (ZPA) från galleriet till listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **Zscaler Private Access (ZPA)** i sökrutan.
1. Välj **Zscaler Private Access (ZPA)** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

Konfigurera och testa Azure AD SSO med Zscaler Private Access (ZPA) med hjälp av en test användare som kallas **Britta Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Zscaler Private Access (ZPA).

Slutför följande Bygg stenar om du vill konfigurera och testa Azure AD SSO med Zscaler Private Access (ZPA):

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** så att användarna kan använda den här funktionen.
2. **[Konfigurera Zscaler Private Access (ZPA)](#configure-zscaler-private-access-zpa)** för att konfigurera SSO-inställningarna på program sidan.
3. **[Skapa en Azure AD test-användare](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** för att aktivera Britta Simon att använda enkel inloggning i Azure AD.
5. **[Skapa Zscaler Private Access (ZPA)-test användare](#create-zscaler-private-access-zpa-test-user)** för att få en motsvarighet till Britta Simon i Zscaler Private Access (ZPA) som är länkad till Azure AD-representation av användare.
6. **[Testa SSO](#test-sso)** för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. På sidan [Azure Portal](https://portal.azure.com/), på sidan för **Zscaler Private Access (ZPA)** , letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. På sidan **grundläggande SAML-konfiguration** anger du värden för följande fält:

    1. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://samlsp.private.zscaler.com/auth/login?domain=<your-domain-name>`

    1. I text rutan **identifierare (enhets-ID)** anger du en URL: `https://samlsp.private.zscaler.com/auth/metadata`

    > [!NOTE]
    > Värdet för **inloggnings-URL** är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [Zscaler Private Access (ZPA)-klient support teamet](https://help.zscaler.com/zpa-submit-ticket) för att hämta värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

   ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. I avsnittet **Konfigurera Zscaler Private Access (ZPA)** kopierar du lämpliga URL: er baserat på ditt krav.

   ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-zscaler-private-access-zpa"></a>Konfigurera Zscaler Private Access (ZPA)

1. Om du vill automatisera konfigurationen i Zscaler Private Access (ZPA) måste du installera **webb läsar tillägget Mina appar säkra inloggnings webbläsare** genom att klicka på **installera tillägget**.

    ![Mina Apps-tillägg](common/install-myappssecure-extension.png)

2. När du har lagt till tillägg i webbläsaren, klickar du på **Konfigurera Zscaler Private Access (ZPA)** för att dirigera dig till Zscaler Private Access-programmet (ZPA). Därifrån anger du administratörsautentiseringsuppgifter för att logga in på Zscaler Private Access (ZPA). Webb läsar tillägget kommer automatiskt att konfigurera programmet åt dig och automatisera steg 3-6.

    ![Konfigurera konfiguration](common/setup-sso.png)

3. Om du vill konfigurera Zscaler Private Access (ZPA) manuellt öppnar du ett nytt webbläsarfönster och loggar in på företags platsen Zscaler Private Access (ZPA) som administratör och utför följande steg:

4. Klicka på **Administration** och gå till avsnittet **autentisering** på den vänstra sidan av menyn och klicka på **IDP konfiguration**.

    ![Administratörs administration för Zscaler Private Access](./media/zscalerprivateaccess-tutorial/tutorial-zscaler-private-access-administration.png)

5. Klicka på **Lägg till IDP-konfiguration**i det övre högra hörnet. 

    ![Zscaler Private Access Administrator IDP](./media/zscalerprivateaccess-tutorial/tutorial-zscaler-private-access-idp.png)

6. På sidan **Lägg till konfiguration av IDP** utför du följande steg:
 
    ![Zscaler Private Access Administrator Select](./media/zscalerprivateaccess-tutorial/tutorial-zscaler-private-access-select.png)

    a. Klicka på **Välj fil** för att ladda upp den hämtade metadatafilen från Azure AD i fältet **IDP för fil överföring i metadata** .

    b. Den läser **IDP metadata** från Azure AD och fyller all fält information som visas nedan.

    ![Administratörs konfiguration för Zscaler Private Access](./media/zscalerprivateaccess-tutorial/config.png)

    c. Välj din domän från fältet **domäner** .
    
    d. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas Britta Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare**och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `Britta Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Till exempel `BrittaSimon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Zscaler Private Access (ZPA).

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **Zscaler Private Access (ZPA)**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **Britta Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

### <a name="create-zscaler-private-access-zpa-test-user"></a>Skapa test användare för Zscaler Private Access (ZPA)

I det här avsnittet skapar du en användare som heter Britta Simon i Zscaler Private Access (ZPA). Arbeta med [Zscaler Private Access (ZPA) support team](https://help.zscaler.com/zpa-submit-ticket) för att lägga till användare i Zscaler Private Access-plattformen (ZPA).

### <a name="test-sso"></a>Testa SSO

När du väljer panelen Zscaler Private Access (ZPA) i åtkomst panelen, bör du loggas in automatiskt på Zscaler Private Access (ZPA) som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)