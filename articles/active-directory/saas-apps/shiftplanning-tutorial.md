---
title: 'Självstudie: Azure Active Directory integrering med mänskligheten | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Humanity.
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
ms.openlocfilehash: d99355a88a52e318b231d9032ede770ee0ba5208
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88548694"
---
# <a name="tutorial-azure-active-directory-integration-with-humanity"></a>Självstudie: Azure Active Directory integrering med mänskligheten

I den här självstudien lär du dig att integrera Humanity med Azure Active Directory (AD Azure).
Integreringen av Humanity med Azure AD medför följande fördelar:

* Du kan i Azure AD styra vem som har åtkomst till Humanity.
* Du kan göra så att dina användare automatiskt loggas in på Humanity (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Humanity behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Humanity-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Humanity har stöd för **SP**-initierad enkel inloggning

## <a name="adding-humanity-from-the-gallery"></a>Lägga till Humanity från galleriet

För att konfigurera integreringen av Humanity i Azure AD behöver du lägga till Humanity från galleriet till din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Humanity från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Humanity**, väljer **Humanity** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

     ![Humanity i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning i Azure AD med Humanity baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Humanity upprättas.

För att kunna konfigurera och testa enkel inloggning i Azure AD med Humanity slutför du följande byggstenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för Humanity](#configure-humanity-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Humanity-testanvändare](#create-humanity-test-user)** – för att ha en motsvarighet för Britta Simon i Humanity som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning i Azure AD med Humanity:

1. I [Azure-portalen](https://portal.azure.com/) går du till programintegreringssidan för **Humanity** och väljer **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning med Humanity-domän och URL:er](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://company.humanity.com/includes/saml/`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://company.humanity.com/app/`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [kundsupporten för Humanity](https://www.humanity.com/support/) och be om dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

4. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **Konfigurera Humanity** kopierar du lämpliga URL:er efter behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-humanity-single-sign-on"></a>Konfigurera enkel inloggning för Humanity

1. I ett annat webbläsarfönster loggar du in på din **Humanity**-företagswebbplats som administratör.

2. På menyn längst upp klickar du på **Admin**.

    ![Administratör](./media/shiftplanning-tutorial/iC786619.png "Administratör")
3. Under **Integrering** klickar du på **Enkel inloggning**.

    ![Enkel inloggning](./media/shiftplanning-tutorial/iC786620.png "för Aha!")

4. I avsnittet **enkel inloggning** utför du följande steg:

    ![Enkel inloggning](./media/shiftplanning-tutorial/iC786905.png "för Aha!")

    a. Välj **SAML-aktiverat **.

    b. Välj **Allow Password Login** (Tillåt inloggning med lösenord).

    c. I textrutan **SAML Issuer URL** (URL för SAML-utfärdare) klistrar du in värdet för den **inloggnings-URL** som du har kopierat från Azure-portalen.

    d. I textrutan **Remote Logout URL** (URL för fjärrutloggning) klistrar du in värdet för den **utloggnings-URL** som du har kopierat från Azure-portalen.

    e. Öppna ditt base-64-kodade certifikat i Anteckningar, kopiera dess innehåll till Urklipp och klistra sedan in det i textrutan **X.509-certifikat**.

    f. Klicka på **Spara inställningar**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **användar namn** skriver du **brittasimon \@ yourcompanydomain. extension**  
    Till exempel BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till Humanity.

1. I Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **Humanity**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **Humanity**.

    ![Länken för Humanity i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-humanity-test-user"></a>Skapa Humanity-testanvändare

För att göra det möjligt för Azure AD-användare att logga in på Humanity måste de etableras till Humanity. När det gäller Humanity är etablering en manuell uppgift.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på din **Humanity**-företagswebbplats som administratör.

2. Klicka på **Admin**.

    ![Administratör](./media/shiftplanning-tutorial/iC786619.png "Administratör")

3. Klicka på **Staff** (Personal).

    ![Bilda](./media/shiftplanning-tutorial/ic786623.png "Bilda")

4. Under **Åtgärder** klickar du på **Add Employees** (Lägg till medarbetare).

    ![Lägg till medarbetare](./media/shiftplanning-tutorial/iC786624.png "Lägg till medarbetare")

5. I avsnittet **Add Employees** (Lägg till medarbetare) avsnittet utför du följande steg:

    ![Spara medarbetare](./media/shiftplanning-tutorial/iC786625.png "Spara medarbetare")

    a. Ange **förnamn**, **efter namn**och **e-postadress** för ett giltigt Azure AD-konto som du vill etablera i de relaterade text rutorna.

    b. Klicka på **Save Employees** (Spara medarbetare).

> [!NOTE]
> Du kan använda andra verktyg för mänskligheten av användar konton eller API: er som tillhandahålls av mänskligheten för att etablera Azure AD-användarkonton.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Humanity-panelen i åtkomstpanelen bör du automatiskt loggas in på Humanity som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)