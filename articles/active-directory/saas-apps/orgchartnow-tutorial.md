---
title: 'Självstudie: Azure Active Directory integration med organisations schema nu | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och organisations schema nu.
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
ms.openlocfilehash: a28bfbb7fcfd357dc3aac521db24788607cd2212
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88543877"
---
# <a name="tutorial-azure-active-directory-integration-with-orgchart-now"></a>Självstudie: Azure Active Directory integrering med organisations schema nu

I den här självstudien får du lära dig att integrera organisations scheman nu med Azure Active Directory (Azure AD).
Att integrera organisations schema nu med Azure AD ger följande fördelar:

* Du kan kontrol lera Azure AD som har åtkomst till organisations schema nu.
* Du kan göra det möjligt för användarna att vara automatiskt inloggade i organisations scheman nu (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med organisations schema nu behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Schema för enkel inloggning som är aktiverat för organisationsschemats

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Organisations schema stöder nu **SP** -och **IDP** -initierad SSO

## <a name="adding-orgchart-now-from-the-gallery"></a>Lägga till org. nu från galleriet

Du måste lägga till ett organisations schema nu från galleriet till din lista över hanterade SaaS-appar för att kunna konfigurera organisationens integrering i Azure AD.

**Gör så här om du vill lägga till ett organisations schema nu från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök skriver du **org nu**och väljer sedan **organisations schema nu** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

     ![Organisations schema nu i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning i Azure AD med organisations schema nu baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i organisations organisationen nu upprättas.

Om du vill konfigurera och testa Azure AD-enkel inloggning med organisations schema nu måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera organisations schema nu enkel inloggning](#configure-orgchart-now-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa organisations schema nu testa användare](#create-orgchart-now-test-user)** – om du vill ha en motsvarighet till Britta Simon i organisations schema nu som är länkat till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning för Azure AD med organisations schema:

1. I [Azure Portal](https://portal.azure.com/)väljer du **enkel inloggning**på sidan för program integrering på **org** .

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. Om du vill konfigurera appen i **IDP**-initierat läge gör du följande i avsnittet **Grundläggande SAML-konfiguration**:

    ![Information om enkel inloggning för organisations-och URL-adresser](common/idp-identifier.png)

    I textrutan **Identifierare** skriver du en URL: `https://sso2.orgchartnow.com`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![image](common/both-preintegrated-signon.png)

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://sso2.orgchartnow.com/Shibboleth.sso/Login?entityID=<YourEntityID>&target=https://sso2.orgchartnow.com`

    > [!NOTE]
    > `<YourEntityID>` är **Azure AD-identifieraren** kopierad från avsnittet **Konfigurera organisations schema nu** , som beskrivs senare i självstudierna.

6. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

7. I avsnittet **Konfigurera organisations schema nu** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-orgchart-now-single-sign-on"></a>Konfigurera för tillfället enkel inloggning för organisations scheman

Om du vill konfigurera enkel inloggning på **organisations schema nu** , måste du skicka den hämtade **XML-koden för federationsmetadata** och lämpliga kopierade url: er från Azure Portal till [organisations schema som nu stöder team](mailto:ocnsupport@officeworksoftware.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

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

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till organisations schema nu.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **konverterare nu**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **konverterare nu**.

    ![Länkat organisationsschemats länk visas nu i listan program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-orgchart-now-test-user"></a>Skapa organisations schema nu testa användare

Om du vill att Azure AD-användare ska kunna logga in på organisations schema nu måste de tillhandahållas i organisations schema nu. 

1. Organisations schema stöder nu just-in-Time-etablering, som är aktiverat som standard. En ny användare skapas vid ett försök att få åtkomst till organisations schema nu om det inte redan finns. Just-in-Time-funktionen för användar etablering kommer bara att skapa en **skrivskyddad** användare när en SSO-begäran kommer från en känd IDP och e-postmeddelandet i SAML-kontrollen inte hittas i användar listan. För den här funktionen för automatisk etablering måste du skapa en åtkomst grupp med rubriken **Allmänt** i organisations schema nu. Följ stegen nedan för att skapa en åtkomst grupp:

    a. Gå till alternativet **hantera grupper** när du har klickat på **kugg hjulet** i det övre högra hörnet i användar gränssnittet.

    ![Organisations schema nu grupper](./media/orgchartnow-tutorial/tutorial_orgchartnow_manage.png)    

    b. Välj ikonen **Lägg till** och namnge gruppen **Allmänt** och klicka sedan på **OK**. 

    ![Nu lägger du till konverterare](./media/orgchartnow-tutorial/tutorial_orgchartnow_add.png)

    c. Välj de mappar som du vill att allmänna eller skrivskyddade användare ska kunna komma åt:

    ![Mappar för org. nu](./media/orgchartnow-tutorial/tutorial_orgchartnow_chart.png)

    d. **Lås** mapparna så att endast administratörs användare kan ändra dem. Tryck sedan på **OK**.

    ![Organisationsschemats lås nu](./media/orgchartnow-tutorial/tutorial_orgchartnow_lock.png)

2. Om du vill skapa **Administratörs** användare och **läsa/skriva** användare måste du skapa en användare manuellt för att få åtkomst till deras behörighets nivå via SSO. Gör följande för att etablera ett användarkonto:

    a. Logga in på konverterare nu som en säkerhets administratör.

    b.  Klicka på **Inställningar** i det övre högra hörnet och navigera sedan till **Hantera användare**.

    ![Inställningar för org. nu](./media/orgchartnow-tutorial/tutorial_orgchartnow_settings.png)

    c. Klicka på **Lägg till** och utför följande steg:

    ![Organisations schema hanteras nu](./media/orgchartnow-tutorial/tutorial_orgchartnow_manageusers.png)

    * I text rutan **användar-ID** anger du användar-ID **som \@ brittasimon contoso.com**.

    * I text rutan **e-postadress** anger du e-postadressen för användaren som **brittasimon \@ contoso.com**.

    * Klicka på **Lägg till**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen organisations schema nu i åtkomst panelen, bör du loggas in automatiskt på det konverterare för det konverterare som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

