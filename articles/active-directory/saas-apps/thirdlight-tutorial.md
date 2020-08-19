---
title: 'Självstudie: Azure Active Directory integrering med ThirdLight | Microsoft Docs'
description: I den här självstudien får du lära dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och ThirdLight.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 3193df0848c0d67935faa0020465e79bd50e2151
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88544986"
---
# <a name="tutorial-azure-active-directory-integration-with-thirdlight"></a>Självstudie: Azure Active Directory integrering med ThirdLight

I den här självstudien får du lära dig hur du integrerar ThirdLight med Azure Active Directory (Azure AD). Den här integrationen ger följande fördelar:

* Du kan använda Azure AD för att kontrol lera vem som har åtkomst till ThirdLight.
* Du kan göra det möjligt för användarna att logga in automatiskt på ThirdLight (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats: Azure Portal.

Om du vill veta mer om SaaS-appens integrering med Azure AD kan du läsa [enkel inloggning till program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med ThirdLight måste du ha:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* En ThirdLight-prenumeration med enkel inloggning aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning i Azure AD i en test miljö.

* ThirdLight stöder SP-initierad SSO.

## <a name="add-thirdlight-from-the-gallery"></a>Lägg till ThirdLight från galleriet

Om du vill ställa in integreringen av ThirdLight i Azure AD måste du lägga till ThirdLight från galleriet i listan över hanterade SaaS-appar.

1. I [Azure Portal](https://portal.azure.com)väljer du **Azure Active Directory**i den vänstra rutan:

    ![Välj Azure Active Directory](common/select-azuread.png)

2. Gå till **företags program**  >  **alla program**:

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett program väljer du **nytt program** överst i fönstret:

    ![Välj nytt program](common/add-new-app.png)

4. Skriv **ThirdLight**i rutan Sök. Välj **ThirdLight** i Sök resultaten och välj sedan **Lägg till**.

     ![Sökresultat](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa enkel inloggning med Azure AD med ThirdLight med hjälp av en test användare som heter Britta Simon.
Om du vill aktivera enkel inloggning måste du upprätta en relation mellan en Azure AD-användare och motsvarande användare i ThirdLight.

Du måste utföra följande steg för att konfigurera och testa enkel inloggning med ThirdLight i Azure AD:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** för att aktivera funktionen för dina användare.
2. **[Konfigurera ThirdLight enkel inloggning](#configure-thirdlight-single-sign-on)** på program sidan.
3. **[Skapa en Azure AD test-användare](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** för att aktivera enkel inloggning med Azure AD för användaren.
5. **[Skapa en ThirdLight](#create-a-thirdlight-test-user)** som är länkad till användarens Azure AD-representation.
6. **[Testa enkel inloggning](#test-single-sign-on)** för att verifiera att konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet ska du aktivera enkel inloggning med Azure AD i Azure Portal.

Utför följande steg för att konfigurera enkel inloggning med ThirdLight i Azure AD:

1. I [Azure Portal](https://portal.azure.com/)på sidan ThirdLight program integration väljer du **enkel inloggning**:

    ![Välj enkel inloggning](common/select-sso.png)

2. I dialog rutan **Välj metod för enkel inloggning** väljer du **SAML/WS-utfodras** läge för att aktivera enkel inloggning:

    ![Välj en metod för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** väljer du ikonen **Redigera** för att öppna dialog rutan **grundläggande SAML-konfiguration** :

    ![Redigera-ikonen](common/edit-urls.png)

4. Utför följande steg i dialog rutan **grundläggande SAML-konfiguration** .

    ![Dialog rutan grundläggande SAML-konfiguration](common/sp-identifier.png)

    1. I rutan **inloggnings-URL** anger du en URL i det här mönstret:
    
          `https://<subdomain>.thirdlight.com/`

    1. I rutan **identifierare (enhets-ID)** anger du en URL i det här mönstret:

       `https://<subdomain>.thirdlight.com/saml/sp`

       > [!NOTE]
       > Dessa värden är plats hållare. Du måste använda den faktiska inloggnings-URL: en och identifieraren. Kontakta [ThirdLight support-teamet](https://www.thirdlight.com/support) för att hämta värdena. Du kan också se de mönster som visas i dialog rutan **grundläggande SAML-konfiguration** i Azure Portal.

5. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , väljer du länken **Hämta** bredvid XML för **federationsmetadata**, enligt dina krav och sparar filen på datorn:

    ![Länk för nedladdning av certifikat](common/metadataxml.png)

6. I avsnittet **Konfigurera ThirdLight** kopierar du lämpliga URL: er utifrån dina krav:

    ![Kopiera URL: en för konfigurationen](common/copy-configuration-urls.png)

    1. **Inloggnings-URL**.

    1. **Azure AD-identifierare**.

    1. **Utloggnings-URL**.

### <a name="configure-thirdlight-single-sign-on"></a>Konfigurera ThirdLight enkel inloggning

1. I ett nytt webbläsarfönster loggar du in på din ThirdLight-företags webbplats som administratör.

1. Gå till **konfigurations**  >  **system administration**  >  **SAML2**:

    ![System administration](./media/thirdlight-tutorial/ic805843.png "System administration")

1. I avsnittet konfiguration av SAML2 utför du följande steg.
  
    ![SAML2 konfigurations avsnitt](./media/thirdlight-tutorial/ic805844.png "SAML2 konfigurations avsnitt")

    1. Välj **aktivera enkel inloggning med SAML2**.

    1. Under **källa för IDP metadata**väljer du **läsa in IDP metadata från XML**.

    1. Öppna den metadatafil som du laddade ned från Azure Portal i föregående avsnitt. Kopiera filens innehåll och klistra in det i rutan **IDP metadata XML** .

    1. Välj **Spara inställningar för SAML2**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare med namnet Britta Simon i Azure Portal.

1. I Azure Portal väljer du **Azure Active Directory** i den vänstra rutan, väljer **användare**och väljer sedan **alla användare**:

    ![Välj alla användare](common/users.png)

2. Välj **ny användare** överst i fönstret:

    ![Välj ny användare](common/new-user.png)

3. I dialog rutan **användare** utför du följande steg.

    ![Dialog rutan användare](common/user-properties.png)

    1. I rutan **Namn** anger du **BrittaSimon**.
  
    1. I rutan **användar namn** anger du **BrittaSimon@ \<yourcompanydomain> . \<extension> **. (Till exempel BrittaSimon@contoso.com .)

    1. Välj **Visa lösen ord**och skriv sedan ned värdet i rutan **lösen ord** .

    1. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera Britta Simon för att använda enkel inloggning med Azure genom att ge hennes åtkomst till ThirdLight.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **ThirdLight**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer du **ThirdLight**.

    ![Lista över program](common/all-applications.png)

3. I den vänstra rutan väljer **du användare och grupper**:

    ![Välj Användare och grupper](common/users-groups-blade.png)

4. Välj **Lägg till användare** och sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Välj Lägg till användare](common/add-assign-user.png)

5. I dialog rutan **användare och grupper** väljer du **Britta Simon** i listan användare och klickar sedan på knappen **Välj** längst ned i fönstret.

6. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan. Klicka på knappen **Välj** längst ned i fönstret.

7. I dialogrutan **Lägg till tilldelning** väljer du **Tilldela**.

### <a name="create-a-thirdlight-test-user"></a>Skapa en ThirdLight-test användare

Om du vill att Azure AD-användare ska kunna logga in på ThirdLight måste du lägga till dem i ThirdLight. Du måste lägga till dem manuellt.

Gör så här för att skapa ett användar konto:

1. Logga in på din ThirdLight-företags webbplats som administratör.

1. Gå till fliken **användare** .

1. Välj **användare och grupper**.

1. Välj **Lägg till ny användare**.

1. Ange användar namnet, ett namn eller en beskrivning och e-postadressen till ett giltigt Azure AD-konto som du vill etablera. Välj en för inställning eller grupp med nya medlemmar.

1. Välj **Skapa**.

> [!NOTE]
> Du kan använda valfritt verktyg för att skapa användar konton eller API som tillhandahålls av ThirdLight för att etablera Azure AD-användarkonton.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

Nu måste du testa konfigurationen för enkel inloggning med Azure AD med hjälp av åtkomst panelen.

När du väljer panelen ThirdLight på åtkomst panelen, bör du loggas in automatiskt på ThirdLight-instansen som du ställer in SSO för. Mer information om åtkomst panelen finns i [komma åt och använda appar på portalen Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Självstudier för att integrera SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
