---
title: 'Självstudie: Azure Active Directory integrering med Jitbit-supportavdelningen | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory-och Jitbit-supportavdelningen.
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
ms.openlocfilehash: 5d4900713cd8d96180bf74b300a738a8b676421e
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88547162"
---
# <a name="tutorial-azure-active-directory-integration-with-jitbit-helpdesk"></a>Självstudie: Azure Active Directory integrering med Jitbit-supportavdelningen

I den här självstudien får du lära dig att integrera Jitbit-supportavdelningen med Azure Active Directory (Azure AD).
Genom att integrera Jitbit-supportavdelningen med Azure AD får du följande fördelar:

* Du kan kontrol lera Azure AD som har åtkomst till Jitbit-supportavdelningen.
* Du kan göra det möjligt för användarna att logga in automatiskt till Jitbit Helpdesk (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Jitbit Helpdesk behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Aktive rad prenumeration för Jitbit Helpdesk-enkel inloggning

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Jitbit Helpdesk stöder **SP** -INITIERAd SSO

## <a name="adding-jitbit-helpdesk-from-the-gallery"></a>Lägga till Jitbit-supportavdelningen från galleriet

Om du vill konfigurera integreringen av Jitbit-supportavdelningen i Azure AD måste du lägga till Jitbit-supportavdelningen från galleriet till din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Jitbit-supportavdelningen från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Jitbit Helpdesk**, väljer **Jitbit supportavdelningen** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

     ![Jitbit supportavdelningen i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med Jitbit-supportavdelningen baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i Jitbit-supportavdelningen upprättas.

Om du vill konfigurera och testa enkel inloggning med Jitbit-supportavdelningen i Azure AD måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Jitbit Helpdesk Single Sign-on](#configure-jitbit-helpdesk-single-sign-on)** -för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Jitbit support test User](#create-jitbit-helpdesk-test-user)** – om du vill ha en motsvarighet till Britta Simon i Jitbit Helpdesk som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med Jitbit supportavdelningen i Azure AD:

1. På sidan [Azure Portal](https://portal.azure.com/)på sidan för **Jitbit-Helpdesk** -programintegration väljer du **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning för Jitbit Helpdesk-domän och URL: er](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: 
    | |
    | ----------------------------------------|
    | `https://<hostname>/helpdesk/User/Login`|
    | `https://<tenant-name>.Jitbit.com`|
    | |
    
    > [!NOTE] 
    > Det här värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [support teamet för Jitbit supportavdelningen](https://www.jitbit.com/support/) för att få det här värdet.

    b. I text rutan **identifierare (enhets-ID)** anger du en URL enligt följande: `https://www.jitbit.com/web-helpdesk/`

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **Konfigurera Jitbit support** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-jitbit-helpdesk-single-sign-on"></a>Konfigurera enkel inloggning för Jitbit-Helpdesk

1. Logga in på företags platsen för Jitbit-supportavdelningen som administratör i ett annat webbläsarfönster.

1. I verktygsfältet högst upp klickar du på **Administration**.

    ![Administration](./media/jitbit-helpdesk-tutorial/ic777681.png "Administration")

1. Klicka på **allmänna inställningar**.

    ![Användare, företag och behörigheter](./media/jitbit-helpdesk-tutorial/ic777680.png "Användare, företag och behörigheter")

1. I avsnittet konfiguration av **autentiseringsinställningar** utför du följande steg:

    ![Autentiseringsinställningar](./media/jitbit-helpdesk-tutorial/ic777683.png "Autentiseringsinställningar")

    a. Välj **Aktivera SAML 2,0 enkel inloggning**för att logga in med enkel inloggning (SSO) med **OneLogin**.

    b. I text rutan **slut punkts-URL** klistrar du in värdet för **inloggnings-URL: en** som du har kopierat från Azure Portal.

    c. Öppna ditt **bas-64-** kodade certifikat i anteckningar, kopiera innehållet i det till Urklipp och klistra in det i rutan **X. 509-certifikat**

    d. Klicka på **Spara ändringar**.

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

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Jitbit-supportavdelningen.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **Jitbit supportavdelningen**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **Jitbit supportavdelningen**.

    ![Jitbit supportavdelningen-länken i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-jitbit-helpdesk-test-user"></a>Skapa test användare av Jitbit-supportavdelningen

För att kunna göra det möjligt för Azure AD-användare att logga in på Jitbit-supportavdelningen måste de tillhandahållas i Jitbit-supportavdelningen. När det gäller Jitbit-supportavdelningen är etableringen en manuell uppgift.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på din **Jitbit-Helpdesk** -klient.

1. Klicka på **Administration**i menyn högst upp.

    ![Administration](./media/jitbit-helpdesk-tutorial/ic777681.png "Administration")

1. Klicka på **användare, företag och behörigheter**.

    ![Användare, företag och behörigheter](./media/jitbit-helpdesk-tutorial/ic777682.png "Användare, företag och behörigheter")

1. Klicka på **Lägg till användare**.

    ![Lägg till användare](./media/jitbit-helpdesk-tutorial/ic777685.png "Lägg till användare")

1. I avsnittet Skapa anger du data för det Azure AD-konto som du vill etablera enligt följande:

    ![Skapa](./media/jitbit-helpdesk-tutorial/ic777686.png "Skapa")

   a. I text rutan **användar namn** anger du användar namnet för användaren som **BrittaSimon**.

   b. Skriv e-post för användaren i text rutan **e-post** **BrittaSimon@contoso.com** .

   c. I text rutan **förnamn** skriver du förnamn för användaren som **Britta**.

   d. I text rutan **efter namn** skriver du efter namnet på användaren som **Simon**.

   e. Klicka på **Skapa**.

> [!NOTE]
> Du kan använda andra verktyg för att skapa användar konton eller API: er för Jitbit-Helpdesk som tillhandahålls av Jitbit-supportavdelningen för att etablera Azure AD-användarkonton.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Jitbit supportavdelningen på åtkomst panelen, bör du loggas in automatiskt till Jitbit-supportavdelningen som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
