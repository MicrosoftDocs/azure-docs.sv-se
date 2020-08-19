---
title: 'Självstudie: Azure Active Directory integrering med SAML SSO för Confluence med hjälp av resolution GmbH | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SAML SSO for Confluence by resolution GmbH.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/24/2018
ms.author: jeedes
ms.openlocfilehash: 87c24cf61974c284772aae23e48ffc907792895b
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88543490"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-confluence-by-resolution-gmbh"></a>Självstudie: Azure Active Directory integrering med SAML SSO för Confluence med hjälp av resolution GmbH

I den här självstudien lär du dig hur du integrerar SAML SSO for Confluence by resolution GmbH med Azure Active Directory (Azure AD).
Integreringen av SAML SSO for Confluence by resolution GmbH med Azure AD medför följande fördelar:

* Du kan styra vem som har åtkomst till SAML SSO for Confluence by resolution GmbH från Azure AD.
* Du kan konfigurera inställningar så att dina användare loggas in automatiskt till Confluence by resolution GmbH (enkel inloggning) med deras Azure Active Directory-konton (Azure AD).
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med SAML SSO for Confluence by resolution GmbH behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* En SAML SSO for Confluence by resolution GmbH-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* SAML SSO for Confluence by resolution GmbH stöder **SP**- och **IDP**-initierad enkel inloggning

## <a name="adding-saml-sso-for-confluence-by-resolution-gmbh-from-the-gallery"></a>Lägga till SAML SSO for Confluence by resolution GmbH från galleriet

För att konfigurera integreringen av SAML SSO for Confluence by resolution GmbH med Azure AD måste du lägga till SAML SSO for Confluence by resolution GmbH från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till SAML SSO for Confluence by resolution GmbH från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **SAML SSO for Confluence by resolution GmbH** i sökrutan, välj **SAML SSO for Confluence by resolution GmbH** från resultatpanelen och klicka på **Lägg till** för att lägga till programmet.

     ![SAML SSO for Confluence by resolution GmbH i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa enkel inloggning i Azure AD med SAML SSO for Confluence by resolution GmbH baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i SAML SSO for Confluence by resolution GmbH upprättas.

För att konfigurera och testa enkel inloggning i Azure AD med SAML SSO for Confluence by resolution GmbH måste du utföra följande uppgifter:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för SAML SSO for Confluence by resolution GmbH](#configure-saml-sso-for-confluence-by-resolution-gmbh-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa SAML SSO for Confluence by resolution GmbH-testanvändare](#create-saml-sso-for-confluence-by-resolution-gmbh-test-user)** – för att skapa en motsvarighet till Britta Simon i SAML SSO for Confluence by resolution GmbH som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning i Azure AD med SAML SSO for Confluence by resolution GmbH:

1. Välj [Enkel inloggning](https://portal.azure.com/) på sidan för programintegrering med **SAML SSO for Confluence by resolution GmbH** på **Azure-portalen**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **grundläggande SAML-konfiguration** utför du följande steg, om du vill konfigurera programmet i **IDP** initierat läge:

    ![Information om enkel inloggning med SAML SSO for Confluence by resolution GmbH-domäner och -URL:er](common/idp-intiated.png)

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://<server-base-url>/plugins/servlet/samlsso`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<server-base-url>/plugins/servlet/samlsso`

    c. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i SP-initierat läge:

    ![Information om enkel inloggning med SAML SSO for Confluence by resolution GmbH-domäner och -URL:er](common/metadata-upload-additional-signon.png)

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [kundsupporten för SAML SSO for Confluence by resolution GmbH](https://www.resolution.de/go/support) och be om dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

4. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

### <a name="configure-saml-sso-for-confluence-by-resolution-gmbh-single-sign-on"></a>Konfigurera enkel inloggning för SAML SSO for Confluence by resolution GmbH

1. Öppna ett nytt webbläsarfönster och logga in på **administrationsportalen för SAML SSO for Confluence by resolution GmbH** som administratör.

2. Hovra över kugghjulet och klicka på **Tillägg**.
    
    ![Konfigurera enkel inloggning](./media/samlssoconfluence-tutorial/addon1.png)

3. Du omdirigeras till sidan för administratörsåtkomst. Ange lösenordet och klicka på **Bekräfta**.

    ![Konfigurera enkel inloggning](./media/samlssoconfluence-tutorial/addon2.png)

4. Klicka på **Hitta nya tillägg** under fliken **ATLASSIAN MARKETPLACE**. 

    ![Konfigurera enkel inloggning](./media/samlssoconfluence-tutorial/addon.png)

5. Sök efter **SAML Single Sign On (SSO) for Confluence** och klicka på **Installera** för att installera det nya SAML-plugin-programmet.

    ![Konfigurera enkel inloggning](./media/samlssoconfluence-tutorial/addon7.png)

6. Installationen av plugin-programmet startar. Klicka på **Stäng**.

    ![Konfigurera enkel inloggning](./media/samlssoconfluence-tutorial/addon8.png)

    ![Konfigurera enkel inloggning](./media/samlssoconfluence-tutorial/addon9.png)

7.  Klicka på **Hantera**.

    ![Konfigurera enkel inloggning](./media/samlssoconfluence-tutorial/addon10.png)
    
8. Klicka på **Konfigurera** för att konfigurera det nya plugin-programmet.

    ![Konfigurera enkel inloggning](./media/samlssoconfluence-tutorial/addon11.png)

9. Det nya plugin-programmet visas även under fliken **Användare och säkerhet**.

    ![Konfigurera enkel inloggning](./media/samlssoconfluence-tutorial/addon3.png)
    
10. Klicka på **Lägg till ny IdP** på **konfigurationssidan för plugin-programmet SAML SingleSignOn** för att konfigurera inställningarna för identitetsprovidern.

    ![Konfigurera enkel inloggning](./media/samlssoconfluence-tutorial/addon4.png)

11. Utför följande steg på sidan **Välj din SAML-identitetsprovider**:

    ![Konfigurera enkel inloggning](./media/samlssoconfluence-tutorial/addon5a.png)
 
    a. Ange **Azure AD** som IdP-typ.
    
    b. Lägg till ett **namn** för identitetsprovidern (t.ex. Azure AD).
    
    c. Lägg till en **beskrivning** av identitetsprovidern (t.ex. Azure AD).
    
    d. Klicka på **Nästa**.
    
12. Klicka på knappen **Nästa** på **konfigurationssidan för identitetsprovidern**.

    ![Konfigurera enkel inloggning](./media/samlssoconfluence-tutorial/addon5b.png)

13. Utför följande steg på sidan **Importera SAML IdP-metadata**:

    ![Konfigurera enkel inloggning](./media/samlssoconfluence-tutorial/addon5c.png)

    a. Klicka på **Läs in fil** och välj XML-filen med metadata som du hämtade i steg 5.

    b. Klicka på **Importera**.
    
    c. Vänta en kort stund tills importen slutförts.
    
    d. Klicka på **Nästa**.
    
14. Klicka på **Nästa** på sidan för **attribut för användar-ID och transformering**.

    ![Konfigurera enkel inloggning](./media/samlssoconfluence-tutorial/addon5d.png)
    
15. Spara inställningarna genom att klicka på **Spara och Nästa** på sidan för **användargenerering och uppdatering**.   
    
    ![Konfigurera enkel inloggning](./media/samlssoconfluence-tutorial/addon6a.png)
    
16. Hoppa över användartestningen så länge genom att klicka på **Hoppa över test och konfigurera manuellt** på sidan **Testa dina inställningar**. Du ska köra testningen i nästa avsnitt, vilket kräver ytterligare några inställningar på Azure-portalen. 
    
    ![Konfigurera enkel inloggning](./media/samlssoconfluence-tutorial/addon6b.png)
    
17. I dialog rutan visas, **hoppar du över testet...**, klickar du på **OK**.
    
    ![Konfigurera enkel inloggning](./media/samlssoconfluence-tutorial/addon6c.png)

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

I det här avsnittet ska du göra det möjligt för Britta Simon att använda enkel inloggning i Azure genom att ge åtkomst till SAML SSO for Confluence by resolution GmbH.

1. Välj **Företagsprogram** på Azure-portalen, välj **Alla program** och välj sedan **SAML SSO for Confluence by resolution GmbH**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Skriv och välj **SAML SSO for Confluence by resolution GmbH** i programlistan.

    ![SAML SSO for Confluence by resolution GmbH-länken i listan över program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-saml-sso-for-confluence-by-resolution-gmbh-test-user"></a>Skapa SAML SSO for Confluence by resolution GmbH-testanvändare

För att Azure AD-användare ska kunna logga in i SAML SSO for Confluence by resolution GmbH måste de vara etablerade i SAML SSO for Confluence by resolution GmbH.  
I SAML SSO for Confluence by resolution GmbH är etableringen en manuell åtgärd.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på din SAML SSO for Confluence by resolution GmbH-företagswebbplats som administratör.

2. Hovra över kugghjulet och klicka på **Användarhantering**.

    ![Lägga till medarbetare](./media/samlssoconfluence-tutorial/user1.png) 

3. Under avsnittet användare klickar du på fliken **Lägg till användare** . Utför följande steg på dialog sidan **Lägg till en användare** :

    ![Lägga till medarbetare](./media/samlssoconfluence-tutorial/user2.png) 

    a. I textrutan **Användarnamn** skriver du e-postadressen för användaren: Britta Simon.

    b. I textrutan **Fullständigt namn** skriver du det fullständiga namnet för användaren: Britta Simon.

    c. I textrutan **E-post** skriver du e-postadressen för användaren: Brittasimon@contoso.com.

    d. I textrutan **Lösenord** skriver du lösenordet för Britta Simon.

    e. Klicka på **Bekräfta lösenord** för att ange lösenordet igen.
    
    f. Klicka på knappen **Lägg till**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen för SAML SSO for Confluence by resolution GmbH på åtkomstpanelen bör du loggas in automatiskt i SAML SSO for Confluence by resolution GmbH-programmet som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

