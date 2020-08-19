---
title: 'Självstudie: Azure Active Directory integrering med Proxyclick | Microsoft Docs'
description: I den här självstudien får du lära dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Proxyclick.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: b3974c5e2a46ede6ef3f92d74759f58ed55b8497
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88553463"
---
# <a name="tutorial-azure-active-directory-integration-with-proxyclick"></a>Självstudie: Azure Active Directory integrering med Proxyclick

I den här självstudien får du lära dig hur du integrerar Proxyclick med Azure Active Directory (Azure AD).
Den här integrationen ger följande fördelar:

* Du kan använda Azure AD för att kontrol lera vem som har åtkomst till Proxyclick.
* Du kan göra det möjligt för användarna att logga in automatiskt på Proxyclick (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats: Azure Portal.

Läs mer om SaaS-appintegrering med Azure AD i [Enkel inloggning till program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Proxyclick måste du ha:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du registrera dig för en [utvärderings version](https://azure.microsoft.com/pricing/free-trial/)på en månad.
* En Proxyclick-prenumeration med enkel inloggning aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning i Azure AD i en test miljö.

* Proxyclick stöder SP-initierad och IdP-initierad SSO.

## <a name="add-proxyclick-from-the-gallery"></a>Lägg till Proxyclick från galleriet

Om du vill ställa in integreringen av Proxyclick i Azure AD måste du lägga till Proxyclick från galleriet i listan över hanterade SaaS-appar.

1. I [Azure Portal](https://portal.azure.com)väljer du **Azure Active Directory**i den vänstra rutan:

    ![Välj Azure Active Directory](common/select-azuread.png)

2. Gå till **företags program**  >  **alla program**:

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett program väljer du **nytt program** överst i fönstret:

    ![Välj nytt program](common/add-new-app.png)

4. Skriv **Proxyclick**i rutan Sök. Välj **Proxyclick** i Sök resultaten och välj sedan **Lägg till**.

     ![Sökresultat](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa enkel inloggning med Azure AD med Proxyclick med hjälp av en test användare som heter Britta Simon.
Om du vill aktivera enkel inloggning måste du upprätta en relation mellan en Azure AD-användare och motsvarande användare i Proxyclick.

Du måste utföra följande steg för att konfigurera och testa enkel inloggning med Proxyclick i Azure AD:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** för att aktivera funktionen för dina användare.
2. **[Konfigurera Proxyclick enkel inloggning](#configure-proxyclick-single-sign-on)** på program sidan.
3. **[Skapa en Azure AD test-användare](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** för att aktivera enkel inloggning med Azure AD för användaren.
5. **[Skapa en Proxyclick](#create-a-proxyclick-test-user)** som är länkad till användarens Azure AD-representation.
6. **[Testa enkel inloggning](#test-single-sign-on)** för att verifiera att konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet ska du aktivera enkel inloggning med Azure AD i Azure Portal.

Utför följande steg för att konfigurera enkel inloggning med Proxyclick i Azure AD:

1. I [Azure Portal](https://portal.azure.com/)på sidan Proxyclick program integration väljer du **enkel inloggning**:

    ![Välj enkel inloggning](common/select-sso.png)

2. I dialog rutan **Välj metod för enkel inloggning** väljer du **SAML/WS-utfodras** läge för att aktivera enkel inloggning:

    ![Välj en metod för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** väljer du ikonen **Redigera** för att öppna dialog rutan **grundläggande SAML-konfiguration** :

    ![Redigera-ikonen](common/edit-urls.png)

4. I dialog rutan **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i IDP-läge, utför du följande steg.

    ![Dialog rutan grundläggande SAML-konfiguration](common/idp-intiated.png)

    1. I rutan **identifierare** anger du en URL i det här mönstret:
   
       `https://saml.proxyclick.com/init/<companyId>`

    1. I rutan **svars-URL** anger du en URL i följande mönster:

       `https://saml.proxyclick.com/consume/<companyId>`

5. Om du vill konfigurera programmet i SP-initierat läge väljer du **Ange ytterligare URL: er**. I rutan **inloggnings-URL** anger du en URL i det här mönstret:
   
   `https://saml.proxyclick.com/init/<companyId>`

    ![Information om enkel inloggning för Proxyclick-domän och URL: er](common/metadata-upload-additional-signon.png)

    

    > [!NOTE]
    > Dessa värden är plats hållare. Du måste använda den faktiska identifieraren, svars-URL: en och inloggnings-URL: en. Steg för att hämta dessa värden beskrivs senare i den här självstudien.

6. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , väljer du länken **Hämta** bredvid **certifikat (base64)** enligt dina krav och sparar certifikatet på datorn:

    ![Länk för nedladdning av certifikat](common/certificatebase64.png)

7. I avsnittet **Konfigurera Proxyclick** kopierar du lämpliga URL: er utifrån dina krav:

    ![Kopiera URL: en för konfigurationen](common/copy-configuration-urls.png)

    1. **Inloggnings-URL**.

    1. **Azure AD-identifierare**.

    1. **Utloggnings-URL**.

### <a name="configure-proxyclick-single-sign-on"></a>Konfigurera Proxyclick enkel inloggning

1. I ett nytt webbläsarfönster loggar du in på din Proxyclick-företags webbplats som administratör.

2. Välj **konto & inställningar**:

    ![Välj konto & inställningar](./media/proxyclick-tutorial/configure1.png)

3. Rulla ned till avsnittet **integreringar** och välj **SAML**:

    ![Välj SAML](./media/proxyclick-tutorial/configure2.png)

4. I avsnittet **SAML** , utför följande steg.

    ![SAML-avsnitt](./media/proxyclick-tutorial/configure3.png)

    1. Kopiera URL-värdet för **SAML-konsumenten** och klistra in det i rutan **svars-URL** i dialog rutan **grundläggande SAML-konfiguration** i Azure Portal.

    1. Kopiera URL-värdet för **SAML SSO Redirect** och klistra in det i rutorna för **inloggnings-URL** och **IDENTIFIERARE** i dialog rutan **grundläggande SAML-konfiguration** i Azure Portal.

    1. I listan **metod för SAML-begäran** väljer du **http-omdirigering**.

    1. I rutan **utfärdare** klistrar du in värdet för **Azure AD-identifieraren** som du kopierade från Azure Portal.

    1. I rutan **slut punkts-URL för SAML 2,0** klistrar du in URL-värdet för **inloggning** som du kopierade från Azure Portal.

    1. Öppna certifikat filen som du laddade ned från Azure Portal i anteckningar. Klistra in innehållet i den här filen i rutan **certifikat** .

    1. Välj **Spara ändringar**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare med namnet Britta Simon i Azure Portal.

1. I Azure Portal väljer du **Azure Active Directory** i den vänstra rutan, väljer **användare**och väljer sedan **alla användare**:

    ![Välj alla användare](common/users.png)

2. Välj **ny användare** överst på skärmen:

    ![Välj ny användare](common/new-user.png)

3. I dialog rutan **användare** utför du följande steg.

    ![Dialog rutan användare](common/user-properties.png)

    1. I rutan **Namn** anger du **BrittaSimon**.
  
    1. I rutan **användar namn** anger du **BrittaSimon@ \<yourcompanydomain> . \<extension> **. (Till exempel BrittaSimon@contoso.com .)

    1. Välj **Visa lösen ord**och skriv sedan ned värdet i rutan **lösen ord** .

    1. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera Britta Simon för att använda enkel inloggning med Azure genom att ge hennes åtkomst till Proxyclick.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **Proxyclick**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer du **Proxyclick**.

    ![Lista över program](common/all-applications.png)

3. I den vänstra rutan väljer **du användare och grupper**:

    ![Välj Användare och grupper](common/users-groups-blade.png)

4. Välj **Lägg till användare** och sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Välj Lägg till användare](common/add-assign-user.png)

5. I dialog rutan **användare och grupper** väljer du **Britta Simon** i listan användare och klickar sedan på knappen **Välj** längst ned i fönstret.

6. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan. Klicka på knappen **Välj** längst ned i fönstret.

7. I dialogrutan **Lägg till tilldelning** väljer du **Tilldela**.

### <a name="create-a-proxyclick-test-user"></a>Skapa en Proxyclick-test användare

Om du vill att Azure AD-användare ska kunna logga in på Proxyclick måste du lägga till dem i Proxyclick. Du måste lägga till dem manuellt.

Gör så här för att skapa ett användar konto:

1. Logga in på din Proxyclick-företags webbplats som administratör.

1. Välj **kollegor** överst i fönstret:

    ![Välj kollegor](./media/proxyclick-tutorial/user1.png)

1. Välj **Lägg till kollega**:

    ![Välj Lägg till kollega](./media/proxyclick-tutorial/user2.png)

1. I avsnittet **Lägg till en kollega** gör du följande:

    ![Lägg till en kollega-sektion](./media/proxyclick-tutorial/user3.png)

    1. I rutan **e-post** anger du användarens e-postadress. I det här fallet **brittasimon \@ contoso.com**.

    1. I rutan **förnamn** anger du användarens förnamn. I det här fallet **Britta**.

    1. I rutan **efter namn** anger du användarens efter namn. I det här fallet **Simon**.

    1. Välj **Lägg till användare**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

Nu måste du testa konfigurationen för enkel inloggning med Azure AD med hjälp av åtkomst panelen.

När du väljer panelen Proxyclick på åtkomst panelen, bör du loggas in automatiskt på Proxyclick-instansen som du ställer in SSO för. Mer information om åtkomst panelen finns i [komma åt och använda appar på portalen Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Självstudier för att integrera SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

