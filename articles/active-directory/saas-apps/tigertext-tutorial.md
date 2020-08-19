---
title: 'Självstudie: Azure Active Directory integrering med TigerText Secure Messenger | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och TigerText Secure Messenger.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: 9dff60767e923bad1322b689acd98e69eb9c2ac6
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88517005"
---
# <a name="tutorial-azure-active-directory-integration-with-tigertext-secure-messenger"></a>Självstudie: Azure Active Directory integrering med TigerText Secure Messenger

I den här självstudien får du lära dig att integrera TigerText Secure Messenger med Azure Active Directory (Azure AD).

Att integrera TigerText Secure Messenger med Azure AD ger följande fördelar:

* Du kan styra Azure AD som har åtkomst till TigerText Secure Messenger.
* Du kan göra det möjligt för användarna att logga in automatiskt till TigerText Secure Messenger (enkel inloggning) med deras Azure AD-konton.
* Du kan hantera dina konton på en central plats: Azure Portal.

Mer information om program vara som en tjänst (SaaS) för program integrering med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med TigerText Secure Messenger behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.
* En TigerText säker Messenger-prenumeration med enkel inloggning aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning i Azure AD i en test miljö och integrerar TigerText Secure Messenger med Azure AD.

TigerText Secure Messenger stöder SP-initierad enkel inloggning (SSO).

## <a name="add-tigertext-secure-messenger-from-the-azure-marketplace"></a>Lägg till TigerText Secure Messenger från Azure Marketplace

Om du vill konfigurera integrationen av TigerText Secure Messenger i Azure AD måste du lägga till TigerText Secure Messenger från Azure Marketplace till din lista över hanterade SaaS-appar:

1. Logga in på [Azure-portalen](https://portal.azure.com?azure-portal=true).
1. Välj **Azure Active Directory** i den vänstra rutan.

    ![Alternativet Azure Active Directory](common/select-azuread.png)

1. Gå till **Företagsprogram** och välj sedan **Alla program**.

    ![Fönstret Företagsprogram](common/enterprise-applications.png)

1. Om du vill lägga till ett nytt program väljer du **+ nytt program** överst i fönstret.

    ![Alternativet nytt program](common/add-new-app.png)

1. Skriv **TigerText Secure Messenger**i rutan Sök. I Sök resultaten väljer du **TigerText Secure Messenger**och väljer sedan **Lägg till** för att lägga till programmet.

    ![TigerText Secure Messenger i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med TigerText Secure Messenger baserat på en test användare som heter **Britta Simon**. För att enkel inloggning ska fungera måste du upprätta en länk mellan en Azure AD-användare och en relaterad användare i TigerText Secure Messenger.

Om du vill konfigurera och testa enkel inloggning med Azure AD med TigerText Secure Messenger måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** så att användarna kan använda den här funktionen.
1. **[Konfigurera TigerText Secure Messenger enkel inloggning](#configure-tigertext-secure-messenger-single-sign-on)** för att konfigurera inställningarna för enkel inloggning på program sidan.
1. **[Skapa en Azure AD test-användare](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med Britta Simon.
1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** för att aktivera Britta Simon att använda enkel inloggning i Azure AD.
1. **[Skapa en TigerText säker Messenger-test användare](#create-a-tigertext-secure-messenger-test-user)** så att det finns en användare med namnet Britta Simon i TigerText Secure Messenger som är länkad till Azure AD-användaren med namnet Britta Simon.
1. **[Testa enkel inloggning](#test-single-sign-on)** för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med TigerText Secure Messenger i Azure AD:

1. Välj **enkel inloggning**på sidan **TigerText Secure Messenger** Application Integration i [Azure Portal](https://portal.azure.com/).

    ![Konfigurera alternativ för enkel inloggning](common/select-sso.png)

1. I fönstret **Välj en enkel inloggnings metod** väljer du **SAML/WS-utfodras** läge för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

1. I fönstret **Konfigurera enkel inloggning med SAML** väljer du **Redigera** (Penn ikonen) för att öppna fönstret **grundläggande SAML-konfiguration** .

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I fönstret **grundläggande SAML-konfiguration** utför du följande steg:

    ![TigerText säker Messenger-domän och URL-information för enkel inloggning](common/sp-identifier.png)

    1. I rutan **inloggnings-URL** anger du en URL:

       `https://home.tigertext.com`

    1. I rutan **identifierare (enhets-ID)** anger du en URL med hjälp av följande mönster:

       `https://saml-lb.tigertext.me/v1/organization/<instance ID>`

    > [!NOTE]
    > Värdet för **identifieraren (enhets-ID)** är inte verkligt. Uppdatera det här värdet med den faktiska identifieraren. Kontakta [support teamet för TigerText Secure Messenger](mailto:prosupport@tigertext.com)för att få värdet. Du kan också se de mönster som visas i fönstret **grundläggande konfiguration av SAML** i Azure Portal.

1. I avsnittet **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , väljer du **Hämta** för att ladda ned **XML-metadata för federationsmetadata** från de angivna alternativen och sparar den på din dator.

    ![Hämtnings alternativ för XML-metadata för federationsmetadata](common/metadataxml.png)

1. I avsnittet **Konfigurera TigerText Secure Messenger** kopierar du den eller de URL: er som du behöver:

   * **Inloggnings-URL**
   * **Azure AD-identifierare**
   * **Utloggnings-URL**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-tigertext-secure-messenger-single-sign-on"></a>Konfigurera TigerText säker Messenger enkel inloggning

Om du vill konfigurera enkel inloggning på den TigerText säkra Messenger-sidan måste du skicka den hämtade XML-koden för federationsmetadata och lämpliga kopierade URL: er från Azure Portal till [support teamet för TigerText Secure Messenger](mailto:prosupport@tigertext.com). TigerText Secure Messenger-teamet ser till att SAML SSO-anslutningen är korrekt inställd på båda sidor.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du en testanvändare med namnet Britta Simon i Azure-portalen.

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

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att ge dem åtkomst till TigerText-säkra Messenger.

1. I Azure Portal väljer du **företags program**  >  **alla program**  >  **TigerText Secure Messenger**.

    ![Fönstret företags program](common/enterprise-applications.png)

1. I listan program väljer du **TigerText Secure Messenger**.

    ![TigerText Secure Messenger i program listan](common/all-applications.png)

1. I den vänstra rutan under **Hantera**väljer **du användare och grupper**.

    ![Alternativet "användare och grupper"](common/users-groups-blade.png)

1. Välj **+ Lägg till användare**och välj sedan **användare och grupper** i fönstret **Lägg till tilldelning** .

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

1. I fönstret **användare och grupper** väljer du **Britta Simon** i listan **användare** och väljer sedan **Välj** längst ned i fönstret.

1. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i fönstret **Välj roll** . Längst ned i fönstret väljer du **Välj**.

1. I fönstret **Lägg till tilldelning** väljer du **tilldela**.

### <a name="create-a-tigertext-secure-messenger-test-user"></a>Skapa en TigerText säker Messenger-test användare

I det här avsnittet skapar du en användare som heter Britta Simon i TigerText Secure Messenger. Arbeta med [TigerText Secure Messenger support team](mailto:prosupport@tigertext.com) för att lägga till Britta Simon som en användare i TigerText Secure Messenger. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen av enkel inloggning med Azure AD med hjälp av portalen Mina appar.

När du väljer **TigerText Secure Messenger** i portalen Mina appar, bör du loggas in automatiskt till den TigerText-säkra Messenger-prenumeration som du ställer in enkel inloggning för. Mer information om My Apps-portalen finns i [komma åt och använda appar på portalen Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier för att integrera SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

* [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

* [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
