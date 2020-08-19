---
title: 'Självstudie: Azure Active Directory integrering med Predictix-beställning | Microsoft Docs'
description: I den här självstudien får du lära dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Predictix ordning.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: 0eec8d9d88b286e386bd310c98d03737e56e05b2
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88553741"
---
# <a name="tutorial-azure-active-directory-integration-with-predictix-ordering"></a>Självstudie: Azure Active Directory integrering med Predictix-beställning

I den här självstudien får du lära dig hur du integrerar Predictix-beställning med Azure Active Directory (Azure AD).
Den här integrationen ger följande fördelar:

* Du kan använda Azure AD för att kontrol lera vem som har åtkomst till Predictix-beställning.
* Du kan göra det möjligt för användarna att logga in automatiskt till Predictix-beställning (enkel inloggning) med deras Azure AD-konton.
* Du kan hantera dina konton på en central plats: Azure Portal.

Läs mer om SaaS-appintegrering med Azure AD i [Enkel inloggning till program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Om du inte har en Azure-prenumeration kan du [skapa ett kostnads fritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Predictix-beställning måste du ha:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/pricing/free-trial/).
* En prenumeration på Predictix-beställning med enkel inloggning aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning i Azure AD i en test miljö.

* Predictix-beställning stöder SP-initierad SSO.

## <a name="add-predictix-ordering-from-the-gallery"></a>Lägg till Predictix-beställning från galleriet

Om du vill ställa in integreringen av Predictix-ordningen i Azure AD måste du lägga till Predictix-beställning från galleriet i listan över hanterade SaaS-appar.

1. I [Azure Portal](https://portal.azure.com)väljer du **Azure Active Directory**i den vänstra rutan:

    ![Välj Azure Active Directory](common/select-azuread.png)

2. Gå till **företags program**  >  **alla program**:

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett program väljer du **nytt program** överst i fönstret:

    ![Välj nytt program](common/add-new-app.png)

4. Skriv **Predictix-ordning**i sökrutan. Välj **Predictix ordning** i Sök resultaten och välj sedan **Lägg till**.

     ![Sökresultat](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa enkel inloggning med Azure AD med Predictix-beställning genom att använda en test användare som heter Britta Simon.
Om du vill aktivera enkel inloggning måste du upprätta en relation mellan en Azure AD-användare och motsvarande användare i Predictix-beställning.

Om du vill konfigurera och testa enkel inloggning med Predictix i Azure AD måste du utföra följande steg:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** för att aktivera funktionen för dina användare.
2. **[Konfigurera Predictix order för enkel inloggning](#configure-predictix-ordering-single-sign-on)** på program sidan.
3. **[Skapa en Azure AD test-användare](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** för att aktivera enkel inloggning med Azure AD för användaren.
5. **[Skapa en Predictix](#create-a-predictix-ordering-test-user)** som är länkad till användarens Azure AD-representation.
6. **[Testa enkel inloggning](#test-single-sign-on)** för att verifiera att konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet ska du aktivera enkel inloggning med Azure AD i Azure Portal.

Utför följande steg för att konfigurera enkel inloggning med Predictix-beställning i Azure AD:

1. I [Azure Portal](https://portal.azure.com/)väljer du **enkel inloggning**på sidan **Predictix order** application integration:

    ![Välj enkel inloggning](common/select-sso.png)

2. I dialog rutan **Välj metod för enkel inloggning** väljer du **SAML/WS-utfodras** läge för att aktivera enkel inloggning:

    ![Välj en metod för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** väljer du ikonen **Redigera** för att öppna dialog rutan **grundläggande SAML-konfiguration** :

    ![Redigera-ikonen](common/edit-urls.png)

4. Utför följande steg i dialog rutan **grundläggande SAML-konfiguration** .

    ![Dialog rutan grundläggande SAML-konfiguration](common/sp-identifier.png)

    1. I rutan **inloggnings-URL** anger du en URL i det här mönstret:

       `https://<companyname-pricing>.ordering.predictix.com/sso/request`

    1. I rutan **identifierare (enhets-ID)** anger du en URL i det här mönstret:

        ```https
        https://<companyname-pricing>.dev.ordering.predictix.com
        https://<companyname-pricing>.ordering.predictix.com
        ```

    > [!NOTE]
    > Dessa värden är plats hållare. Du måste använda den faktiska inloggnings-URL: en och identifieraren. Kontakta [support teamet för Predictix-beställning](https://www.predix.io/support/) för att hämta värdena. Du kan också se de mönster som visas i dialog rutan **grundläggande SAML-konfiguration** i Azure Portal.

5. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , väljer du länken **Hämta** bredvid **certifikat (base64)** enligt dina krav och sparar certifikatet på datorn:

    ![Länk för nedladdning av certifikat](common/certificatebase64.png)

6. I avsnittet **Konfigurera Predictix-beställning** kopierar du lämpliga URL: er utifrån dina krav:

    ![Kopiera URL: en för konfigurationen](common/copy-configuration-urls.png)

    1. **Inloggnings-URL**.

    2. **Azure AD-identifierare**.

    3. **Utloggnings-URL**.

### <a name="configure-predictix-ordering-single-sign-on"></a>Konfigurera enkel inloggning för Predictix-beställning

Om du vill konfigurera enkel inloggning på Predictix-beställnings sidan måste du skicka det certifikat som du laddade ned och de URL: er som du kopierade från Azure Portal till [support teamet för Predictix-beställning](https://www.predix.io/support/). Det här teamet ser till att SAML SSO-anslutningen är korrekt inställd på båda sidor.

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

I det här avsnittet ska du aktivera Britta Simon för att använda enkel inloggning med Azure AD genom att ge hennes åtkomst till Predictix-beställning.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **Predictix ordning**:

    ![Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer du **Predictix-beställning**.

    ![Lista över program](common/all-applications.png)

3. I den vänstra rutan väljer **du användare och grupper**:

    ![Välj Användare och grupper](common/users-groups-blade.png)

4. Välj **Lägg till användare** och sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Välj Lägg till användare](common/add-assign-user.png)

5. I dialog rutan **användare och grupper** väljer du **Britta Simon** i listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan. Klicka på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** väljer du **Tilldela**.

### <a name="create-a-predictix-ordering-test-user"></a>Skapa en test användare för Predictix-beställning

Därefter måste du skapa en användare med namnet Britta Simon i Predictix-beställning. Arbeta med [support teamet för Predictix-beställning](https://www.predix.io/support/) för att lägga till användare. Användarna måste skapas och aktive ras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

Nu måste du testa konfigurationen för enkel inloggning med Azure AD med hjälp av åtkomst panelen.

När du väljer sorterings panelen Predictix på åtkomst panelen, bör du loggas in automatiskt på den Predictix beställnings instans som du ställer in SSO för. Mer information finns i [komma åt och använda appar på portalen Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Självstudier för att integrera SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
