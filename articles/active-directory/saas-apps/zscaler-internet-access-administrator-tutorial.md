---
title: 'Självstudie: Azure Active Directory integrering med Zscaler-administratör för Internet åtkomst | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Zscaler Internet Access Administrator.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.openlocfilehash: 0cbcfb65d7c7ac71b17e38dd72545a3d8fd2f162
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88545893"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-internet-access-administrator"></a>Självstudie: Azure Active Directory integrering med Zscaler-administratör för Internet åtkomst

I den här självstudien får du lära dig hur du integrerar Zscaler Internet Access Administrator med Azure Active Directory (AD Azure).
När du integrerar Zscaler Internet Access Administrator med Azure AD får du följande fördelar:

* I Azure AD kan du styra vem som har åtkomst till Zscaler Internet Access Administrator.
* Du kan göra så att dina användare automatiskt loggas in på Zscaler Internet Access Administrator (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Zscaler Internet Access Administrator behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Zscaler Internet Access Administrator-prenumeration

> [!NOTE]
> Den här integreringen är också tillgänglig för användning från Azure AD amerikanska myndigheters moln miljö. Du hittar det här programmet i Cloud App-galleriet för Azure AD amerikanska myndigheter och konfigurerar det på samma sätt som du gör från det offentliga molnet.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Zscaler Internet Access Administrator har stöd för **IDP**-initierad SSO

## <a name="adding-zscaler-internet-access-administrator-from-the-gallery"></a>Lägger till Zscaler Internet Access Administrator från galleriet

För att konfigurera integrering av Zscaler Internet Access Administrator till Azure AD måste du lägga till Zscaler Internet Access Administrator från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Zscaler Internet Access Administrator från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Zscaler Internet Access Administrator**, väljer **Zscaler Internet Access Administrator** i resultatpanelen och klickar på **Lägg till** för att lägga till programmet.

     ![Zscaler Internet Access Administrator i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Zscaler Internet Access Administrator baserat på en testanvändare som heter **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Zscaler Internet Access Administrator upprättas.

Om du vill konfigurera och testa enkel inloggning i Azure AD med Zscaler Internet Access Administrator måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för Zscaler Internet Access Administrator](#configure-zscaler-internet-access-administrator-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa testanvändare för Zscaler Internet Access Administrator](#create-zscaler-internet-access-administrator-test-user)** – du har en motsvarighet till Britta Simon i Zscaler Internet Access Administrator som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Om du vill konfigurera enkel inloggning med Azure AD med Zscaler Internet Access Administrator behöver du följande objekt:

1. På [Azure-portalen](https://portal.azure.com/)på programintegreringssidan **Zscaler Internet Access Administrator** markerar du **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. På sidan **Konfigurera enkel inloggning med SAML** klickar du på knappen **Redigera** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Zscaler Internet Access Administrator-domän och webbadresser med information om enkel inloggning](common/idp-intiated.png)

    a. I textrutan **Identifierare** skriver du en URL enligt dina behov:

    | Identifierare |
    |------------|
    | `https://admin.zscaler.net` |
    | `https://admin.zscalerone.net` |
    | `https://admin.zscalertwo.net` |
    | `https://admin.zscalerthree.net` |
    | `https://admin.zscloud.net` |
    | `https://admin.zscalerbeta.net` |

    b. I textrutan **Svars-URL** skriver du en URL enligt dina behov:

    | Svars-URL |
    |-----------|
    | `https://admin.zscaler.net/adminsso.do` |
    | `https://admin.zscalerone.net/adminsso.do` |
    | `https://admin.zscalertwo.net/adminsso.do` |
    | `https://admin.zscalerthree.net/adminsso.do` |
    | `https://admin.zscloud.net/adminsso.do` |
    | `https://admin.zscalerbeta.net/adminsso.do` |

5. Programmet Zscaler Internet Access Administrator förväntar sig SAML-påståenden i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut i avsnittet om **användarattribut och anspråk** på sidan för programintegrering. På sidan **Konfigurera enkel inloggning med SAML** klickar du på knappen **Redigera** för att öppna dialogrutan för **användarattribut och anspråk**.

    ![Länken Attribut](./media/zscaler-internet-access-administrator-tutorial/tutorial_zscaler-internet_attribute.png)

6. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** konfigurerar du SAML-tokenattributet på det sätt som visas i bilden ovan och utför följande steg:

    | Name  | Källattribut  |
    | ---------| ------------ |
    | Roll     | user.assignedroles |

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![image](./common/new-save-attribute.png)
    
    ![image](./common/new-attribute-details.png)

    b. Från listan över **Källattribut** väljer du attributvärdet.

    c. Klicka på **OK**.

    d. Klicka på **Spara**.

    > [!NOTE]
    > Klicka [här](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management) för att få veta hur du konfigurerar en Roll i Azure AD

7. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

8. I avsnittet **Konfigurera Zscaler Internet Access Administrator** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-zscaler-internet-access-administrator-single-sign-on"></a>Konfigurera enkel inloggning för Zscaler Internet Access Administrator

1. I ett annat webbläsarfönster loggar du in på ditt Zscaler Internet Access Admin-användargränssnitt.

2. Gå till **Administration > Administratörshantering**, utför följande steg och klicka på Spara:

    ![Administration](./media/zscaler-internet-access-administrator-tutorial/AdminSSO.png "Administration")

    a. Markera **Aktivera SAML-autentisering**.

    b. Klicka på **Ladda upp** för att ladda upp Azure SAML-signeringscertifikatet som du har laddat ned från Azure-portalen i det **offentliga SSL-certifikatet**.

    c. Om du vill ha ytterligare säkerhet kan du lägga till information om **Utfärdare** för att verifiera utfärdaren av SAML-svaret.

3. I användargränssnittet för administration utför du följande steg:

    ![Administration](./media/zscaler-internet-access-administrator-tutorial/ic800207.png)

    a. Hovra över menyn **Aktivering** längst ned till vänster.

    b. Klicka på **Aktivera**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **användar namn** skriver du **brittasimon \@ yourcompanydomain. extension**  
    Till exempel BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska göra det möjligt för Britta Simon att använda Azure enkel inloggning genom att ge åtkomst till Zscaler Internet Access Administrator.

1. På Azure-portalen väljer du **Företagsprogram**, väljer **Alla program** och sedan **Zscaler Internet Access Administrator**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program anger och väljer du **Zscaler Internet Access Administrator**.

    ![Länken Zscaler Internet Access Administrator i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-zscaler-internet-access-administrator-test-user"></a>Skapa testanvändare för Zscaler Internet Access Administrator

Målet med det här avsnittet är att skapa en användare som heter Britta Simon i Zscaler Internet Access Administrator. Zscaler Internet-åtkomst har inte stöd för just-in-time-etablering för enkel inloggning för administratör. Du måste manuellt skapa ett administratörskonto.
Anvisningar om hur du skapar ett administratörskonto finns i Zscaler-dokumentationen:

https://help.zscaler.com/zia/adding-admins

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Zscaler Internet Access Administrator-panelen i åtkomstpanelen bör du automatiskt loggas in på Zscaler Internet Access Administrator som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
