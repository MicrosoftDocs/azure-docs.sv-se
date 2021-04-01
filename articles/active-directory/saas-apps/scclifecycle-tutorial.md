---
title: 'Självstudie: Azure Active Directory integrering med SCC-livscykel | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SCC livs cykel.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/22/2019
ms.author: jeedes
ms.openlocfilehash: 3c296276012963a2ec8652a91e265bb6c94df73b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92669659"
---
# <a name="tutorial-azure-active-directory-integration-with-scc-lifecycle"></a>Självstudie: Azure Active Directory integrering med SCC-livscykel

I den här självstudien får du lära dig att integrera SCC-livscykel med Azure Active Directory (Azure AD).
Genom att integrera SCC-livscykel med Azure AD får du följande fördelar:

* Du kan styra Azure AD som har åtkomst till SCC-livscykeln.
* Du kan göra det möjligt för användarna att logga in automatiskt till SCC-livscykel (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med SCC livs cykel behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/)
* SCC LifeCycle-prenumeration med enkel inloggning aktive rad

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* SCC-livscykel stöder **SP** -INITIERAd SSO

## <a name="adding-scc-lifecycle-from-the-gallery"></a>Lägga till SCC-livscykel från galleriet

Om du vill konfigurera integreringen av SCC-livscykeln i Azure AD måste du lägga till SCC-livscykel från galleriet i listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till SCC-livscykel från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **SCC Lifecycle**, väljer **SCC Lifecycle** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

    ![SCC-livscykel i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med SCC-livscykel baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och en relaterad användare i SCC-livscykeln.

Om du vill konfigurera och testa enkel inloggning med Azure AD med SCC-livscykel måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera SCC livs cykel enkel inloggning – om](#configure-scc-lifecycle-single-sign-on)** du vill konfigurera enskilda Sign-On inställningar på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa SCC Lifecycle test User](#create-scc-lifecycle-test-user)** – om du vill ha en motsvarighet till Britta Simon i SCC-livscykel som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med SCC-livscykel i Azure AD:

1. I [Azure Portal](https://portal.azure.com/)på sidan **SCC Lifecycle** Application Integration väljer du **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![SCC-livscykel domän och URL: er enkel inloggnings information](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<sub-domain>.scc.com/ic7/welcome/customer/PICTtest.aspx`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: 
    
    - `https://bs1.scc.com/<entity>`
    - `https://lifecycle.scc.com/<entity>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [SCC Lifecycle support team](mailto:lifecycle.support@scc.com) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. I avsnittet **Konfigurera SCC livs cykel** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-scc-lifecycle-single-sign-on"></a>Konfigurera SCC-livscykel, enkel Sign-On

Om du vill konfigurera enkel inloggning på **SCC livs cykel** sida måste du skicka XML för hämtade **metadata** och lämpliga kopierade url: er från Azure Portal till [SCC Lifecycle support team](mailto:lifecycle.support@scc.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

   > [!NOTE]
   > Enkel inloggning måste aktive ras av [SCC Lifecycle support team](mailto:lifecycle.support@scc.com).

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

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till SCC-livscykel.

1. I Azure Portal väljer du **företags program**, väljer **alla program** och väljer sedan **SCC livs cykel**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **SCC livs cykel**.

    ![SCC livs cykel länk i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-scc-lifecycle-test-user"></a>Skapa SCC LifeCycle test User

För att Azure AD-användare ska kunna logga in på SCC-livscykel måste de tillhandahållas i SCC-livscykeln. Det finns inget åtgärds objekt som du kan använda för att konfigurera användar etablering till SCC-livscykel.

När en tilldelad användare försöker logga in i SCC-livscykeln skapas ett SCC-livscykel-konto automatiskt vid behov.

> [!NOTE]
> Azure Active Directory-kontoinnehavaren får ett e-postmeddelande och följer en länk för att bekräfta kontot innan det blir aktivt.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen SCC livs cykel i åtkomst panelen, bör du loggas in automatiskt på den SCC-livscykel som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](./tutorial-list.md)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Vad är villkorlig åtkomst i Azure Active Directory?](../conditional-access/overview.md)