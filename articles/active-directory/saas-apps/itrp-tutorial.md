---
title: 'Självstudie: Azure Active Directory integrering med ITRP | Microsoft Docs'
description: I den här självstudien får du lära dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och ITRP.
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
ms.openlocfilehash: d7a7c4f7d9a9ba2ef574519a41aae3dd6e28e4fb
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88552806"
---
# <a name="tutorial-azure-active-directory-integration-with-itrp"></a>Självstudie: Azure Active Directory integrering med ITRP

I den här självstudien får du lära dig hur du integrerar ITRP med Azure Active Directory (Azure AD).
Den här integrationen ger följande fördelar:

* Du kan använda Azure AD för att kontrol lera vem som har åtkomst till ITRP.
* Du kan göra det möjligt för användarna att logga in automatiskt på ITRP (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats: Azure Portal.

Läs mer om SaaS-appintegrering med Azure AD i [Enkel inloggning till program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med ITRP måste du ha:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* En ITRP-prenumeration med enkel inloggning aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning i Azure AD i en test miljö.

* ITRP stöder SP-initierad SSO.

## <a name="add-itrp-from-the-gallery"></a>Lägg till ITRP från galleriet

Om du vill ställa in integreringen av ITRP i Azure AD måste du lägga till ITRP från galleriet i listan över hanterade SaaS-appar.

1. I [Azure Portal](https://portal.azure.com)väljer du **Azure Active Directory**i den vänstra rutan:

    ![Välj Azure Active Directory](common/select-azuread.png)

2. Gå till **företags program**  >  **alla program**:

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett program väljer du **nytt program** överst i fönstret:

    ![Välj nytt program](common/add-new-app.png)

4. Skriv **ITRP**i rutan Sök. Välj **ITRP** i Sök resultaten och välj sedan **Lägg till**.

     ![Sökresultat](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa enkel inloggning med Azure AD med ITRP med hjälp av en test användare som heter Britta Simon.
Om du vill aktivera enkel inloggning måste du upprätta en relation mellan en Azure AD-användare och motsvarande användare i ITRP.

Du måste utföra följande steg för att konfigurera och testa enkel inloggning med ITRP i Azure AD:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** för att aktivera funktionen för dina användare.
2. **[Konfigurera ITRP enkel inloggning](#configure-itrp-single-sign-on)** på program sidan.
3. **[Skapa en Azure AD test-användare](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** för att aktivera enkel inloggning med Azure AD för användaren.
5. **[Skapa en ITRP](#create-an-itrp-test-user)** som är länkad till användarens Azure AD-representation.
6. **[Testa enkel inloggning](#test-single-sign-on)** för att verifiera att konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet ska du aktivera enkel inloggning med Azure AD i Azure Portal.

Utför följande steg för att konfigurera enkel inloggning med ITRP i Azure AD:

1. I [Azure Portal](https://portal.azure.com/)på sidan ITRP program integration väljer du **enkel inloggning**:

    ![Välj enkel inloggning](common/select-sso.png)

2. I dialog rutan **Välj metod för enkel inloggning** väljer du **SAML/WS-utfodras** läge för att aktivera enkel inloggning:

    ![Välj en metod för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** väljer du ikonen **Redigera** för att öppna dialog rutan **grundläggande SAML-konfiguration** :

    ![Redigera-ikonen](common/edit-urls.png)

4. Utför följande steg i dialog rutan **grundläggande SAML-konfiguration** .

    ![Dialog rutan grundläggande SAML-konfiguration](common/sp-identifier.png)

    1. I rutan **inloggnings-URL** anger du en URL i det här mönstret:
    
       `https://<tenant-name>.itrp.com`

    1. I rutan **identifierare (enhets-ID)** anger du en URL i det här mönstret:

       `https://<tenant-name>.itrp.com`

    > [!NOTE]
    > Dessa värden är plats hållare. Du måste använda den faktiska inloggnings-URL: en och identifieraren. Kontakta [ITRP support-teamet](https://www.4me.com/support/) för att hämta värdena. Du kan också se de mönster som visas i dialog rutan **grundläggande SAML-konfiguration** i Azure Portal.

5. I avsnittet **certifikat för SAML-signering** väljer du ikonen **Redigera** för att öppna dialog rutan **SAML-signerings certifikat** :

    ![Redigera-ikonen](common/edit-certificate.png)

6. I dialog rutan **SAML signerings certifikat** , kopierar du **tumavtrycket** och sparar det:

    ![Kopiera tumavtryck-värdet](common/copy-thumbprint.png)

7. I avsnittet **Konfigurera ITRP** kopierar du lämpliga URL: er utifrån dina krav:

    ![Kopiera URL: en för konfigurationen](common/copy-configuration-urls.png)

    1. **Inloggnings-URL**.

    1. **Azure AD-identifierare**.

    1. **Utloggnings-URL**.

### <a name="configure-itrp-single-sign-on"></a>Konfigurera ITRP enkel inloggning

1. I ett nytt webbläsarfönster loggar du in på din ITRP-företags webbplats som administratör.

1. Välj ikonen **Inställningar** längst upp i fönstret:

    ![Ikonen Inställningar](./media/itrp-tutorial/ic775570.png "Ikonen Inställningar")

1. I den vänstra rutan väljer du **enkel inloggning**:

    ![Välj enkel inloggning](./media/itrp-tutorial/ic775571.png "Välj enkel inloggning")

1. I avsnittet konfiguration av **enkel inloggning** utför du följande steg.

    ![Avsnittet enkel inloggning](./media/itrp-tutorial/ic775572.png "Avsnittet enkel inloggning")

    ![Avsnittet enkel inloggning](./media/itrp-tutorial/ic775573.png "Avsnittet enkel inloggning")

    1. Välj **Aktiverad**.

    1. I rutan **URL för fjärrutloggning** klistrar du in URL-värdet för **utloggning** som du kopierade från Azure Portal.

    1. I rutan **URL för SAML SSO** klistrar du in URL-värdet för **inloggning** som du kopierade från Azure Portal.

    1. I rutan **finger avtryck för certifikat** klistrar du in **tumavtryck** -värdet för certifikatet som du kopierade från Azure Portal.

    1. Välj **Spara**.

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

I det här avsnittet ska du aktivera Britta Simon för att använda enkel inloggning med Azure genom att ge hennes åtkomst till ITRP.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **ITRP**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer du **ITRP**.

    ![Lista över program](common/all-applications.png)

3. I den vänstra rutan väljer **du användare och grupper**:

    ![Välj Användare och grupper](common/users-groups-blade.png)

4. Välj **Lägg till användare** och sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Välj Lägg till användare](common/add-assign-user.png)

5. I dialog rutan **användare och grupper** väljer du **Britta Simon** i listan användare och klickar sedan på knappen **Välj** längst ned i fönstret.

6. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan. Klicka på knappen **Välj** längst ned i fönstret.

7. I dialogrutan **Lägg till tilldelning** väljer du **Tilldela**.

### <a name="create-an-itrp-test-user"></a>Skapa en ITRP-test användare

Om du vill att Azure AD-användare ska kunna logga in på ITRP måste du lägga till dem i ITRP. Du måste lägga till dem manuellt.

Gör så här för att skapa ett användar konto:

1. Logga in på din ITRP-klient.

1. Välj ikonen **poster** överst i fönstret:

    ![Poster-ikon](./media/itrp-tutorial/ic775575.png "Poster-ikon")

1. Välj **personer**på menyn:

    ![Välj personer](./media/itrp-tutorial/ic775587.png "Välj personer")

1. Välj plus tecknet ( **+** ) för att lägga till en ny person:

    ![Välj plus tecknet](./media/itrp-tutorial/ic775576.png "Välj plus tecknet")

1. I dialog rutan **Lägg till ny person** gör du följande:

    ![Dialog rutan Lägg till ny person](./media/itrp-tutorial/ic775577.png "Dialog rutan Lägg till ny person")

    1. Ange namn och e-postadress för ett giltigt Azure AD-konto som du vill lägga till.

    1. Välj **Spara**.

> [!NOTE]
> Du kan använda valfritt verktyg för att skapa användar konton eller API som tillhandahålls av ITRP för att etablera Azure AD-användarkonton.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

Nu måste du testa konfigurationen för enkel inloggning med Azure AD med hjälp av åtkomst panelen.

När du väljer panelen ITRP på åtkomst panelen, bör du loggas in automatiskt på ITRP-instansen som du ställer in SSO för. Mer information om åtkomst panelen finns i [komma åt och använda appar på portalen Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Självstudier för att integrera SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
