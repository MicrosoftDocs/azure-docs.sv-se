---
title: 'Självstudie: Azure Active Directory integrering med PageDNA | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och PageDNA.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.openlocfilehash: 543085eaea993f6e464a501ffc93befd7589a009
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88554149"
---
# <a name="tutorial-azure-active-directory-integration-with-pagedna"></a>Självstudie: Azure Active Directory integrering med PageDNA

I den här självstudien får du lära dig hur du integrerar PageDNA med Azure Active Directory (Azure AD).

Genom att integrera PageDNA med Azure AD får du följande fördelar:

* I Azure AD kan du kontrol lera vem som har åtkomst till PageDNA.
* Du kan göra det möjligt för användarna att logga in automatiskt på PageDNA (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats: Azure Portal.

Mer information om program vara som en tjänst (SaaS) för program integrering med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med PageDNA behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.
* En PageDNA-prenumeration med enkel inloggning aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning i Azure AD i en test miljö och integrerar PageDNA med Azure AD.

PageDNA stöder följande funktioner:

* SP-initierad enkel inloggning (SSO).

* Just-in-Time-etablering av användare.

## <a name="add-pagedna-from-the-azure-marketplace"></a>Lägg till PageDNA från Azure Marketplace

Om du vill konfigurera integreringen av PageDNA i Azure AD måste du lägga till PageDNA från Azure Marketplace till din lista över hanterade SaaS-appar:

1. Logga in på [Azure-portalen](https://portal.azure.com?azure-portal=true).
1. Välj **Azure Active Directory** i den vänstra rutan.

    ![Alternativet Azure Active Directory](common/select-azuread.png)

1. Gå till **Företagsprogram** och välj sedan **Alla program**.

    ![Fönstret Företagsprogram](common/enterprise-applications.png)

1. Om du vill lägga till ett nytt program väljer du **+ nytt program** överst i fönstret.

    ![Alternativet nytt program](common/add-new-app.png)

1. Skriv **PageDNA**i rutan Sök. I Sök resultaten väljer du **PageDNA**och väljer sedan **Lägg till** för att lägga till programmet.

    ![PageDNA i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med PageDNA baserat på en test användare som heter **Britta Simon**. För att enkel inloggning ska fungera måste du upprätta en länk mellan en Azure AD-användare och en relaterad användare i PageDNA.

Om du vill konfigurera och testa enkel inloggning med PageDNA i Azure AD måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** så att användarna kan använda den här funktionen.
1. **[Konfigurera PageDNA enkel inloggning](#configure-pagedna-single-sign-on)** för att konfigurera inställningarna för enkel inloggning på program sidan.
1. **[Skapa en Azure AD test-användare](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med Britta Simon.
1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** för att aktivera Britta Simon att använda enkel inloggning i Azure AD.
1. **[Skapa en PageDNA-test användare](#create-a-pagedna-test-user)** så att det finns en användare med namnet Britta Simon i PageDNA som är länkad till Azure AD-användaren med namnet Britta Simon.
1. **[Testa enkel inloggning](#test-single-sign-on)** för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med PageDNA i Azure AD:

1. Välj **enkel inloggning**på sidan **PageDNA** Application Integration i [Azure Portal](https://portal.azure.com/).

    ![Konfigurera alternativ för enkel inloggning](common/select-sso.png)

1. I fönstret **Välj en enkel inloggnings metod** väljer du **SAML/WS-utfodras** läge för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

1. I fönstret **Konfigurera enkel inloggning med SAML** väljer du **Redigera** (Penn ikonen) för att öppna fönstret **grundläggande SAML-konfiguration** .

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I fönstret **grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning för PageDNA-domän och URL: er](common/sp-identifier.png)

    1. I rutan **inloggnings-URL** anger du en URL genom att använda något av följande mönster:

        ```https
        https://stores.pagedna.com/<your site>
        https://<your domain>
        https://<your domain>/<your site>
        https://www.nationsprint.com/<your site>
        ```

    1. I rutan **identifierare (enhets-ID)** anger du en URL genom att använda något av följande mönster:

        ```https
        https://stores.pagedna.com/<your site>/saml2ep.cgi
        https://www.nationsprint.com/<your site>/saml2ep.cgi
        ```

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [PageDNA support-teamet](mailto:success@pagedna.com)om du vill hämta dessa värden. Du kan också se de mönster som visas i fönstret **grundläggande konfiguration av SAML** i Azure Portal.

1. I avsnittet **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , väljer du **Ladda ned** för att ladda ned **certifikat (RAW)** från de angivna alternativen och sparar dem på din dator.

    ![Hämtnings alternativet för certifikat (RAW)](common/certificateraw.png)

1. I avsnittet **Konfigurera PageDNA** kopierar du den eller de URL: er som du behöver:

   * **Inloggnings-URL**
   * **Azure AD-identifierare**
   * **Utloggnings-URL**

    ![Kopiera URL: en för konfigurationen](common/copy-configuration-urls.png)

### <a name="configure-pagedna-single-sign-on"></a>Konfigurera PageDNA enkel inloggning

Om du vill konfigurera enkel inloggning på PageDNA-sidan skickar du det nedladdade certifikatet (RAW) och lämpliga kopierade URL: er från Azure Portal till [support teamet för PageDNA](mailto:success@pagedna.com). PageDNA-teamet ser till att SAML SSO-anslutningen är korrekt inställd på båda sidor.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du en testanvändare i Azure-portalen med namnet Britta Simon.

1. I Azure Portal i det vänstra fönstret väljer du **Azure Active Directory**    >  **användare**  >  **alla användare**.

    ![Alternativen användare och alla användare](common/users.png)

1. Överst på skärmen väljer du **+ ny användare**.

    ![Alternativ för ny användare](common/new-user.png)

1. I fönstret **användare** utför du följande steg:

    ![Fönstret användare](common/user-properties.png)

    1. I rutan **Namn** anger du **BrittaSimon**.
  
    1. I rutan **användar namn** anger du **BrittaSimon \@ \<yourcompanydomain> . \<extension> **. Till exempel **BrittaSimon \@ contoso.com**.

    1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.

    1. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du User Britta Simon till att använda enkel inloggning med Azure genom att ge användaren åtkomst till PageDNA.

1. I Azure Portal väljer du **företags program**  >  **alla program**  >  **PageDNA**.

    ![Fönstret företags program](common/enterprise-applications.png)

1. I listan program väljer du **PageDNA**.

    ![PageDNA i listan program](common/all-applications.png)

1. I den vänstra rutan under **Hantera**väljer **du användare och grupper**.

    ![Alternativet "användare och grupper"](common/users-groups-blade.png)

1. Välj **+ Lägg till användare**och välj sedan **användare och grupper** i fönstret **Lägg till tilldelning** .

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

1. I fönstret **användare och grupper** väljer du **Britta Simon** i listan **användare** och väljer sedan **Välj** längst ned i fönstret.

1. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i fönstret **Välj roll** . Längst ned i fönstret väljer du **Välj**.

1. I fönstret **Lägg till tilldelning** väljer du **tilldela**.

### <a name="create-a-pagedna-test-user"></a>Skapa en PageDNA-test användare

En användare med namnet Britta Simon skapas nu i PageDNA. Du behöver inte göra något för att skapa den här användaren. PageDNA stöder just-in-Time-etablering, som är aktiverat som standard. Om en användare som heter Britta Simon inte redan finns i PageDNA, skapas en ny efter autentiseringen.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen av enkel inloggning med Azure AD med hjälp av portalen Mina appar.

När du väljer **PageDNA** i portalen för Mina appar, bör du loggas in automatiskt till den PageDNA-prenumeration som du ställer in enkel inloggning för. Mer information om My Apps-portalen finns i [komma åt och använda appar på portalen Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier för att integrera SaaS-program med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

* [Enkel inloggning till program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

* [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

