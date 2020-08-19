---
title: 'Självstudie: Azure Active Directory integrering med Uberflip | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Uberflip.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 8936c2150c32a22877f9b56736ecc4b8f3424b32
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88533188"
---
# <a name="tutorial-azure-active-directory-integration-with-uberflip"></a>Självstudie: Azure Active Directory integrering med Uberflip

I den här självstudien får du lära dig hur du integrerar Uberflip med Azure Active Directory (Azure AD).

Genom att integrera Uberflip med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Uberflip.
* Du kan göra det möjligt för användarna att logga in automatiskt på Uberflip (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats: Azure Portal.

Mer information om program vara som en tjänst (SaaS) för program integrering med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Uberflip behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.
* En Uberflip-prenumeration med enkel inloggning aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

Uberflip stöder följande funktioner:

* SP-initierad och IDP enkel inloggning (SSO).
* Just-in-Time-etablering av användare.

## <a name="add-uberflip-from-the-azure-marketplace"></a>Lägg till Uberflip från Azure Marketplace

Om du vill konfigurera integreringen av Uberflip i Azure AD måste du lägga till Uberflip från Azure Marketplace till din lista över hanterade SaaS-appar:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **Azure Active Directory** i den vänstra rutan.

   ![Alternativet Azure Active Directory](common/select-azuread.png)

1. Gå till **Företagsprogram** och välj sedan **Alla program**.

   ![Fönstret Företagsprogram](common/enterprise-applications.png)

1. Om du vill lägga till ett nytt program väljer du **+ nytt program** överst i fönstret.

   ![Alternativet nytt program](common/add-new-app.png)

1. Skriv **Uberflip**i rutan Sök. I Sök resultaten väljer du **Uberflip**och väljer sedan **Lägg till** för att lägga till programmet.

   ![Uberflip i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med Uberflip baserat på en test användare som heter **B Simon**. För att enkel inloggning ska fungera måste du upprätta en länk mellan en Azure AD-användare och en relaterad användare i Uberflip.

Om du vill konfigurera och testa enkel inloggning med Uberflip i Azure AD måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** så att användarna kan använda den här funktionen.
1. **[Konfigurera Uberflip enkel inloggning](#configure-uberflip-single-sign-on)** för att konfigurera inställningarna för enkel inloggning på program sidan.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** för att testa enkel inloggning i Azure AD med B. Simon.
1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** att aktivera B. Simon för att använda enkel inloggning i Azure AD.
1. **[Skapa en Uberflip-test användare](#create-an-uberflip-test-user)** så att det finns en användare med namnet b. Simon i Uberflip som är länkad till Azure AD-användaren med namnet b. Simon.
1. **[Testa enkel inloggning](#test-single-sign-on)** för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med Uberflip i Azure AD:

1. Välj **enkel inloggning**på sidan **Uberflip** Application Integration i [Azure Portal](https://portal.azure.com/).

    ![Konfigurera alternativ för enkel inloggning](common/select-sso.png)

1. I fönstret **Välj en enkel inloggnings metod** väljer du **SAML/WS-utfodras** läge för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

1. I fönstret **Konfigurera enkel inloggning med SAML** väljer du **Redigera** (Penn ikonen) för att öppna fönstret **grundläggande SAML-konfiguration** .

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I fönstret **grundläggande SAML-konfiguration** utför du något av följande steg, beroende på vilket SSO-läge som du vill konfigurera:

   * Om du vill konfigurera programmet i IDP SSO-läge, i rutan **svars-URL (intyg om försäkran mottagar tjänst)** anger du en URL med hjälp av följande mönster:

     `https://app.uberflip.com/sso/saml2/<IDPID>/<ACCOUNTID>`

     ![Information om enkel inloggning för Uberflip-domän och URL: er](common/both-replyurl.png)

     > [!NOTE]
     > Värdet är inte verkligt. Uppdatera det här värdet med den faktiska svars-URL: en. Kontakta [Uberflip support-teamet](mailto:support@uberflip.com)för att få det faktiska värdet. Du kan också se de mönster som visas i fönstret **grundläggande konfiguration av SAML** i Azure Portal.

   * Om du vill konfigurera programmet i SP-initierat SSO-läge väljer du **Ange ytterligare URL: er**i rutan **inloggnings-URL** :

     `https://app.uberflip.com/users/login`

     ![Information om enkel inloggning för Uberflip-domän och URL: er](common/both-signonurl.png)

1. I avsnittet **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , väljer du **Hämta** för att ladda ned **XML-metadata för federationsmetadata** från de angivna alternativen och sparar den på din dator.

   ![Hämtnings alternativ för XML-metadata för federationsmetadata](common/metadataxml.png)

1. I fönstret **Konfigurera Uberflip** kopierar du den eller de URL: er som du behöver:

   * **Inloggnings-URL**
   * **Azure AD-identifierare**
   * **Utloggnings-URL**

   ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-uberflip-single-sign-on"></a>Konfigurera Uberflip enkel inloggning

Om du vill konfigurera enkel inloggning på Uberflip-sidan måste du skicka den hämtade XML-koden för federationsmetadata och lämpliga kopierade URL: er från Azure Portal till [support teamet för Uberflip](mailto:support@uberflip.com). Uberflip-teamet ser till att SAML SSO-anslutningen är korrekt inställd på båda sidor.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du en test användare med namnet B. Simon i Azure Portal.

1. I Azure Portal i det vänstra fönstret väljer du **Azure Active Directory**  >  **användare**  >  **alla användare**.

    ![Alternativen användare och alla användare](common/users.png)

1. Överst på skärmen väljer du **+ ny användare**.

    ![Alternativ för ny användare](common/new-user.png)

1. I fönstret **användare** utför du följande steg:

    ![Fönstret användare](common/user-properties.png)

    1. I rutan **namn** anger du **BSimon**.
  
    1. I rutan **användar namn** anger du **BSimon \@ \<yourcompanydomain> . \<extension> **. Till exempel **BSimon \@ contoso.com**.

    1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.

    1. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du B. Simon för att använda enkel inloggning med Azure genom att ge åtkomst till Uberflip.

1. I Azure Portal väljer du **företags program**  >  **alla program**  >  **Uberflip**.

    ![Fönstret företags program](common/enterprise-applications.png)

1. I listan program väljer du **Uberflip**.

    ![Uberflip i listan program](common/all-applications.png)

1. I den vänstra rutan under **Hantera**väljer **du användare och grupper**.

    ![Alternativet "användare och grupper"](common/users-groups-blade.png)

1. Välj **+ Lägg till användare**och välj sedan **användare och grupper** i fönstret **Lägg till tilldelning** .

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

1. I fönstret **användare och grupper** väljer du **B Simon** i listan **användare** och väljer sedan **Välj** längst ned i fönstret.

1. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i fönstret **Välj roll** . Längst ned i fönstret väljer du **Välj**.

1. I fönstret **Lägg till tilldelning** väljer du **tilldela**.

### <a name="create-an-uberflip-test-user"></a>Skapa en Uberflip-test användare

En användare med namnet B. Simon skapas nu i Uberflip. Du behöver inte göra något för att skapa den här användaren. Uberflip stöder just-in-Time-etablering, som är aktiverat som standard. Om en användare som heter B. Simon inte redan finns i Uberflip skapas en ny efter autentiseringen.

> [!NOTE]
> Kontakta [Uberflip support-teamet](mailto:support@uberflip.com)om du behöver skapa en användare manuellt.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen av enkel inloggning med Azure AD med hjälp av portalen Mina appar.

När du väljer **Uberflip** i portalen för Mina appar, bör du loggas in automatiskt till den Uberflip-prenumeration som du ställer in enkel inloggning för. Mer information om My Apps-portalen finns i [komma åt och använda appar på portalen Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier för att integrera SaaS-program med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

* [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

* [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
