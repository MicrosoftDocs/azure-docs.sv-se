---
title: 'Självstudie: Azure Active Directory integrering med Kantega SSO för Confluence | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Kantega SSO för Confluence.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: be86e04359c29696d208994d85d36b7740b60cc3
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101646222"
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-confluence"></a>Självstudie: Azure Active Directory integrering med Kantega SSO för Confluence

I den här självstudien får du lära dig att integrera Kantega SSO för Confluence med Azure Active Directory (Azure AD).
Genom att integrera Kantega SSO för Confluence med Azure AD får du följande fördelar:

* Du kan styra Azure AD som har åtkomst till Kantega SSO för Confluence.
* Du kan göra det möjligt för användarna att logga in automatiskt till Kantega SSO för Confluence (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Kantega SSO för Confluence behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/)
* Kantega SSO för Confluence enkel inloggning aktive rad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Kantega SSO för Confluence stöder **SP-och IDP** -INITIERAd SSO

## <a name="adding-kantega-sso-for-confluence-from-the-gallery"></a>Lägga till Kantega SSO för Confluence från galleriet

Om du vill konfigurera integrationen av Kantega SSO för Confluence i Azure AD måste du lägga till Kantega SSO för Confluence från galleriet till listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till Kantega SSO för Confluence från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **KANTEGA SSO för Confluence**, väljer **Kantega SSO för Confluence** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

    ![Kantega SSO för Confluence i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med Kantega SSO för Confluence baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i Kantega SSO för Confluence upprättas.

Om du vill konfigurera och testa enkel inloggning i Azure AD med Kantega SSO för Confluence måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera KANTEGA SSO för Confluence enkel inloggning](#configure-kantega-sso-for-confluence-single-sign-on)** för att konfigurera de enskilda Sign-On inställningarna på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa KANTEGA SSO för Confluence test User](#create-kantega-sso-for-confluence-test-user)** – om du vill ha en motsvarighet till Britta Simon i Kantega SSO för Confluence som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning för Azure AD med Kantega SSO för Confluence:

1. I [Azure Portal](https://portal.azure.com/)på sidan **Kantega SSO för Confluence** program integration väljer du **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, utför följande steg:

    ![Skärm bild som visar avsnittet "Basic S A M L-konfiguration" med fälten "identifierare" och "svara U R L" markerade och knappen "Spara" markerad.](common/idp-intiated.png)

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![Information om enkel inloggning för Kantega SSO för Confluence-domän och URL: er](common/metadata-upload-additional-signon.png)

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Dessa värden tas emot under konfigurationen av Confluence-pluginprogrammet, som beskrivs senare i självstudien.

6. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

7. I avsnittet **Konfigurera KANTEGA SSO för Confluence** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-kantega-sso-for-confluence-single-sign-on"></a>Konfigurera Kantega SSO för Confluence Single Sign-On

1. Logga in på din **Confluence-administratörs Portal** som administratör i ett annat webbläsarfönster.

1. Hovra över kugghjulet och klicka på **Tillägg**.

    ![Skärm bild som visar meny ikonen "kugg hjuls" och "tillägg" valda.](./media/kantegassoforconfluence-tutorial/addon1.png)

1. Klicka på **Hitta nya tillägg** under fliken **ATLASSIAN MARKETPLACE**.

    ![Skärm bild som visar fliken "ATTLASSIAN MARKETPLACE" med alternativet "hitta nya tillägg" markerat.](./media/kantegassoforconfluence-tutorial/addon.png)

1. Sök i **KANTEGA SSO för Confluence SAML Kerberos** och klicka på knappen **Installera** för att installera det nya SAML-plugin-programmet.

    ![Skärm bild som visar sidan "hitta nya tillägg" med "Kantega S S O för Confluence S A M L Kerberos" i sökrutan och knappen "installera" markerad.](./media/kantegassoforconfluence-tutorial/addon2.png)

1. Plugin-installationen startar.

    ![Skärm bild som visar skärmen för att installera plugin-programmet.](./media/kantegassoforconfluence-tutorial/addon3.png)

1. När installationen är klar. Klicka på **Stäng**.

    ![Skärm bild som visar skärmen "installerad och klar att gå till" med åtgärden "Stäng" vald.](./media/kantegassoforconfluence-tutorial/addon33.png)

1. Klicka på **Hantera**.

    ![Skärm bild som visar plugin-programmet "Kantega enkel inloggning med Kerberos och S A M L" med knappen "hantera" vald.](./media/kantegassoforconfluence-tutorial/addon34.png)

1. Klicka på **Konfigurera** för att konfigurera det nya plugin-programmet.

    ![Skärm bild som visar sidan "Kantega enkel inloggning med Kerberos och S A M L" med knappen "Konfigurera" vald.](./media/kantegassoforconfluence-tutorial/addon35.png)

1. Det nya plugin-programmet visas även under fliken **Användare och säkerhet**.

    ![Skärm bild som visar fliken "användare & säkerhet" med åtgärden "Kantega enkel inloggning" vald.](./media/kantegassoforconfluence-tutorial/addon36.png)

1. I **SAML** -avsnittet. Välj **Azure Active Directory (Azure AD)** i list rutan **Lägg till identitets leverantör** .

    ![Skärm bild som visar avsnittet "S A M L" med "Lägg till identitets leverantör" och "Azure Active Directory (Azure AD)" valt.](./media/kantegassoforconfluence-tutorial/addon4.png)

1. Välj prenumerations nivå som **Basic**.

    ![Skärm bild som visar sidan "förbereda Azure AD" med "Basic" vald.](./media/kantegassoforconfluence-tutorial/addon5.png)

1. I avsnittet **app Properties** utför du följande steg:

    ![Skärm bild som visar avsnittet "app-egenskaper" med knappen "app I D U R L" och "Kopiera" markerad, och knappen "Nästa" är markerad.](./media/kantegassoforconfluence-tutorial/addon6.png)

    a. Kopiera **app-ID-URI** -värdet och Använd det som **identifierare, svars-URL och Sign-On URL** i avsnittet **grundläggande SAML-konfiguration** i Azure Portal.

    b. Klicka på **Nästa**.

1. I avsnittet **metadata-import** utför du följande steg: 

    ![Skärm bild som visar avsnittet "metadata-import" med "metadatafil" på den här datorn "markerat.](./media/kantegassoforconfluence-tutorial/addon7.png)

    a. Välj **metadatafil på den här datorn** och ladda upp metadatafilen, som du har laddat ned från Azure Portal.

    b. Klicka på **Nästa**.

1. I avsnittet **namn och SSO-plats** utför du följande steg:

    ![Skärm bild som visar "namn och S O-plats" med text rutan "namn på identitetsprovider" och "Nästa"-knappen markerad.](./media/kantegassoforconfluence-tutorial/addon8.png)

    a. Lägg till namnet på identitets leverantören i text rutan för **identitets leverantörs namn** (t. ex. Azure AD).

    b. Klicka på **Nästa**.

1. Verifiera signerings certifikatet och klicka på **Nästa**.

    ![Skärm bild som visar avsnittet "signaturverifiering" med knappen "Nästa" valt.](./media/kantegassoforconfluence-tutorial/addon9.png)

1. Utför följande steg i avsnittet **Confluence User Accounts** :

    ![Skärm bild som visar avsnittet "Confluence User Accounts" med alternativet "skapa användare i Confluence: s interna katalog" om det behövs "och knappen" Nästa "har marker ATS.](./media/kantegassoforconfluence-tutorial/addon10.png)

    a. Välj **skapa användare i Confluence interna katalog om det behövs** och ange rätt namn på gruppen för användare (kan vara flera). av grupper åtskilda med kommatecken).

    b. Klicka på **Nästa**.

1. Klicka på **Finish**.

    ![Skärm bild av sidan Sammanfattning med knappen Slutför markerad.](./media/kantegassoforconfluence-tutorial/addon11.png)

1. Utför följande steg på avsnittet **kända domäner för Azure AD** : 

    ![Skärm bild som visar sidan "kända domäner för Azure AD" där text rutan "kända domäner" är markerad och knappen "Spara" är markerad.](./media/kantegassoforconfluence-tutorial/addon12.png)

    a. Välj **kända domäner** i den vänstra panelen på sidan.

    b. Ange domän namn i text rutan för **kända domäner** .

    c. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** anger du `brittasimon@yourcompanydomain.extension`  
    Till exempel BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Kantega SSO för Confluence.

1. I Azure Portal väljer du **företags program**, väljer **alla program** och väljer sedan **Kantega SSO för Confluence**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **KANTEGA SSO för Confluence**.

    ![Länken Kantega SSO för Confluence i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-kantega-sso-for-confluence-test-user"></a>Skapa Kantega SSO för Confluence test User

Om du vill att Azure AD-användare ska kunna logga in på Confluence måste de tillhandahållas i Confluence. När det gäller Kantega SSO för Confluence är etableringen en manuell uppgift.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på din Kantega SSO för Confluence-företags webbplats som administratör.

1. Hovra över kugghjulet och klicka på **Användarhantering**.

    ![Skärm bild som visar "kugg hjuls"-ikonen och "användar hantering" vald.](./media/kantegassoforconfluence-tutorial/user1.png)

1. Under avsnittet användare klickar du på fliken **Lägg till användare** . Utför följande steg på dialog sidan **Lägg till en användare** :

    ![Lägga till medarbetare](./media/kantegassoforconfluence-tutorial/user2.png)

    a. Skriv e-postadressen för användaren i textrutan **Användarnamn** som Brittasimon@contoso.com.

    b. I textrutan **Fullständigt namn** skriver du det fullständiga namnet för användaren: Britta Simon.

    c. I textrutan **E-post** skriver du e-postadressen för användaren: Brittasimon@contoso.com.

    d. I text rutan **lösen ord** skriver du lösen ordet för användaren.

    e. Klicka på **Bekräfta lösenord** för att ange lösenordet igen.

    f. Klicka på knappen **Lägg till**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Kantega SSO för confluence på åtkomst panelen, bör du loggas in automatiskt på Kantega SSO för Confluence som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](./tutorial-list.md)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Vad är villkorlig åtkomst i Azure Active Directory?](../conditional-access/overview.md)