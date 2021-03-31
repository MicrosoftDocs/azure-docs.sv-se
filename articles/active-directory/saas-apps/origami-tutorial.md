---
title: 'Självstudie: Azure Active Directory integrering med origami | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och origami.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 02c79e8385c7a7e9d60a3dcbed603ca94cb1dc43
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92522278"
---
# <a name="tutorial-azure-active-directory-integration-with-origami"></a>Självstudie: Azure Active Directory integrering med origami

I den här självstudien får du lära dig hur du integrerar origami med Azure Active Directory (Azure AD).
Genom att integrera origami med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till origami.
* Du kan göra det möjligt för användarna att logga in automatiskt till origami (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med origami behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Origami-aktiverad prenumeration med enkel inloggning

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Origami stöder **SP** -INITIERAd SSO

## <a name="adding-origami-from-the-gallery"></a>Lägga till origami från galleriet

Om du vill konfigurera integreringen av origami i Azure AD måste du lägga till origami från galleriet i listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till origami från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök skriver du **origami**, väljer **origami** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

     ![Origami i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med origami baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i origami upprättas.

Om du vill konfigurera och testa enkel inloggning med origami i Azure AD måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera origami enkel inloggning](#configure-origami-single-sign-on)** – om du vill konfigurera de enskilda Sign-On inställningarna på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa origami test User](#create-origami-test-user)** – om du vill ha en motsvarighet till Britta Simon i origami som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med origami i Azure AD:

1. Välj **enkel inloggning** på sidan **origami** Application Integration i [Azure Portal](https://portal.azure.com/).

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning för origami-domän och URL: er](common/sp-signonurl.png)

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://live.origamirisk.com/origami/account/login?account=<companyname>`

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [origami client support team](https://wordpress.org/support/theme/origami) för att hämta värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **Konfigurera origami** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-origami-single-sign-on"></a>Konfigurera origami Single Sign-On

1. Logga in på origami-kontot med administratörs behörighet.

2. På menyn längst upp klickar du på **Admin**.
   
    ![Skärm bild som visar start sidan för origami med "admin" vald.](./media/origami-tutorial/tutorial_origami_51.png)

3. Utför följande steg på sidan installation av enkel inloggnings konfiguration:
   
    ![Skärm bild som visar sidan "enkel inloggnings konfiguration" med alternativet "aktivera enkel inloggning" markerat och text rutorna är markerade.](./media/origami-tutorial/tutorial_origami_531.png)

    a. Välj **aktivera enkel inloggning**.

    b. I text rutan **URL för identitets leverantörens inloggnings sida** klistrar du in värdet för **inloggnings-URL: en** som du har kopierat från Azure Portal.

    c. I text rutan **URL för identitets leverantörens URL** klistrar du in värdet för **Logga ut URL**, som du har kopierat från Azure Portal.

    d. Klicka på **Bläddra** för att ladda upp det certifikat som du har laddat ned från Azure Portal.

    e. Klicka på **Spara ändringar**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** anger du **brittasimon@yourcompanydomain.extension**  
    Till exempel BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till origami.

1. I Azure Portal väljer du **företags program**, väljer **alla program** och väljer sedan **origami**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **origami**.

    ![Origami-länken i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-origami-test-user"></a>Skapa origami test användare

I det här avsnittet skapar du en användare som heter Britta Simon i origami. 

1. Logga in på origami-kontot med administratörs behörighet.

2. På menyn längst upp klickar du på **Admin**.
   
    ![Skärm bild som visar origami-kontots start sida med "admin" vald.](./media/origami-tutorial/tutorial_origami_51.png)

3. I dialog rutan **användare och säkerhet** klickar du på **användare**.
   
    ![Skärm bild som visar dialog rutan "användare och säkerhet" där "användare" har valts.](./media/origami-tutorial/tutorial_origami_54.png)

4. Klicka på **Lägg till ny användare**.
   
    ![Skärm bild som visar knappen "Lägg till ny användare" vald.](./media/origami-tutorial/tutorial_origami_55.png)

5. I dialog rutan Lägg till ny användare utför du följande steg:
   
    ![Skärm bild som visar dialog rutan "Lägg till ny användare" med text rutorna "användar namn", "förnamn" och "efter namn" markerade.](./media/origami-tutorial/tutorial_origami_56.png)

    a. I text rutan **användar namn** anger du e-postadressen till användaren, t. ex. **brittasimon \@ contoso.com**.

    b. Skriv ett lösen ord i text rutan **lösen ord** .

    c. Skriv lösen ordet igen i text rutan **Bekräfta lösen ord** .

    d. I textrutan **Förnamn** skriver du förnamnet på användaren som **Britta**.

    e. I text rutan **efter namn** anger du det senaste namnet på användaren som **Simon**.

    f. Klicka på **Spara**.
   
    ![Skärm bild som visar knappen "Spara" vald.](./media/origami-tutorial/tutorial_origami_57.png)

6. Tilldela användar **roller** och **klient åtkomst** till användaren. 
   
    ![Konfigurera enkel inloggning](./media/origami-tutorial/tutorial_origami_58.png)

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen origami på åtkomst panelen, bör du loggas in automatiskt på den origami som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](./tutorial-list.md)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Vad är villkorlig åtkomst i Azure Active Directory?](../conditional-access/overview.md)