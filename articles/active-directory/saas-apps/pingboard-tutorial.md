---
title: 'Självstudie: Azure Active Directory integrering med Pingboard | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Pingboard.
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
ms.openlocfilehash: d7b090eb5228a449212ceebd6cd299c5f8860089
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92520803"
---
# <a name="tutorial-azure-active-directory-integration-with-pingboard"></a>Självstudie: Azure Active Directory integrering med Pingboard

I den här självstudien får du lära dig hur du integrerar Pingboard med Azure Active Directory (Azure AD).
Genom att integrera Pingboard med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Pingboard.
* Du kan göra det möjligt för användarna att logga in automatiskt till Pingboard (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Pingboard behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Pingboard-aktiverad prenumeration med enkel inloggning

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Pingboard stöder **SP** -och **IDP** -initierad SSO

* Pingboard har stöd för [Automatisk användar etablering](./pingboard-provisioning-tutorial.md) 

## <a name="adding-pingboard-from-the-gallery"></a>Lägga till Pingboard från galleriet

Om du vill konfigurera integreringen av Pingboard i Azure AD måste du lägga till Pingboard från galleriet i listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till Pingboard från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök skriver du **Pingboard**, väljer **Pingboard** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

     ![Pingboard i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med Pingboard baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i Pingboard upprättas.

Om du vill konfigurera och testa enkel inloggning med Pingboard i Azure AD måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Pingboard enkel inloggning](#configure-pingboard-single-sign-on)** – om du vill konfigurera de enskilda Sign-On inställningarna på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Pingboard test User](#create-pingboard-test-user)** – om du vill ha en motsvarighet till Britta Simon i Pingboard som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med Pingboard i Azure AD:

1. Välj **enkel inloggning** på sidan **Pingboard** Application Integration i [Azure Portal](https://portal.azure.com/).

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, utför följande steg:

    ![Skärm bild som visar "Basic S A M L-konfiguration" med text rutorna "identifierare" och "svara U R L" och "Spara"-knappen markerat.](common/idp-intiated.png)

    a. Skriv en URL i text rutan **identifierare** : `http://app.pingboard.com/sp`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<entity-id>.pingboard.com/auth/saml/consume`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![Information om enkel inloggning för Pingboard-domän och URL: er](common/metadata-upload-additional-signon.png)

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<sub-domain>.pingboard.com/sign_in`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska svars-URL:en och inloggnings-URL:en. Kontakta [Pingboard client support team](https://support.pingboard.com/) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

7. I avsnittet **Konfigurera Pingboard** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-pingboard-single-sign-on"></a>Konfigurera Pingboard Single Sign-On

1. Om du vill konfigurera SSO på Pingboard-sidan öppnar du ett nytt webbläsarfönster och loggar in på ditt Pingboard-konto. Du måste vara Pingboard-administratör för att kunna konfigurera enkel inloggning.

2. På den översta menyn väljer du **appar >-integreringar**

    ![Konfigurera enkel inloggning](./media/pingboard-tutorial/Pingboard_integration.png)

3. På sidan **integrations** , leta upp panelen **"Azure Active Directory"** och klicka på den.

    ![Pingboard-integrering med enkla Sign-On](./media/pingboard-tutorial/Pingboard_aad.png)

4. I den modala som följer klickar du på **Konfigurera**

    ![Konfigurations knapp för Pingboard](./media/pingboard-tutorial/Pingboard_configure.png)

5. På följande sida ser du att "Azure SSO-integrering är aktive rad". Öppna XML-filen med hämtade metadata i ett anteckningar och klistra in innehållet i **metadata för IDP**.

    ![Konfigurations skärm för Pingboard SSO](./media/pingboard-tutorial/Pingboard_sso_configure.png)

6. Filen är verifierad och om allt är korrekt kommer enkel inloggning nu att aktive ras.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **användar namn** brittasimon@yourcompanydomain.extension . Till exempel BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Pingboard.

1. I Azure Portal väljer du **företags program**, väljer **alla program** och väljer sedan **Pingboard**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **Pingboard**.

    ![Pingboard-länken i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-pingboard-test-user"></a>Skapa Pingboard test användare

Syftet med det här avsnittet är att skapa en användare som kallas Britta Simon i Pingboard. Pingboard stöder automatisk användar etablering, som är aktiverat som standard. Du hittar mer information [här](pingboard-provisioning-tutorial.md) om hur du konfigurerar automatisk användaretablering.

**Om du behöver skapa användare manuellt så gör du följande:**

1. Logga in på din Pingboard-företags webbplats som administratör.

2. Klicka på knappen **Lägg till anställd** på **katalog** sidan.

    ![Lägga till medarbetare](./media/pingboard-tutorial/create_testuser_add.png)

3. Utför följande steg på dialog sidan **"Lägg till anställd"** :

    ![Bjud in personer](./media/pingboard-tutorial/create_testuser_name.png)

    a. I text rutan **fullständigt namn** skriver du det fullständiga namnet på användaren som **Britta Simon**.

    b. Skriv e-postadressen till användaren som i text rutan **e-post** **brittasimon@contoso.com** .

    c. Skriv jobb titeln för Britta Simon i text rutan för **jobb titeln** .

    d. I list rutan **plats** väljer du platsen för Britta Simon.

    e. Klicka på **Lägg till**.

4. En bekräftelse skärm är upp för att bekräfta att användaren har lagts till.

    ![Confirm](./media/pingboard-tutorial/create_testuser_confirm.png)

    > [!NOTE]
    > Azure Active Directory-kontoinnehavaren får ett e-postmeddelande och följer en länk för att bekräfta kontot innan det blir aktivt.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Pingboard på åtkomst panelen, bör du loggas in automatiskt på den Pingboard som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](./tutorial-list.md)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Vad är villkorlig åtkomst i Azure Active Directory?](../conditional-access/overview.md)

- [Konfigurera användar etablering](./pingboard-provisioning-tutorial.md)