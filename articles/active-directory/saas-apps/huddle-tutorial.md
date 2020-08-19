---
title: 'Självstudie: Azure Active Directory integrering med Huddle | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Huddle.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.openlocfilehash: 77472e27a11614b7b5600f48e020afcceaeb967c
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88525912"
---
# <a name="tutorial-azure-active-directory-integration-with-huddle"></a>Självstudie: Azure Active Directory integrering med Huddle

I den här självstudien lär du dig att integrera Huddle med Azure Active Directory (AD Azure).
När du integrerar Huddle med Azure AD innebär det följande fördelar:

* Du kan styra vem som har åtkomst till Huddle från Azure AD.
* Du kan konfigurera inställningar så att dina användare automatiskt loggas in i Huddle (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Huddle behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Huddle-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Huddle stöder **SP- och IDP**-initierad enkel inloggning

## <a name="adding-huddle-from-the-gallery"></a>Lägga till Huddle från galleriet

För att konfigurera integreringen av Huddle i Azure AD måste du lägga till Huddle från galleriet till din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Huddle från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Huddle**, väljer **Huddle** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

     ![Huddle i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa enkel inloggning i Azure AD med Huddle baserat på testanvändaren **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Huddle upprättas.

För att kunna konfigurera och testa Azure AD enkel inloggning med Huddle slutför du följande byggstenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för Huddle](#configure-huddle-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Huddle-testanvändare](#create-huddle-test-user)** – för att ha en motsvarighet för Britta Simon i Huddle som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning i Azure AD med Huddle:

1. Välj **Enkel inloggning** på sidan för programintegrering av **Huddle** på [Azure-portalen](https://portal.azure.com/).

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, utför följande steg:

    > [!NOTE]
    > Din instans av Huddle identifieras automatiskt från den domän som du anger nedan.

    ![Huddle-domän och webbadresser med information om enkel inloggning](common/idp-intiated.png)

    a. I textrutan **Identifierare** skriver du en URL:

    ```http
    https://login.huddle.net
    https://login.huddle.com
    ```

    b. Skriv en URL i textrutan **Svars-URL**: 

    ```http
    https://login.huddle.net/saml/browser-sso
    https://login.huddle.com/saml/browser-sso
    https://login.huddle.com/saml/idp-initiated-sso
    ```

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![Huddle-domän och webbadresser med information om enkel inloggning](common/metadata-upload-additional-signon.png)

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster:

    ```http
    https://<customsubdomain>.huddle.com
    https://us.huddle.com
        ```

    > [!NOTE]
    > The Sign-on URL value is not real. Update this value with the actual Sign-On URL. Contact [Huddle Client support team](https://huddle.zendesk.com) to get this value.

6. On the **Set up Single Sign-On with SAML** page, in the **SAML Signing Certificate** section, click **Download** to download the **Certificate (Base64)** from the given options as per your requirement and save it on your computer.

    ![The Certificate download link](common/certificatebase64.png)

7. On the **Set up Huddle** section, copy the appropriate URL(s) as per your requirement.

    ![Copy configuration URLs](common/copy-configuration-urls.png)

    a. Login URL

    b. Azure Ad Identifier

    c. Logout URL

### Configure Huddle Single Sign-On

To configure single sign-on on **Huddle** side, you need to send the downloaded **Certificate (Base64)** and appropriate copied URLs from Azure portal to [Huddle support team](https://huddle.zendesk.com/). They set this setting to have the SAML SSO connection set properly on both sides.

> [!NOTE]
> Single sign-on needs to be enabled by the Huddle support team. You get a notification when the configuration has been completed.

### Create an Azure AD test user

The objective of this section is to create a test user in the Azure portal called Britta Simon.

1. In the Azure portal, in the left pane, select **Azure Active Directory**, select **Users**, and then select **All users**.

    ![The "Users and groups" and "All users" links](common/users.png)

2. Select **New user** at the top of the screen.

    ![New user Button](common/new-user.png)

3. In the User properties, perform the following steps.

    ![The User dialog box](common/user-properties.png)

    a. In the **Name** field enter **BrittaSimon**.
  
    b. In the **User name** field type **brittasimon\@yourcompanydomain.extension**  
    For example, BrittaSimon@contoso.com

    c. Select **Show password** check box, and then write down the value that's displayed in the Password box.

    d. Click **Create**.

### Assign the Azure AD test user

In this section, you enable Britta Simon to use Azure single sign-on by granting access to Huddle.

1. In the Azure portal, select **Enterprise Applications**, select **All applications**, then select **Huddle**.

    ![Enterprise applications blade](common/enterprise-applications.png)

2. In the applications list, select **Huddle**.

    ![The Huddle link in the Applications list](common/all-applications.png)

3. In the menu on the left, select **Users and groups**.

    ![The "Users and groups" link](common/users-groups-blade.png)

4. Click the **Add user** button, then select **Users and groups** in the **Add Assignment** dialog.

    ![The Add Assignment pane](common/add-assign-user.png)

5. In the **Users and groups** dialog select **Britta Simon** in the Users list, then click the **Select** button at the bottom of the screen.

6. If you are expecting any role value in the SAML assertion then in the **Select Role** dialog select the appropriate role for the user from the list, then click the **Select** button at the bottom of the screen.

7. In the **Add Assignment** dialog click the **Assign** button.

### Create Huddle test user

To enable Azure AD users to log in to Huddle, they must be provisioned into Huddle. In the case of Huddle, provisioning is a manual task.

**To configure user provisioning, perform the following steps:**

1. Log in to your **Huddle** company site as administrator.

2. Click **Workspace**.

3. Click **People \> Invite People**.

    ![People](./media/huddle-tutorial/ic787838.png "People")

4. In the **Create a new invitation** section, perform the following steps:
  
    ![New Invitation](./media/huddle-tutorial/ic787839.png "New Invitation")
  
    a. In the **Choose a team to invite people to join** list, select **team**.

    b. Type the **Email Address** of a valid Azure AD account you want to provision in to **Enter email address for people you'd like to invite** textbox.

    c. Click **Invite**.

    > [!NOTE]
    > The Azure AD account holder will receive an email including a link to confirm the account before it becomes active.

> [!NOTE]
> You can use any other Huddle user account creation tools or APIs provided by Huddle to provision Azure AD user accounts.

### Test single sign-on

In this section, you test your Azure AD single sign-on configuration using the Access Panel.

When you click the Huddle tile in the Access Panel, you should be automatically signed in to the Huddle for which you set up SSO. For more information about the Access Panel, see [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## Additional Resources

- [List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [What is Conditional Access in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

