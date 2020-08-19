---
title: 'Självstudie: Azure Active Directory integrering med RolePoint | Microsoft Docs'
description: I den här självstudien får du lära dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och RolePoint.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/15/2019
ms.author: jeedes
ms.openlocfilehash: 235010cbcdde326cf17643cec81f3b6fcb1eea59
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88548777"
---
# <a name="tutorial-azure-active-directory-integration-with-rolepoint"></a>Självstudie: Azure Active Directory integrering med RolePoint

I den här självstudien får du lära dig hur du integrerar RolePoint med Azure Active Directory (Azure AD).
Den här integrationen ger följande fördelar:

* Du kan använda Azure AD för att kontrol lera vem som har åtkomst till RolePoint.
* Du kan göra det möjligt för användarna att logga in automatiskt på RolePoint (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats: Azure Portal.

Läs mer om SaaS-appintegrering med Azure AD i [Enkel inloggning till program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med RolePoint måste du ha:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* En RolePoint-prenumeration med enkel inloggning aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning i Azure AD i en test miljö.

* RolePoint stöder SP-initierad SSO.

## <a name="add-rolepoint-from-the-gallery"></a>Lägg till RolePoint från galleriet

Om du vill ställa in integreringen av RolePoint i Azure AD måste du lägga till RolePoint från galleriet i listan över hanterade SaaS-appar.

1. I [Azure Portal](https://portal.azure.com)väljer du **Azure Active Directory**i den vänstra rutan:

    ![Välj Azure Active Directory](common/select-azuread.png)

2. Gå till **företags program**  >  **alla program**:

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett program väljer du **nytt program** överst i fönstret:

    ![Välj nytt program](common/add-new-app.png)

4. Skriv **RolePoint**i rutan Sök. Välj **RolePoint** i Sök resultaten och välj sedan **Lägg till**.

     ![Sökresultat](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa enkel inloggning med Azure AD med RolePoint med hjälp av en test användare som heter Britta Simon.
Om du vill aktivera enkel inloggning måste du upprätta en relation mellan en Azure AD-användare och motsvarande användare i RolePoint.

Du måste utföra följande steg för att konfigurera och testa enkel inloggning med RolePoint i Azure AD:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** för att aktivera funktionen för dina användare.
2. **[Konfigurera RolePoint enkel inloggning](#configure-rolepoint-single-sign-on)** på program sidan.
3. **[Skapa en Azure AD test-användare](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** för att aktivera enkel inloggning med Azure AD för användaren.
5. **[Skapa en RolePoint](#create-a-rolepoint-test-user)** som är länkad till användarens Azure AD-representation.
6. **[Testa enkel inloggning](#test-single-sign-on)** för att verifiera att konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet ska du aktivera enkel inloggning med Azure AD i Azure Portal.

Utför följande steg för att konfigurera enkel inloggning med RolePoint i Azure AD:

1. I [Azure Portal](https://portal.azure.com/)på sidan RolePoint program integration väljer du **enkel inloggning**:

    ![Välj enkel inloggning](common/select-sso.png)

2. I dialog rutan **Välj metod för enkel inloggning** väljer du **SAML/WS-utfodras** läge för att aktivera enkel inloggning:

    ![Välj en metod för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** väljer du ikonen **Redigera** för att öppna dialog rutan **grundläggande SAML-konfiguration** :

    ![Redigera-ikonen](common/edit-urls.png)

4. Utför följande steg i dialog rutan **grundläggande SAML-konfiguration** .

    ![Dialog rutan grundläggande SAML-konfiguration](common/sp-identifier.png)

    1. I rutan **inloggnings-URL** anger du en URL i det här mönstret:

       `https://<subdomain>.rolepoint.com/login`

    1. I rutan **identifierare (enhets-ID)** anger du en URL i det här mönstret:

       `https://app.rolepoint.com/<instancename>`

    > [!NOTE]
    > Dessa värden är plats hållare. Du måste använda den faktiska inloggnings-URL: en och identifieraren. Vi rekommenderar att du använder ett unikt sträng värde i identifieraren. Kontakta [RolePoint support-teamet](mailto:info@rolepoint.com) för att hämta dessa värden. Du kan också se de mönster som visas i dialog rutan **grundläggande SAML-konfiguration** i Azure Portal.

5. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , väljer du länken **Hämta** bredvid XML för **federationsmetadata**, enligt dina krav och sparar filen på din dator.

    ![Länk för nedladdning av certifikat](common/metadataxml.png)

6. I avsnittet **Konfigurera RolePoint** kopierar du lämpliga URL: er utifrån dina krav:

    ![Kopiera URL: en för konfigurationen](common/copy-configuration-urls.png)

    1. **Inloggnings-URL**.

    1. **Azure AD-identifierare**.

    1. **Utloggnings-URL**.


### <a name="configure-rolepoint-single-sign-on"></a>Konfigurera RolePoint enkel inloggning

Om du vill konfigurera enkel inloggning på RolePoint-sidan måste du arbeta med [RolePoint support-teamet](mailto:info@rolepoint.com). Skicka det här teamet till XML-filen för federationsmetadata och de URL: er som du fick från Azure Portal. De konfigurerar RolePoint för att se till att SAML SSO-anslutningen är korrekt inställd på båda sidor.

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

I det här avsnittet ska du aktivera Britta Simon för att använda enkel inloggning med Azure genom att ge hennes åtkomst till RolePoint.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **RolePoint**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer du **RolePoint**.

    ![Lista över program](common/all-applications.png)

3. I den vänstra rutan väljer **du användare och grupper**:

    ![Välj Användare och grupper](common/users-groups-blade.png)

4. Välj **Lägg till användare** och sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Välj Lägg till användare](common/add-assign-user.png)

5. I dialog rutan **användare och grupper** väljer du **Britta Simon** i listan användare och klickar sedan på knappen **Välj** längst ned i fönstret.

6. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan. Klicka på knappen **Välj** längst ned i fönstret.

7. I dialogrutan **Lägg till tilldelning** väljer du **Tilldela**.

### <a name="create-a-rolepoint-test-user"></a>Skapa en RolePoint-test användare

Därefter måste du skapa en användare med namnet Britta Simon i RolePoint. Arbeta med [RolePoint support team](mailto:info@rolepoint.com) för att lägga till användare i RolePoint. Användarna måste skapas och aktive ras innan du kan använda enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

Nu måste du testa konfigurationen för enkel inloggning med Azure AD med hjälp av åtkomst panelen.

När du väljer panelen RolePoint på åtkomst panelen, bör du loggas in automatiskt på RolePoint-instansen som du ställer in SSO för. Mer information om åtkomst panelen finns i [komma åt och använda appar på portalen Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Självstudier för att integrera SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
