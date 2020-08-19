---
title: 'Självstudie: Azure Active Directory integrering med FreshDesk | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och FreshDesk.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/16/2020
ms.author: jeedes
ms.openlocfilehash: 71c1bea58f17b457f417a5b050640d04d44019d5
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88551072"
---
# <a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>Självstudie: Azure Active Directory integrering med FreshDesk

I den här självstudien lär du dig att integrera FreshDesk med Azure Active Directory (AD Azure).
Genom att integrera FreshDesk med Azure AD får du följande fördelar:

* Du kan i Azure AD styra vem som har åtkomst till FreshDesk.
* Du kan göra så att dina användare automatiskt loggas in på FreshDesk (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med FreshDesk behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* FreshDesk-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* FreshDesk stöder **IDP**-initierad enkel inloggning
* När du har konfigurerat FreshDesk kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-freshdesk-from-the-gallery"></a>Lägga till FreshDesk från galleriet

För att konfigurera integreringen av FreshDesk till Azure AD behöver du lägga till FreshDesk från galleriet till listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **FreshDesk** i sökrutan.
1. Välj **FreshDesk** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-freshdesk"></a>Konfigurera och testa enkel inloggning med Azure AD för FreshDesk

Konfigurera och testa Azure AD SSO med FreshDesk med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i FreshDesk.

Om du vill konfigurera och testa Azure AD SSO med FreshDesk, slutför du följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
1. **[Konfigurera enkel inloggning för FreshDesk](#configure-freshdesk-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
    1. **[Skapa FreshDesk-testanvändare](#create-freshdesk-test-user)** – för att ha en motsvarighet för Britta Simon i FreshDesk som är länkad till en Azure AD-representation av användaren.
1. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

## <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **FreshDesk** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    a. I text rutan **inloggnings-URL** skriver du en URL med följande mönster: `https://<tenant-name>.freshdesk.com` eller något annat värde FreshDesk har föreslagit.

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<tenant-name>.freshdesk.com` eller något annat värde så Freshdesk har föreslagit.

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Hämta dessa värden genom att kontakta [supportteamet för FreshDesk-klienten](https://freshdesk.com/helpdesk-software?utm_source=Google-AdWords&utm_medium=Search-IND-Brand&utm_campaign=Search-IND-Brand&utm_term=freshdesk&device=c&gclid=COSH2_LH7NICFVUDvAodBPgBZg). Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. FreshDesk-programmet förväntar sig SAML-intyg i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. Följande skärm bild visar en lista över standardattribut, medan **unika användar identifierare** mappas med **User. UserPrincipalName** , men FreshDesk förväntar sig att detta anspråk ska mappas med **User. mail**, så du måste redigera mappningen av attributet genom att klicka på ikonen Redigera och ändra attributet mappning.

    ![image](common/edit-attribute.png)

1. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**. Klicka på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **set-up-FreshDesk** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **namn** anger du **BrittaSimon**.
  
    b. I fältet **användar namn** skriver du **brittasimon \@ yourcompanydomain. extension**  
    Till exempel BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.


### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till FreshDesk.

1. På Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **FreshDesk**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan skriver du in och väljer **FreshDesk**.

    ![FreshDesk-länken i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-freshdesk-single-sign-on"></a>Konfigurera enkel inloggning för FreshDesk

1. I ett annat webbläsarfönster loggar du in på din Freshdesk-företagsplats som administratör.

2. Välj **säkerhets ikonen** och i avsnittet **säkerhet** , utför följande steg:

    ![Enkel inloggning](./media/freshdesk-tutorial/configure-1.png "Enkel inloggning")
  
    a. För **enkel inloggning**väljer du **på**.

    b. I **inloggnings metoden**väljer du **SAML SSO**.

    c. I det **entitets-ID som tillhandahålls av** text rutan IDP klistrar du in **entitets-ID** -värdet, som du har kopierat från Azure Portal.

    d. I text rutan för **SAML SSO URL** klistrar du in **inloggnings-URL** -värde, som du har kopierat från Azure Portal.

    e. I **signerings alternativen**väljer du **enbart signerade intyg** från List rutan.

    f. I textrutan **Utloggnings-URL** klistrar du värdet för den **utloggnings-URL** som du har kopierat från Azure-portalen.

    ex. I text rutan **säkerhetscertifikat** , klistra in **certifikat (base64)** som du har hämtat tidigare.
  
    h. Klicka på **Spara**.

## <a name="create-freshdesk-test-user"></a>Skapa Freshdesk-testanvändare

För att göra det möjligt för Azure AD-användare att logga in på FreshDesk måste de etableras i FreshDesk.  
När det gäller FreshDesk är etablering en manuell aktivitet.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på **Freshdesk**-klientorganisationen.

1. I menyn till vänster klickar du på **admin** och på fliken **allmänna inställningar** klickar du på **agenter**.
  
    ![Agenter](./media/freshdesk-tutorial/create-user-1.png "Agenter")

1. Klicka på **Ny Agent**.

    ![Ny agent](./media/freshdesk-tutorial/create-user-2.png "Ny agent")

1. I dialog rutan agent information anger du de obligatoriska fälten och klickar på **Skapa Agent**.

    ![Agent information](./media/freshdesk-tutorial/create-user-3.png "Agent information")

    >[!NOTE]
    >Azure AD-kontoinnehavaren får ett e-postmeddelande som innehåller en länk för att bekräfta kontot innan det aktiveras.
    >
    >[!NOTE]
    >Du kan använda andra verktyg för FreshDesk av användar konton eller API: er som tillhandahålls av FreshDesk för att etablera Azure AD-användarkonton till FreshDesk.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen FreshDesk på åtkomst panelen, bör du loggas in automatiskt på den FreshDesk som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

