---
title: 'Självstudie: Azure Active Directory integrering med Tableau online | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Tableau online.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.openlocfilehash: 035bc13ee44a8a003ae860eb2bdd67432fa91f14
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88542533"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tableau-online"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med Tableau online

I den här självstudien får du lära dig hur du integrerar Tableau online med Azure Active Directory (Azure AD). När du integrerar Tableau online med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Tableau online.
* Gör det möjligt för användarna att logga in automatiskt till Tableau online med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Tableau online, enkel inloggning (SSO) aktive rad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Tableau online stöder **SP** -INITIERAd SSO
* När du har konfigurerat Tableau online kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen utökas från villkorlig åtkomst. [Lär dig hur du tvingar fram sessions kontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-tableau-online-from-the-gallery"></a>Lägga till Tableau online från galleriet

Om du vill konfigurera integreringen av Tableau online i Azure AD måste du lägga till Tableau online från galleriet till listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **Tableau online** i sökrutan.
1. Välj **Tableau online** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med Tableau online baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och en relaterad användare i Tableau online.

Om du vill konfigurera och testa Azure AD SSO med Tableau online slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera Tableau online SSO](#configure-tableau-online-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa Tableau online-test](#create-tableau-online-test-user)** för att få en motsvarighet till B. Simon i Tableau online som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med Tableau online för Azure AD:

1. I [Azure Portal](https://portal.azure.com/)på sidan **Tableau online** Application Integration väljer du **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning för Tableau online-domän och URL: er](common/sp-identifier.png)

    a. I rutan **Inloggnings-URL** anger du följande URL: `https://sso.online.tableau.com/public/sp/login?alias=<entityid>`

    b. I textrutan **Identifierare (entitets-ID)** anger du URL:en: `https://sso.online.tableau.com/public/sp/metadata?alias=<entityid>`

    > [!NOTE]
    > Du får `<entityid>` värdet från avsnittet **Konfigurera Tableau online** i den här självstudien. Värdet för entitets-ID är ett värde för **Azure AD-identifieraren** i avsnittet **Konfigurera Tableau online** .

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. I avsnittet **Konfigurera Tableau online** kopierar du lämpliga URL: er enligt ditt krav.

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

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **användar namn** skriver du **brittasimon \@ yourcompanydomain. extension**  
    Till exempel BrittaSimon \@ contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Tableau online.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **Tableau online**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **Tableau online**.

    ![Länken Tableau online i listan program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

## <a name="configure-tableau-online-sso"></a>Konfigurera Tableau online SSO

1. Logga in på ditt Tableau online-program i ett annat webbläsarfönster. Gå till **Inställningar** och sedan **autentisering**.

    ![Konfigurera enkel inloggning](./media/tableauonline-tutorial/tutorial_tableauonline_09.png)

2. För att aktivera SAML under avsnittet **autentiseringsmetoder** . Markera **aktivera ytterligare en autentiseringsmetod** och markera sedan **SAML** -kryss rutan.

    ![Konfigurera enkel inloggning](./media/tableauonline-tutorial/tutorial_tableauonline_12.png)

3. Rulla nedåt uppåt för att **Importera metadatafilen till avsnittet Tableau online** .  Klicka på Bläddra och importera metadatafilen som du har laddat ned från Azure AD. Klicka sedan på **Använd**.

   ![Konfigurera enkel inloggning](./media/tableauonline-tutorial/tutorial_tableauonline_13.png)

4. I avsnittet **matchnings intyg** infogar du motsvarande namn på identitets leverantören för **e-postadress**, **förnamn**och **efter namn**. För att hämta den här informationen från Azure AD: 
  
    a. Gå till sidan **Tableau online** application integration i Azure Portal.

    b. I avsnittet **användarattribut &-anspråk** klickar du på redigerings ikonen.

   ![Konfigurera enkel inloggning](./media/tableauonline-tutorial/attributesection.png)

    c. Kopiera namn rymds värdet för dessa attribut: givenName, e-post och efter namn med hjälp av följande steg:

   ![Enkel inloggning för Azure AD](./media/tableauonline-tutorial/tutorial_tableauonline_10.png)

    d. Klicka på **User. givenName** -värde

    e. Kopiera värdet från text rutan **namnrymd** .

    ![Konfigurera enkel inloggning](./media/tableauonline-tutorial/attributesection2.png)

    f. Om du vill kopiera namn rymds värden för e-postmeddelandet och förnamnet upprepar du ovanstående steg.

    ex. Växla till Tableau online-programmet och ange sedan avsnittet **användarattribut &-anspråk** enligt följande:

    * E-post: **mail** eller **userPrincipalName**

    * Förnamn: **givenName**

    * Efter namn: efter **namn**

    ![Konfigurera enkel inloggning](./media/tableauonline-tutorial/tutorial_tableauonline_14.png)

### <a name="create-tableau-online-test-user"></a>Skapa Tableau online-test användare

I det här avsnittet skapar du en användare som heter Britta Simon i Tableau online.

1. På **Tableau online**klickar du på **Inställningar** och sedan på **autentisering** . Rulla ned till avsnittet **Hantera användare** . Klicka på **Lägg till användare** och sedan på **Ange e-postadresser**.
  
    ![Skapa en testanvändare för Azure AD](./media/tableauonline-tutorial/tutorial_tableauonline_15.png)

2. Välj **Lägg till användare för (SAML) autentisering**. I text rutan **Ange e-postadresser** lägger du till Britta. Simon \@ contoso.com
  
    ![Skapa en testanvändare för Azure AD](./media/tableauonline-tutorial/tutorial_tableauonline_11.png)

3. Klicka på **Lägg till användare**.

### <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Tableau online på åtkomst panelen, bör du loggas in automatiskt på Tableau online som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vad är session Control i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)