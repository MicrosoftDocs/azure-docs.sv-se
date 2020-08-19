---
title: 'Självstudie: Azure Active Directory-integrering med LockPath-indikering | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och LockPath-indikering.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.openlocfilehash: 82af00c091aedb56e0cad954e554094e75df0013
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88535891"
---
# <a name="tutorial-azure-active-directory-integration-with-lockpath-keylight"></a>Självstudie: Azure Active Directory integrering med LockPath-indikering

I den här självstudien får du lära dig att integrera LockPath-indikering med Azure Active Directory (Azure AD).
Att integrera LockPath-indikering med Azure AD ger följande fördelar:

* Du kan styra Azure AD som har åtkomst till LockPath-indikering.
* Du kan göra det möjligt för användarna att logga in automatiskt till LockPath (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med LockPath-indikering behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/)
* LockPath-aktiverad prenumeration för enkel inloggning

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* LockPath-ljus stöder **SP** -INITIERAd SSO
* LockPath-indikering stöder **just-in-Time** User-etablering

## <a name="adding-lockpath-keylight-from-the-gallery"></a>Lägga till LockPath-ljus från galleriet

Om du vill konfigurera integrationen av LockPath-i Azure AD måste du lägga till LockPath-ljus från galleriet i listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till LockPath-indikering från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **LockPath-indikering**, väljer **LockPath-indikering** från resultat panelen och klickar sedan på knappen **Lägg till** för att lägga till programmet.

    ![LockPath-indikering i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning i Azure AD med LockPath-indikering baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i LockPath-.

Om du vill konfigurera och testa enkel inloggning med LockPath i Azure AD måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera LockPath för enkel inloggning med enkel inloggning](#configure-lockpath-keylight-single-sign-on)** för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa LockPath-test för att testa användaren](#create-lockpath-keylight-test-user)** – om du vill ha en motsvarighet till Britta Simon i LockPath-ljust som är länkat till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning för Azure AD med LockPath-indikering:

1. I [Azure Portal](https://portal.azure.com/)väljer du **enkel inloggning**på LockPath-sidan för program integrering i **LockPath Keylight** .

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning för LockPath-och webb adresser](common/sp-identifier-reply.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<company name>.keylightgrc.com/`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<company name>.keylightgrc.com`

    c. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://<company name>.keylightgrc.com/Login.aspx`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med faktisk inloggnings-URL, identifierare och svars-URL. Kontakta [LockPath-support teamet](https://www.lockpath.com/contact/) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML**, i avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (RAW)** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/certificateraw.png)

6. Kopiera lämpliga URL: er enligt ditt krav i avsnittet **Konfigurera LockPath-** .

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-lockpath-keylight-single-sign-on"></a>Konfigurera LockPath-enkel inloggning med enkel inloggning

1. Utför följande steg för att aktivera SSO i LockPath-ljus:

    a. Logga in på ditt LockPath-konto som administratör.

    b. I menyn högst upp klickar du på **person**och väljer sedan inställningar för att ställa in en **indikering**.

    ![Konfigurera enkel inloggning](./media/keylight-tutorial/401.png)

    c. Klicka på **SAML**i trädvyn till vänster.

    ![Konfigurera enkel inloggning](./media/keylight-tutorial/402.png)

    d. I dialog rutan **SAML-inställningar** klickar du på **Redigera**.

    ![Konfigurera enkel inloggning](./media/keylight-tutorial/404.png)

1. På dialog sidan **Redigera SAML-inställningar** utför du följande steg:

    ![Konfigurera enkel inloggning](./media/keylight-tutorial/405.png)

    a. Konfigurera **SAML-autentisering** till **aktiv**.

    b. I text rutan **inloggnings-URL för identitetsprovider** klistrar du in URL-värdet för **inloggning** som du har kopierat från Azure Portal.

    c. I text rutan **URL för identitets leverantörs utloggning** klistrar du in URL-värdet för **utloggning** som du har kopierat från Azure Portal.

    d. Klicka på **Välj fil** för att välja det hämtade LockPath-och klicka sedan på **Öppna** för att ladda upp certifikatet.

    e. Ange **SAML-användarens ID-plats** till **NameIdentifier-elementet för ämnes instruktionen**.

    f. Tillhandahåll **tjänst leverantören** för en indikering med följande mönster: `https://<CompanyName>.keylightgrc.com` .

    ex. Ange **användare för automatisk etablering** till **aktiv**.

    h. Ange en **konto typ för automatisk etablering** till **fullständig användare**.

    i. Ange **säkerhets roll för automatisk etablering**, Välj **Standard användare med SAML**.

    j. Ange **Automatisk etablering av säkerhets**konfiguration, Välj **standard användar konfiguration**.

    k. I text rutan **email-attribut** skriver du `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` .

    l. I text rutan för det **första** namnattributet skriver du `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` .

    m. I text rutan för **senaste namnattribut** skriver du `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` .

    n. Klicka på **Spara**.

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

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till LockPath-ljus.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **LockPath-indikering**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **LockPath-indikering**.

    ![LockPath-länken i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-lockpath-keylight-test-user"></a>Skapa LockPath test User

I det här avsnittet skapas en användare som kallas Britta Simon i LockPath-indikering. LockPath-stöder just-in-Time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i LockPath-ljuset, skapas en ny efter autentiseringen. Om du behöver skapa en användare manuellt måste du kontakta [support teamet för LockPath-](https://www.lockpath.com/contact/).

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen för LockPath-på åtkomst panelen, bör du loggas in automatiskt på det LockPath-som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)