---
title: 'Självstudie: Azure Active Directory integration med närvaro hanterings tjänster | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory-och närvaro hanterings tjänster.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: jeedes
ms.openlocfilehash: df04cc41f2b5947bb0cf23715a857a3449627f23
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88550268"
---
# <a name="tutorial-azure-active-directory-integration-with-attendance-management-services"></a>Självstudie: Azure Active Directory integrering med närvaro hanterings tjänster

I den här självstudien får du lära dig hur du integrerar närvaro hanterings tjänster med Azure Active Directory (Azure AD).
Att integrera närvaro hanterings tjänster med Azure AD ger följande fördelar:

* Du kan styra i Azure AD som har åtkomst till närvaro hanterings tjänster.
* Du kan göra det möjligt för användarna att logga in automatiskt till närvaro hanterings tjänster (enkel inloggning) med deras Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med närvaro hanterings tjänster behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/)
* Aktive rad prenumeration för enkel inloggning för närvaro hanterings tjänster

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Närvaro hanterings tjänster stöder **SP** -INITIERAd SSO

## <a name="adding-attendance-management-services-from-the-gallery"></a>Lägga till närvaro hanterings tjänster från galleriet

Om du vill konfigurera integrering av närvaro hanterings tjänster i Azure AD måste du lägga till närvaro hanterings tjänster från galleriet i listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till närvaro hanterings tjänster från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök skriver du **närvaro hanterings tjänster**, väljer **närvaro hanterings tjänster** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

    ![Närvaro hanterings tjänster i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning för Azure AD med närvaro hanterings tjänster baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i närvaro hanterings tjänster upprättas.

Om du vill konfigurera och testa enkel inloggning med närvaro hanterings tjänster i Azure AD måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för närvaro hanterings tjänster](#configure-attendance-management-services-single-sign-on)** för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa en test användare för närvaro hanterings tjänster](#create-attendance-management-services-test-user)** för att få en motsvarighet till Britta Simon i närvaro hanterings tjänster som är kopplade till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med närvaro hanterings tjänster i Azure AD:

1. I [Azure Portal](https://portal.azure.com/)väljer du **enkel inloggning**på sidan för program integration i **närvaro hanterings tjänster** .

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning för närvaro hanterings tjänster och URL: er](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://id.obc.jp/<tenant information >/`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://id.obc.jp/<tenant information >/`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [närvaro hanterings tjänstens klient support team](https://www.obcnet.jp/) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **Konfigurera närvaro hanterings tjänster** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-attendance-management-services-single-sign-on"></a>Konfigurera enkel inloggning för närvaro hanterings tjänster

1. Logga in på företagets webbplats för närvaro hanterings tjänster som administratör i ett annat webbläsarfönster.

1. Klicka på **SAML-autentisering** under **avsnittet säkerhets hantering**.

    ![Konfiguration av närvaro hanterings tjänster](./media/attendancemanagementservices-tutorial/user1.png)

1. Utför följande steg:

    ![Konfiguration av närvaro hanterings tjänster](./media/attendancemanagementservices-tutorial/user2.png)

    a. Välj **Använd SAML-autentisering**.

    b. I text rutan **identifierare** klistrar du in värdet för **Azure AD Identifier** -värdet, som du har kopierat från Azure Portal.

    c. I text rutan för **autentiseringens slut punkts-URL** klistrar du in värdet för **inloggnings-URL** -värdet som du har kopierat från Azure Portal.

    d. Klicka på **Välj en fil** för att överföra certifikatet som du laddade ned från Azure AD.

    e. Välj **inaktivera lösenordsautentisering**.

    f. Klicka på **registrering**

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **användar namn** `brittasimon@yourcompanydomain.extension` . Till exempel BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till närvaro hanterings tjänster.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **närvaro hanterings tjänster**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **närvaro hanterings tjänster**.

    ![Länken närvaro hanterings tjänster i listan program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-attendance-management-services-test-user"></a>Test användare för att skapa närvaro hanterings tjänster

Om du vill att Azure AD-användare ska kunna logga in på närvaro hanterings tjänster måste de tillhandahållas i närvaro hanterings tjänster. När det gäller närvaro hanterings tjänster är etableringen en manuell uppgift.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på företagets webbplats för närvaro hanterings tjänster som administratör.

1. Klicka på **användar hantering** under **avsnittet säkerhets hantering**.

    ![Lägga till medarbetare](./media/attendancemanagementservices-tutorial/user5.png)

1. Klicka på **ny regel inloggning**.

    ![Lägga till medarbetare](./media/attendancemanagementservices-tutorial/user3.png)

1. I avsnittet **OBCiD information** , utför följande steg:

    ![Lägga till medarbetare](./media/attendancemanagementservices-tutorial/user4.png)

    a. I text rutan **OBCiD** anger du e-postmeddelandet som användare `BrittaSimon\@contoso.com` .

    b. I textrutan **Password** (Lösenord) skriver du lösenordet för användaren.

    c. Klicka på **registrering**

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen närvaro hanterings tjänster på åtkomst panelen, bör du loggas in automatiskt på de närvaro hanterings tjänster som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)