---
title: 'Självstudie: Azure Active Directory integration med uppfattning USA (icke-UltiPro) | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och uppfattning USA (icke-UltiPro).
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
ms.openlocfilehash: 3fc16a869c176074467a0b21880937d7753602b2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92515533"
---
# <a name="tutorial-azure-active-directory-integration-with-perception-united-states-non-ultipro"></a>Självstudie: Azure Active Directory integration med uppfattning USA (icke-UltiPro)

I den här självstudien får du lära dig att integrera uppfattning USA (icke-UltiPro) med Azure Active Directory (Azure AD).
Genom att integrera uppfattning USA (icke-UltiPro) med Azure AD får du följande fördelar:

* Du kan styra Azure AD som har till gång till uppfattning USA (icke-UltiPro).
* Du kan göra det möjligt för användarna att logga in automatiskt för att få uppfattning om USA (icke-UltiPro) (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med uppfattning USA (icke-UltiPro) behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Uppfattning USA (icke-UltiPro) aktive rad prenumeration med enkel inloggning

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Uppfattning USA (icke-UltiPro) stöder **IDP** INITIERAd SSO

## <a name="adding-perception-united-states-non-ultipro-from-the-gallery"></a>Lägga till uppfattning USA (icke-UltiPro) från galleriet

För att konfigurera integrering av uppfattning USA (icke-UltiPro) i Azure AD måste du lägga till uppfattning USA (icke-UltiPro) från galleriet till listan över hanterade SaaS-appar.

**Gör så här om du vill lägga till uppfattning USA (icke-UltiPro) från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **uppfattning USA (icke-UltiPro)**, väljer **uppfattning USA (icke-UltiPro)** från resultat panelen och klickar sedan på knappen **Lägg till** för att lägga till programmet.

     ![Uppfattning USA (icke-UltiPro) i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med uppfattning USA (icke-UltiPro) baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i uppfattning USA (icke-UltiPro) upprättas.

Om du vill konfigurera och testa enkel inloggning med Azure AD med uppfattning USA (icke-UltiPro) måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera USA för enkel inloggning (icke-UltiPro)](#configure-perception-united-states-non-ultipro-single-sign-on)** för att konfigurera de enskilda Sign-On inställningarna på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa uppfattning USA (icke-UltiPro) test användare](#create-perception-united-states-non-ultipro-test-user)** – för att få en motsvarighet till Britta Simon i uppfattning USA (icke-UltiPro) som är länkad till Azure AD-representationen av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med Azure AD med uppfattning USA (icke-UltiPro):

1. På sidan [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **uppfattning USA (icke-UltiPro)** och väljer **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. På sidan **Konfigurera enkel inloggning med SAML** utför du följande steg:

    ![Information om hur du USA (icke-UltiPro) domän och URL: er för enkel inloggning](common/idp-intiated.png)

    a. Skriv en URL i text rutan **identifierare** : `https://perception.kanjoya.com/sp`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://perception.kanjoya.com/sso?idp=<entity_id>`

    c. UltiPro-programmet **()** kräver att värdet för **Azure AD-identifieraren** är <entity_id>, som du kommer att få från avsnittet **Konfigurera uppfattande USA (icke-UltiPro)** som ska vara URI-kodat. USA Använd följande länk för att hämta URI-kodat värde: **http://www.url-encode-decode.com/** .

    d. När du har hämtat det URI-kodade värdet kombinera det med **svars-URL: en** enligt ovan –

    `https://perception.kanjoya.com/sso?idp=<URI encooded entity_id>`
    
    e. Klistra in ovanstående värde i text rutan **svars-URL** .

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. I avsnittet **Konfigurera uppfattning USA (icke-UltiPro)** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL   

### <a name="configure-perception-united-states-non-ultipro-single-sign-on"></a>Konfigurera uppfattning USA (icke-UltiPro) Single Sign-On

1. I ett annat webbläsarfönster loggar du in på din uppfattning USA-företags (icke-UltiPro) företags plats som administratör.

2. Klicka på **konto inställningar** i huvud verktygsfältet.

    ![Skärm bild som visar "konto inställningar" som valts från huvud verktygsfältet.](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_user.png)

3. Utför följande steg på sidan **konto inställningar** :

    ![Uppfattning USA (icke-UltiPro) användare](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_account.png)

    a. I text rutan **företags namn** skriver du namnet på **företaget**.
    
    b. Skriv namnet på **kontot** i text rutan **konto namn** .

    c. Skriv giltig **e**-postadress i text rutan **standard Reply-To e-postadress** .

    d. Välj **SSO Identity Provider** som **SAML 2,0**.

4. Utför följande steg på sidan **SSO-konfiguration** :

    ![Uppfattning USA (icke-UltiPro) SSOConfig](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_ssoconfig.png)

    a. Välj **SAML NameID typ** som **e-postadress**.

    b. I text rutan **namn på SSO-konfiguration** skriver du namnet på din **konfiguration**.
    
    c. I text rutan **namn på identitetsprovider** , klistrar du in värdet för **Azure AD-identifierare**, som du har kopierat från Azure Portal. 

    d. I **text rutan SAML-domän** anger du domänen som @contoso.com .

    e. Klicka på **överför igen** för att ladda upp **metadata-XML-** filen.

    f. Klicka på **Uppdatera**.

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

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till uppfattning USA (icke-UltiPro).

1. I Azure Portal väljer du **företags program**, väljer **alla program** och väljer sedan **uppfattning USA (icke-UltiPro)**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **uppfattning USA (icke-UltiPro)**.

    ![Den uppfattning USA-länken (icke-UltiPro) i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-perception-united-states-non-ultipro-test-user"></a>Skapa uppfattning USA (icke-UltiPro) test användare

I det här avsnittet skapar du en användare med namnet Britta Simon i uppfattning USA (icke-UltiPro). Arbeta med [USA-support teamet (som inte är UltiPro)](https://www.ultimatesoftware.com/Contact/ContactUs) för att lägga till användarna i den uppfattning USA-plattformen (icke-UltiPro).

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen för uppfattning USA (icke-UltiPro) i åtkomst panelen, bör du loggas in automatiskt på den uppfattning USA (icke-UltiPro) som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](./tutorial-list.md)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Vad är villkorlig åtkomst i Azure Active Directory?](../conditional-access/overview.md)