---
title: 'Självstudie: Azure Active Directory integrering med TurboRater | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och TurboRater.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 3/8/2019
ms.author: jeedes
ms.openlocfilehash: 0c22993baa6a9095bddba67bdc9d18a40021db6c
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88546397"
---
# <a name="tutorial-azure-active-directory-integration-with-turborater"></a>Självstudie: Azure Active Directory integrering med TurboRater

I den här självstudien får du lära dig hur du integrerar TurboRater med Azure Active Directory (Azure AD).

Genom att integrera TurboRater med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till TurboRater.
* Du kan göra det möjligt för användarna att logga in automatiskt på TurboRater (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats: Azure Portal.

Mer information om program vara som en tjänst (SaaS) för program integrering med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med TurboRater behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.
* En TurboRater-prenumeration med enkel inloggning aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

TurboRater har stöd för IDP-initierad enkel inloggning (SSO).

## <a name="add-turborater-from-the-azure-marketplace"></a>Lägg till TurboRater från Azure Marketplace

Om du vill konfigurera integreringen av TurboRater i Azure AD måste du lägga till TurboRater från Azure Marketplace till din lista över hanterade SaaS-appar:

1. Logga in på [Azure-portalen](https://portal.azure.com?azure-portal=true).
1. Välj **Azure Active Directory** i den vänstra rutan.

    ![Alternativet Azure Active Directory](common/select-azuread.png)

1. Gå till **Företagsprogram** och välj sedan **Alla program**.

    ![Alternativet företags program](common/enterprise-applications.png)

1. Om du vill lägga till ett nytt program väljer du **+ nytt program** överst i fönstret.

    ![Alternativet nytt program](common/add-new-app.png)

1. Skriv **TurboRater**i rutan Sök. I Sök resultaten väljer du **TurboRater**och väljer sedan **Lägg till** för att lägga till programmet.

    ![TurboRater i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med TurboRater baserat på en test användare som heter  **B Simon**. För att enkel inloggning ska fungera måste du upprätta en länk mellan en Azure AD-användare och en relaterad användare i TurboRater.

Om du vill konfigurera och testa enkel inloggning med TurboRater i Azure AD måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** så att användarna kan använda den här funktionen.
1. **[Konfigurera TurboRater enkel inloggning](#configure-turborater-single-sign-on)** för att konfigurera inställningarna för enkel inloggning på program sidan.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** för att testa enkel inloggning i Azure AD med B. Simon.
1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** att aktivera B. Simon för att använda enkel inloggning i Azure AD.
1. **[Skapa en TurboRater-test användare](#create-a-turborater-test-user)** så att det finns en användare med namnet b. Simon i TurboRater som är länkad till Azure AD-användaren med namnet b. Simon.
1. **[Testa enkel inloggning](#test-single-sign-on)** för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med TurboRater i Azure AD:

1. Välj **enkel inloggning**på sidan **TurboRater** Application Integration i [Azure Portal](https://portal.azure.com/).

    ![Konfigurera alternativ för enkel inloggning](common/select-sso.png)

1. I fönstret **Välj en enkel inloggnings metod** väljer du **SAML/WS-utfodras** läge för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

1. På sidan **Konfigurera enkel inloggning med SAML** väljer du **Redigera** (Penn ikonen) för att öppna fönstret **grundläggande SAML-konfiguration** .

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I fönstret **grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning för TurboRater-domän och URL: er](common/idp-intiated.png)

    1. I rutan **identifierare (enhets-ID)** anger du en URL:

       `https://www.itcdataservices.com`

    1. I rutan **svars-URL (intyg om mottagar tjänst)** anger du en URL med hjälp av följande mönster:

       | Miljö | URL |
       | ---------------| --------------- |
       | Testa  | `https://ratingqa.itcdataservices.com/webservices/imp/saml/login` |
       | Direktsändning  | `https://www.itcratingservices.com/webservices/imp/saml/login` |

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren och svars-URL:en. Kontakta [TurboRater support-teamet](https://www.getitc.com/support)om du vill hämta dessa värden. Du kan också se de mönster som visas i fönstret **grundläggande konfiguration av SAML** i Azure Portal.

1. I avsnittet **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , väljer du **Hämta** för att ladda ned **XML-metadata för federationsmetadata** från de angivna alternativen och sparar den på din dator.

    ![Hämtnings alternativ för XML-metadata för federationsmetadata](common/metadataxml.png)

1. I avsnittet **Konfigurera TurboRater** kopierar du den eller de URL: er som du behöver:

   * **Inloggnings-URL**
   * **Azure AD-identifierare**
   * **Utloggnings-URL**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-turborater-single-sign-on"></a>Konfigurera TurboRater enkel inloggning

Om du vill konfigurera enkel inloggning på TurboRater-sidan måste du skicka den hämtade XML-koden för federationsmetadata och lämpliga kopierade URL: er från Azure Portal till [support teamet för TurboRater](https://www.getitc.com/support). TurboRater-teamet ser till att SAML SSO-anslutningen är korrekt inställd på båda sidor.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du en testanvändare med namnet Britta Simon i Azure-portalen.

1. I Azure Portal i det vänstra fönstret väljer du **Azure Active Directory**    >  **användare**  >  **alla användare**.

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

I det här avsnittet aktiverar du B. Simon för att använda enkel inloggning med Azure genom att ge åtkomst till TurboRater.

1. I Azure Portal väljer du **företags program**  >  **alla program**  >  **TurboRater**.

    ![Fönstret företags program](common/enterprise-applications.png)

1. I listan program väljer du **TurboRater**.

    ![TurboRater i listan program](common/all-applications.png)

1. I den vänstra rutan under **Hantera**väljer **du användare och grupper**.

    ![Alternativet "användare och grupper"](common/users-groups-blade.png)

1. Välj **+ Lägg till användare**och välj sedan **användare och grupper** i fönstret **Lägg till tilldelning** .

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

1. I fönstret **användare och grupper** väljer du **B. Simon** i listan **användare** och väljer sedan **Välj** längst ned i fönstret.

1. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i fönstret **Välj roll** . Längst ned i fönstret väljer du **Välj**.

1. I fönstret **Lägg till tilldelning** väljer du **tilldela**.

### <a name="create-a-turborater-test-user"></a>Skapa en TurboRater-test användare

I det här avsnittet skapar du en användare som heter B. Simon i TurboRater. Arbeta med [TurboRater support team](https://www.getitc.com/support) för att lägga till B. Simon som en användare i TurboRater. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen av enkel inloggning med Azure AD med hjälp av portalen Mina appar.

När du väljer **TurboRater** i portalen för Mina appar, bör du loggas in automatiskt till den TurboRater-prenumeration som du ställer in enkel inloggning för. Mer information om My Apps-portalen finns i [komma åt och använda appar på portalen Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier för att integrera SaaS-program med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

* [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

* [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
