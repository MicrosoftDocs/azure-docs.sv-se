---
title: 'Självstudie: Azure Active Directory integration med Infogix Data3Sixty-regler | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Infogix Data3Sixty-regler.
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
ms.openlocfilehash: c46fe09691c270338e7ce60f8f0753e8c9b84948
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88553051"
---
# <a name="tutorial-azure-active-directory-integration-with-infogix-data3sixty-govern"></a>Självstudie: Azure Active Directory integrering med Infogix Data3Sixty-regler

I den här självstudien får du lära dig att integrera Infogix Data3Sixty-regler med Azure Active Directory (Azure AD).
Att integrera Infogix Data3Sixty-regler med Azure AD ger följande fördelar:

* Du kan styra Azure AD som har åtkomst till Infogix Data3Sixty-regler.
* Du kan göra det möjligt för användarna att logga in automatiskt till Infogix Data3Sixty-regler (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Infogix Data3Sixty-regler behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Infogix Data3Sixty styr enkel inloggning aktive rad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Infogix Data3Sixty-regler stöder **SP-och IDP** -INITIERAd SSO
* Infogix Data3Sixty-regler stöder **just-in-Time** User-etablering

## <a name="adding-infogix-data3sixty-govern-from-the-gallery"></a>Lägga till Infogix Data3Sixty-regler från galleriet

Om du vill konfigurera integrationen av Infogix-Data3Sixty i Azure AD måste du lägga till Infogix Data3Sixty styr från galleriet till listan över hanterade SaaS-appar.

**Gör så här om du vill lägga till Infogix Data3Sixty-regler från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Infogix Data3Sixty-regler**, väljer **Infogix Data3Sixty styr** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

     ![Infogix Data3Sixty-regler i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning i Azure AD med Infogix Data3Sixty-regler baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och en relaterad användare i Infogix Data3Sixty-regler.

Om du vill konfigurera och testa enkel inloggning i Azure AD med Infogix Data3Sixty-regler måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Infogix Data3Sixty styr enkel inloggning](#configure-infogix-data3sixty-govern-single-sign-on)** – konfigurera inställningar för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Infogix Data3Sixty styr test-User](#create-infogix-data3sixty-govern-test-user)** -to att ha en motsvarighet till Britta Simon i Infogix Data3Sixty-regler som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning i Azure AD med Infogix Data3Sixty-regler:

1. Välj **enkel inloggning**på sidan **Infogix Data3Sixty** på program integration i [Azure Portal](https://portal.azure.com/).

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, utför följande steg:

    ![Infogix Data3Sixty styr domän-och URL-information för enkel inloggning](common/idp-intiated.png)

    a. Skriv en URL i text rutan **identifierare** : `https://data3sixty.com/ui`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<subdomain>.data3sixty.com/sso/acs`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![Infogix Data3Sixty styr domän-och URL-information för enkel inloggning](common/metadata-upload-additional-signon.png)

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<subdomain>.data3sixty.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med faktisk svars-URL och inloggnings-URL. Kontakta [Infogix Data3Sixty för att styra klient support teamet](mailto:data3sixtysupport@infogix.com) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. Infogix Data3Sixty-regler för program förväntar sig SAML-intyg i ett särskilt format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut i avsnittet **Användarattribut** på sidan för programintegrering. På sidan **Konfigurera enkel inloggning med SAML** klickar du på knappen **Redigera** för att öppna dialogrutan **Användarattribut**.

    ![image](common/edit-attribute.png)

7. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** så redigerar du anspråken genom att använda **Redigera-ikonen** eller lägga till anspråken genom att använda **Lägg till nytt anspråk** för att konfigurera SAML-tokenattribut som det visas i bilden ovan och utföra följande steg:

    | Name | Källattribut|
    | -----------| -------------- |
    | förnamn  | user.givenname |
    | efternamn   | user.surname |
    | användarnamn   | user.mail    |

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Lämna **Namnrymd** tom.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Klicka på **OK**

    ex. Klicka på **Spara**.

8. På sidan **Konfigurera enkel inloggning med SAML**, i avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (RAW)** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/certificateraw.png)

9. I avsnittet **Konfigurera Infogix Data3Sixty-regler** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-infogix-data3sixty-govern-single-sign-on"></a>Konfigurera Infogix Data3Sixty styr enkel inloggning

Om du vill konfigurera enkel inloggning på **Infogix Data3Sixty-styrnings** sidan måste du skicka det nedladdade **certifikatet (RAW)** och lämpliga kopierade url: er från Azure Portal till [Infogix Data3Sixtys regler för support teamet](mailto:data3sixtysupport@infogix.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

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

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Infogix Data3Sixty-regler.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **Infogix Data3Sixty-regler**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **Infogix Data3Sixty-regler**.

    ![Länken Infogix Data3Sixty-styrning i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-infogix-data3sixty-govern-test-user"></a>Skapa Infogix Data3Sixty-styr test användare

I det här avsnittet skapas en användare som kallas Britta Simon i Infogix Data3Sixty-regler. Infogix Data3Sixty-regler stöder just-in-Time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i Infogix Data3Sixty-regler, skapas en ny efter autentiseringen.

> [!Note]
> Om du behöver skapa en användare manuellt kan du kontakta [Infogix Data3Sixty för att styra support teamet](mailto:data3sixtysupport@infogix.com).

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Infogix Data3Sixty-regler på åtkomst panelen, bör du loggas in automatiskt till Infogix Data3Sixty-regler för vilka du ställer in SSO. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

