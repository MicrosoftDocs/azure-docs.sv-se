---
title: 'Självstudie: Azure Active Directory integration med Kantega SSO för FishEye/degeln | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Kantega SSO för FishEye/degeln.
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
ms.openlocfilehash: 06a4e8aa1ad74f47526f3a39931632953bfaaec2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92459194"
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-fisheyecrucible"></a>Självstudie: Azure Active Directory integrering med Kantega SSO för FishEye/degeln

I den här självstudien får du lära dig att integrera Kantega SSO för FishEye/degeln med Azure Active Directory (Azure AD).
Genom att integrera Kantega SSO för FishEye/filterdegeln med Azure AD får du följande fördelar:

* Du kan styra Azure AD som har åtkomst till Kantega SSO för FishEye/degeln.
* Du kan göra det möjligt för användarna att logga in automatiskt till Kantega SSO för FishEye/filterdegeln (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Kantega SSO för FishEye/degeln behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/)
* Kantega SSO för FishEye/filterdegeln enkel inloggning aktive rad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Kantega SSO för FishEye/degeln stöder **SP och IDP** -INITIERAd SSO

## <a name="adding-kantega-sso-for-fisheyecrucible-from-the-gallery"></a>Lägga till Kantega SSO för FishEye/degeln från galleriet

Om du vill konfigurera integrationen av Kantega SSO för FishEye/degeln i Azure AD måste du lägga till Kantega SSO för FishEye/degeln från galleriet till listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till Kantega SSO för FishEye/degeln från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **KANTEGA SSO för FishEye/degeln**, väljer **Kantega SSO för FishEye/degeln** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

    ![Kantega SSO för FishEye/degeln i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med Kantega SSO för FishEye/degeln baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i Kantega SSO för FishEye/degeln upprättas.

Om du vill konfigurera och testa enkel inloggning i Azure AD med Kantega SSO för FishEye/degeln måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera KANTEGA SSO för FishEye/filterdegel enkel inloggning-om](#configure-kantega-sso-for-fisheyecrucible-single-sign-on)** du vill konfigurera enskilda Sign-On inställningar på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa KANTEGA SSO för FishEye/filterdegel test User](#create-kantega-sso-for-fisheyecrucible-test-user)** – om du vill ha en motsvarighet till Britta Simon i Kantega SSO för FishEye/degeln som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med Kantega SSO för FishEye/degeln i Azure AD:

1. På sidan [Azure Portal](https://portal.azure.com/)på sidan **Kantega SSO för FishEye/degeln** väljer du **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, utför följande steg:

    ![Skärm bild som visar avsnittet "Basic S A M L-konfiguration" med text rutan "identifierare" och "svara U R L" och knappen "Spara" markerad.](common/idp-intiated.png)

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![Kantega SSO för FishEye/degelns information om enkel inloggning](common/metadata-upload-additional-signon.png)

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Dessa värden tas emot under konfigurationen av FishEye/filterdegeln-plugin-programmet som beskrivs senare i självstudien.

6. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

7. I avsnittet **Konfigurera KANTEGA SSO för FishEye/degeln** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-kantega-sso-for-fisheyecrucible-single-sign-on"></a>Konfigurera Kantega SSO för FishEye/degeln Single Sign-On

1. I ett annat webbläsarfönster loggar du in på den lokala FishEye/filterdegeln-servern som administratör.

1. Hovra över kugghjulet och klicka på **Tillägg**.

    ![Skärm bild som visar kugg hjuls-ikonen och valda tillägg.](./media/kantegassoforfisheyecrucible-tutorial/addon1.png)

1. Under system inställningar klickar du på **hitta nya tillägg**. 

    ![Skärm bild som visar avsnittet "system inställningar" med alternativet "hitta nya tillägg" markerat.](./media/kantegassoforfisheyecrucible-tutorial/add-on2.png)

1. Sök **KANTEGA SSO for degeln** och klicka på knappen **Installera** för att installera det nya SAML-plugin-programmet.

    ![Skärm bild som visar sidan "Attlasian Marketplace for FishEye" med "Kantega S S O for degeln" i sökrutan och knappen "installera" markerad.](./media/kantegassoforfisheyecrucible-tutorial/addon2.png)

1. Plugin-installationen startar. 

    ![Skärm bild som visar dialog rutan "installera" för plugin-programmet.](./media/kantegassoforfisheyecrucible-tutorial/addon33.png)

1. När installationen är klar. Klicka på **Stäng**.

    ![Skärm bild som visar dialog rutan "installerad och klar att gå till" och knappen "Stäng" markerad.](./media/kantegassoforfisheyecrucible-tutorial/addon34.png)

1.  Klicka på **Hantera**.

    ![Skärm bild som visar "Kantega S S O för degeln S A M L & Kerberos" och knappen "hantera" markerad.](./media/kantegassoforfisheyecrucible-tutorial/addon35.png)

1. Klicka på **Konfigurera** för att konfigurera det nya plugin-programmet. 

    ![Skärm bild som visar sidan "användare-installerade tillägg" och knappen "Konfigurera" vald.](./media/kantegassoforfisheyecrucible-tutorial/addon3.png)

1. I **SAML** -avsnittet. Välj **Azure Active Directory (Azure AD)** i list rutan **Lägg till identitets leverantör** .

    ![Skärm bild som visar sidan "tillägg-Kantega-enkel inloggning" med list rutan "Lägg till identitetsprovider" och "Azure Active Directory (Azure AD)" vald. ](./media/kantegassoforfisheyecrucible-tutorial/addon4.png)

1. Välj prenumerations nivå som **Basic**.

    ![Skärm bild som visar avsnittet "förbereda Azure A D" med Basic-valt.](./media/kantegassoforfisheyecrucible-tutorial/addon5.png)

1. I avsnittet **app Properties** utför du följande steg:

    ![Skärm bild som visar avsnittet "app Properties" med text rutan "app I D U R I" och kopierings knappen vald.](./media/kantegassoforfisheyecrucible-tutorial/addon6.png)

    a. Kopiera **app-ID-URI** -värdet och Använd det som **identifierare, svars-URL och Sign-On URL** i avsnittet **grundläggande SAML-konfiguration** i Azure Portal.

    b. Klicka på **Nästa**.

1. I avsnittet **metadata-import** utför du följande steg:

    ![Skärm bild som visar avsnittet "metadata-import" med "metadatafil" på den här datorn "markerat.](./media/kantegassoforfisheyecrucible-tutorial/addon7.png)

    a. Välj **metadatafil på den här datorn** och ladda upp metadatafilen, som du har laddat ned från Azure Portal.

    b. Klicka på **Nästa**.

1. I avsnittet **namn och SSO-plats** utför du följande steg:

    ![Skärm bild som visar "namn och S O-plats" med text rutan "namn på identitetsprovider" och "Nästa"-knappen markerad.](./media/kantegassoforfisheyecrucible-tutorial/addon8.png)

    a. Lägg till namnet på identitets leverantören i text rutan för **identitets leverantörs namn** (t. ex. Azure AD).

    b. Klicka på **Nästa**.

1. Verifiera signerings certifikatet och klicka på **Nästa**.   

    ![Skärm bild som visar avsnittet "signaturverifiering" och knappen "Nästa" är markerad.](./media/kantegassoforfisheyecrucible-tutorial/addon9.png)

1. Utför följande steg i avsnittet **FishEye User Accounts** :

    ![Skärm bild som visar avsnittet "FishEye User Accounts" med alternativet "skapa användare i FishEye: s interna katalog" om det behövs "och knappen" Nästa "är markerad.](./media/kantegassoforfisheyecrucible-tutorial/addon10.png)

    a. Välj **skapa användare i FishEye interna katalog om det behövs** och ange rätt namn på gruppen för användare (kan vara flera). av grupper åtskilda med kommatecken).

    b. Klicka på **Nästa**.

1. Klicka på **Finish**.

    ![Skärm bild som visar avsnittet "Sammanfattning" med knappen "Slutför" markerat.](./media/kantegassoforfisheyecrucible-tutorial/addon11.png)

1. Utför följande steg på avsnittet **kända domäner för Azure AD** :  

    ![Skärm bild som visar avsnittet "kända domäner för Azure A D" med knappen "Spara" valt.](./media/kantegassoforfisheyecrucible-tutorial/addon12.png)

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
  
    b. I fältet **användar namn** `brittasimon@yourcompanydomain.extension` . Till exempel BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Kantega SSO för FishEye/degeln.

1. I Azure Portal väljer du **företags program**, väljer **alla program** och väljer sedan **Kantega SSO för FishEye/degeln**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **KANTEGA SSO för FishEye/degeln**.

    ![Kantega SSO för FishEye/filterdegeln-länken i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-kantega-sso-for-fisheyecrucible-test-user"></a>Skapa Kantega SSO för FishEye/filterdegeln test användare

Om du vill att Azure AD-användare ska kunna logga in på FishEye/degeln måste de tillhandahållas i FishEye/degeln. I Kantega SSO för FishEye/degeln är etableringen en manuell uppgift.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på den lokala filterdegeln-servern som administratör.

1. Hovra över kugg hjuls och klicka på **användarna**.

    ![Skärm bild som visar ikonen "kugg hjuls" markerad och "användare" valda i list rutan.](./media/kantegassoforfisheyecrucible-tutorial/user1.png)

1. Under fliken **användare** klickar du på **Lägg till användare**.

    ![Skärm bild som visar avsnittet "användare" med knappen "Lägg till användare" valt.](./media/kantegassoforfisheyecrucible-tutorial/user2.png)

1. Utför följande steg på dialog sidan **Lägg till ny användare** :

    ![Lägga till medarbetare](./media/kantegassoforfisheyecrucible-tutorial/user3.png)

    a. Skriv e-postadressen för användaren i textrutan **Användarnamn** som Brittasimon@contoso.com.

    b. I text rutan **visnings namn** skriver du visnings namn för användaren som Britta Simon.

    c. I textrutan för **e-postadress** skriver du användarens e-postadress som Brittasimon@contoso.com.

    d. I textrutan **Password** (Lösenord) skriver du lösenordet för användaren.

    e. Ange lösen ordet för användaren i text rutan **Bekräfta lösen ord** .

    f. Klicka på **Lägg till**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Kantega SSO för FishEye/filterdegeln i åtkomst panelen, bör du loggas in automatiskt på Kantega SSO för FishEye/degeln som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](./tutorial-list.md)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Vad är villkorlig åtkomst i Azure Active Directory?](../conditional-access/overview.md)