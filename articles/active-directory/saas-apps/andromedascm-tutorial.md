---
title: 'Självstudie: Azure Active Directory integrering med Andromeda | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Andromeda.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.openlocfilehash: 31fc1877dd0edd2fd670f0f9466692ac7bc8ad15
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88531199"
---
# <a name="tutorial-azure-active-directory-integration-with-andromeda"></a>Självstudie: Azure Active Directory integrering med Andromeda

I den här självstudien får du lära dig hur du integrerar Andromeda med Azure Active Directory (Azure AD).
Genom att integrera Andromeda med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Andromeda.
* Du kan göra det möjligt för användarna att logga in automatiskt till Andromeda (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Andromeda behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/)
* Andromeda-aktiverad prenumeration med enkel inloggning

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Andromeda stöder **SP-och IDP** -INITIERAd SSO
* Andromeda stöder **just-in-Time** User-etablering

## <a name="adding-andromeda-from-the-gallery"></a>Lägga till Andromeda från galleriet

Om du vill konfigurera integreringen av Andromeda i Azure AD måste du lägga till Andromeda från galleriet i listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till Andromeda från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök skriver du **Andromeda**, väljer **Andromeda** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

    ![Andromeda i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med Andromeda baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i Andromeda upprättas.

Om du vill konfigurera och testa enkel inloggning med Andromeda i Azure AD måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Andromeda-enkel inloggning](#configure-andromeda-single-sign-on)** för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Andromeda test User](#create-andromeda-test-user)** – om du vill ha en motsvarighet till Britta Simon i Andromeda som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med Andromeda i Azure AD:

1. Välj **enkel inloggning**på sidan **Andromeda** Application Integration i [Azure Portal](https://portal.azure.com/).

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, utför följande steg:

    ![Information om enkel inloggning för Andromeda-domän och URL: er](common/idp-intiated.png)

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://<tenantURL>.ngcxpress.com/`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<tenantURL>.ngcxpress.com/SAMLConsumer.aspx`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![Information om enkel inloggning för Andromeda-domän och URL: er](common/metadata-upload-additional-signon.png)

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<tenantURL>.ngcxpress.com/SAMLLogon.aspx`

    > [!NOTE]
    > Dessa värden är inte verkliga. Du uppdaterar värdet med den faktiska identifieraren, svars-URL: en och inloggnings-URL: en som beskrivs senare i självstudien.

6. Andromeda-programmet förväntar sig SAML-intyg i ett särskilt format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut i avsnittet **Användarattribut** på sidan för programintegrering. På sidan **Konfigurera enkel inloggning med SAML** klickar du på knappen **Redigera** för att öppna dialogrutan **Användarattribut**.

    ![image](common/edit-attribute.png)

    > [!Important]
    > Ta bort namn områdes definitionerna när du ställer in dessa.

7. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** så redigerar du anspråken genom att använda **Redigera-ikonen** eller lägga till anspråken genom att använda **Lägg till nytt anspråk** för att konfigurera SAML-tokenattribut som det visas i bilden ovan och utföra följande steg: 

    | Name | Källattribut|
    | ------ | -----------|
    | roll        | App-speciell roll |
    | typ        | Apptyp |
    | company       | CompanyName |

    > [!NOTE]
    > Det finns inga verkliga värden. Dessa värden är bara för demo syfte, Använd dina organisations roller.

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Lämna **Namnrymd** tom.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Klicka på **OK**

    ex. Klicka på **Spara**.

8. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

9. I avsnittet **Konfigurera Andromeda** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-andromeda-single-sign-on"></a>Konfigurera Andromeda enkel inloggning

1. Logga in på din Andromeda företags webbplats som administratör.

2. Klicka på **administratör** överst på Meny raden och navigera till **Administration**.

    ![Andromeda-administratör](./media/andromedascm-tutorial/tutorial_andromedascm_admin.png)

3. Klicka på **SAML-konfiguration**på vänster sida av verktygsfältet under **gränssnitt** .

    ![Andromeda SAML](./media/andromedascm-tutorial/tutorial_andromedascm_saml.png)

4. Utför följande steg på sidan **SAML-konfigurations** avsnitt:

    ![Andromeda-konfiguration](./media/andromedascm-tutorial/tutorial_andromedascm_config.png)

    a. Kontrol lera **Aktivera SSO med SAML**.

    b. Under avsnittet **Andromeda information** kopierar du värdet för **SP-identitet** och klistrar in det i text rutan **identifierare** i avsnittet **grundläggande SAML-konfiguration** .

    c. Kopiera **konsumentens URL** -värde och klistra in det i text rutan **svars-URL** i avsnittet **grundläggande SAML-konfiguration** .

    d. Kopiera värdet för **inloggnings-URL** och klistra in det i text rutan **inloggnings-URL** i avsnittet **grundläggande SAML-konfiguration** .

    e. Skriv ditt IDP namn under avsnittet **SAML Identity Provider** .

    f. I text rutan för **enkel inloggning i slut punkt** klistrar du in värdet för **inloggnings-URL: en** som du har kopierat från Azure Portal.

    ex. Öppna det nedladdade **base64-kodade certifikatet** från Azure Portal i anteckningar, klistra in det i text rutan **X 509-certifikat** .
    
    h. Mappa följande attribut med respektive värde för att under lätta SSO-inloggning från Azure AD. Attributet **User ID** krävs för inloggning. För etablering, krävs **e-post**, **företag**, **UserType**och **roll** . I det här avsnittet definierar vi attribut mappning (namn och värden) som motsvarar de som definierats i Azure Portal

    ![Andromeda attbmap](./media/andromedascm-tutorial/tutorial_andromedascm_attbmap.png)

    i. Klicka på **Spara**.

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

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Andromeda.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **Andromeda**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **Andromeda**.

    ![Andromeda-länken i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-andromeda-test-user"></a>Skapa Andromeda test användare

I det här avsnittet skapas en användare som kallas Britta Simon i Andromeda. Andromeda stöder just-in-Time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i Andromeda skapas en ny efter autentiseringen. Om du behöver skapa en användare manuellt kontaktar du [Andromeda client support team](https://www.ngcsoftware.com/support/).

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Andromeda på åtkomst panelen, bör du loggas in automatiskt på den Andromeda som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)