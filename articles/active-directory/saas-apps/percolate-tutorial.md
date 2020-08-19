---
title: 'Självstudie: Azure Active Directory integrering med Percolate | Microsoft Docs'
description: I den här självstudien får du lära dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Percolate.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: deb64aa0c344e818b5fd85ca1a161293fd35d6f6
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88554023"
---
# <a name="tutorial-azure-active-directory-integration-with-percolate"></a>Självstudie: Azure Active Directory integrering med Percolate

I den här självstudien får du lära dig hur du integrerar Percolate med Azure Active Directory (Azure AD).

Den här integrationen ger följande fördelar:

* Du kan använda Azure AD för att kontrol lera vem som har åtkomst till Percolate.
* Du kan göra det möjligt för användarna att logga in automatiskt på Percolate (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats: Azure Portal.

Läs mer om SaaS-appintegrering med Azure AD i [Enkel inloggning till program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Om du inte har en Azure-prenumeration kan du [skapa ett kostnads fritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Percolate måste du ha:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* En Percolate-prenumeration med enkel inloggning aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning i Azure AD i en test miljö.

* Percolate stöder SP-initierad och IdP-initierad SSO.

## <a name="add-percolate-from-the-gallery"></a>Lägg till Percolate från galleriet

Om du vill konfigurera integreringen av Percolate i Azure AD måste du lägga till Percolate från galleriet i listan över hanterade SaaS-appar.

1. I [Azure Portal](https://portal.azure.com)väljer du **Azure Active Directory**i den vänstra rutan:

    ![Välj Azure Active Directory](common/select-azuread.png)

2. Gå till **företags program**  >  **alla program**:

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett program väljer du **nytt program** överst i fönstret:

    ![Välj nytt program](common/add-new-app.png)

4. Skriv **Percolate**i rutan Sök. Välj **Percolate** i Sök resultaten och välj sedan **Lägg till**.

     ![Sökresultat](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa enkel inloggning med Azure AD med Percolate med hjälp av en test användare som heter Britta Simon.
Om du vill aktivera enkel inloggning måste du upprätta en relation mellan en Azure AD-användare och motsvarande användare i Percolate.

Du måste utföra följande steg för att konfigurera och testa enkel inloggning med Percolate i Azure AD:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** för att aktivera funktionen för dina användare.
2. **[Konfigurera Percolate enkel inloggning](#configure-percolate-single-sign-on)** på program sidan.
3. **[Skapa en Azure AD test-användare](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** för att aktivera enkel inloggning med Azure AD för användaren.
5. **[Skapa en Percolate](#create-a-percolate-test-user)** som är länkad till användarens Azure AD-representation.
6. **[Testa enkel inloggning](#test-single-sign-on)** för att verifiera att konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet ska du aktivera enkel inloggning med Azure AD i Azure Portal.

Utför följande steg för att konfigurera enkel inloggning med Percolate i Azure AD:

1. I [Azure Portal](https://portal.azure.com/)på sidan **Percolate** program integration väljer du **enkel inloggning**:

    ![Välj enkel inloggning](common/select-sso.png)

2. I dialog rutan **Välj metod för enkel inloggning** väljer du **SAML/WS-utfodras** läge för att aktivera enkel inloggning:

    ![Välj en metod för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** väljer du ikonen **Redigera** för att öppna dialog rutan **grundläggande SAML-konfiguration** :

    ![Redigera-ikonen](common/edit-urls.png)

4. I dialog rutan för den **grundläggande SAML-konfigurationen** behöver du inte vidta några åtgärder för att konfigurera programmet i IDP-initierat läge. Appen är redan integrerad med Azure.

    ![Information om enkel inloggning för Percolate-domän och URL: er](common/preintegrated.png)

5. Om du vill konfigurera programmet i SP-initierat läge väljer du **Ange ytterligare URL: er** i rutan **inloggnings-URL** och anger **https://percolate.com/app/login** :

   ![Information om enkel inloggning för Percolate-domän och URL: er](common/metadata-upload-additional-signon.png)
6. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signerings certifikat** , väljer du **kopierings** ikonen för att kopiera **URL: en för appens federationens metadata**. Spara den här URL: en.

    ![Kopiera URL för app Federation-Metadata](common/copy-metadataurl.png)

7. I avsnittet **Konfigurera Percolate** kopierar du lämpliga URL: er baserat på dina krav.

    ![Kopiera URL: en för konfigurationen](common/copy-configuration-urls.png)

    1. **Inloggnings-URL**.

    1. **Azure AD-identifierare**.

    1. **Utloggnings-URL**.

### <a name="configure-percolate-single-sign-on"></a>Konfigurera Percolate enkel inloggning

1. Logga in på Percolate som administratör i ett nytt webbläsarfönster.

2. På vänster sida av start sidan väljer du **Inställningar**:
    
    ![Välj Inställningar](./media/percolate-tutorial/configure01.png)

3. I det vänstra fönstret väljer du **SSO** under **organisation**:

    ![Välj SSO under organisation](./media/percolate-tutorial/configure02.png)

    1. I rutan **inloggnings webb adress** klistrar du in URL-värdet för **inloggning** som du kopierade från Azure Portal.

    1. I rutan **entitets-ID** klistrar du in det ID-värde för **Azure AD** som du kopierade från Azure Portal.

    1. I anteckningar öppnar du det bas-64-kodade certifikatet som du laddade ned från Azure Portal. Kopiera innehållet och klistra in det i rutan **x509-certifikat** .

    1. Skriv **EmailAddress**i rutan **e-postattribut** .

    1. Rutan **URL för identitets leverantörens metadata** är ett valfritt fält. Om du har kopierat en URL för en **app Federation-Metadata** från Azure Portal kan du klistra in den i den här rutan.

    1. I listan **ska AuthNRequests vara signerade?** väljer du **Nej**.

    1. I listan **Aktivera SSO Auto-Provisioning** väljer du **Nej**.

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

I det här avsnittet ska du aktivera Britta Simon för att använda enkel inloggning med Azure AD genom att ge hennes åtkomst till Percolate.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **Percolate**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer du **Percolate**.

    ![Lista över program](common/all-applications.png)

3. I den vänstra rutan väljer **du användare och grupper**:

    ![Välj Användare och grupper](common/users-groups-blade.png)

4. Välj **Lägg till användare** och sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Välj Användare och grupper](common/add-assign-user.png)

5. I dialog rutan **användare och grupper** väljer du **Britta Simon** i listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan. Klicka på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** väljer du **Tilldela**.

### <a name="create-a-percolate-test-user"></a>Skapa en Percolate-test användare

Om du vill att Azure AD-användare ska kunna logga in på Percolate måste du lägga till dem i Percolate. Du måste lägga till dem manuellt.

Gör så här för att skapa ett användar konto:

1. Logga in på Percolate som administratör.

2. I den vänstra rutan väljer **du användare** under **organisation**. Välj **nya användare**:

    ![Välj nya användare](./media/percolate-tutorial/configure03.png)

3. Utför följande steg på sidan **skapa användare** .

    ![Sidan Skapa användare](./media/percolate-tutorial/configure04.png)

    1. I rutan **e-post** anger du användarens e-postadress. Till exempel brittasimon@contoso.com.

    1. I rutan **fullständigt namn** anger du namnet på användaren. Till exempel **Brittasimon**.

    1. Välj **skapa användare**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

Nu måste du testa konfigurationen för enkel inloggning med Azure AD med hjälp av åtkomst panelen.

När du väljer panelen Percolate på åtkomst panelen, bör du loggas in automatiskt på Percolate-instansen som du ställer in SSO för. Mer information finns i [komma åt och använda appar på portalen Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Självstudier för att integrera SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)