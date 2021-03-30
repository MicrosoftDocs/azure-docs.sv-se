---
title: 'Självstudie: Azure Active Directory integration med HR2day av Merces | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och HR2day av Merces.
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
ms.openlocfilehash: 94b152c842845433a55c8c1e285adabda1591260
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92442991"
---
# <a name="tutorial-azure-active-directory-integration-with-hr2day-by-merces"></a>Självstudie: Azure Active Directory integration med HR2day av Merces

I den här självstudien får du lära dig hur du integrerar HR2day med Merces med Azure Active Directory (Azure AD).
Genom att integrera HR2day från Merces med Azure AD får du följande fördelar:

* Du kan kontrol lera Azure AD som har åtkomst till HR2day av Merces.
* Du kan göra det möjligt för användarna att logga in automatiskt till HR2day av Merces (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med HR2day av Merces behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/)
* HR2day av Merces-aktiverad prenumeration med enkel inloggning

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* HR2day av Merces stöder **SP** -INITIERAd SSO

## <a name="adding-hr2day-by-merces-from-the-gallery"></a>Lägga till HR2day från Merces från galleriet

Om du vill konfigurera integrering av HR2day av Merces i Azure AD måste du lägga till HR2day från Merces från galleriet till din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till HR2day från Merces från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **HR2day efter Merces**, väljer **HR2day efter Merces** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

     ![HR2day efter Merces i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning i Azure AD med HR2day av Merces baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och en relaterad användare i HR2day av Merces.

Om du vill konfigurera och testa enkel inloggning med HR2day av Merces i Azure AD måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera HR2day av Merces enkel inloggning](#configure-hr2day-by-merces-single-sign-on)** -för att konfigurera de enskilda Sign-On inställningarna på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa HR2day av Merces test User](#create-hr2day-by-merces-test-user)** -om du vill ha en motsvarighet till Britta Simon i HR2day av Merces som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med HR2day med hjälp av Merces i Azure AD:

1. I [Azure Portal](https://portal.azure.com/)på sidan **HR2day av Merces** program integration väljer du **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![HR2day av Merces-domän och URL-information för enkel inloggning](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<tenantname>.force.com/<instancename>`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://hr2day.force.com/<companyname>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [HR2day från Merces client support team](mailto:servicedesk@merces.nl) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. HR2day av Merces-programmet förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade attribut mappningar i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut. Klicka på **Redigera** ikon för att öppna dialog rutan **användarattribut** .

    ![Skärm bild som visar användarattribut med redigerings ikonen vald.](common/edit-attribute.png)

    > [!NOTE]
    > Innan du kan konfigurera SAML Assertion måste du kontakta [HR2day av Merces-klient support teamet](mailto:servicedesk@merces.nl) och begära värdet för det unika ID-attributet för din klient. Du behöver det här värdet för att kunna slutföra stegen i nästa avsnitt.

6. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** så redigerar du anspråken genom att använda **Redigera-ikonen** eller lägga till anspråken genom att använda **Lägg till nytt anspråk** för att konfigurera SAML-tokenattribut som det visas i bilden ovan och utföra följande steg:

    | Name |  Källattribut |
    | ---------- | ----------- |
    | ATTR_LOGINCLAIM | `join([mail],"102938475Z","@"` |
    | | |

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![Skärm bild som visar användar anspråk med alternativet att lägga till ett nytt anspråk.](common/new-save-attribute.png)

    ![Skärm bild som visar dialog rutan hantera användar anspråk där du kan ange de värden som beskrivs.](common/new-attribute-details.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Lämna **Namnrymd** tom.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Klicka på **OK**

    ex. Klicka på **Spara**.

7. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

8. I avsnittet **Konfigurera HR2day efter Merces** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-hr2day-by-merces-single-sign-on"></a>Konfigurera HR2day av Merces Single Sign-On

Om du vill konfigurera enkel inloggning på **HR2day av Merces** -sidan måste du skicka det hämtade **certifikatet (base64)** och lämpliga kopierade url: er från Azure Portal till [HR2day av Merces support team](mailto:servicedesk@merces.nl). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

> [!NOTE]
> Omnämnande till Merces-teamet som den här integrationen kräver att entitets-ID: t anges med mönstret **https: \/ /HR2day.Force.com/instancename**.

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

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till HR2day av Merces.

1. I Azure Portal väljer du **företags program**, väljer **alla program** och väljer sedan **HR2day av Merces**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **HR2day av Merces**.

    ![HR2day av Merces-länken i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-hr2day-by-merces-test-user"></a>Skapa HR2day av Merces-test användare

I det här avsnittet skapar du en användare som heter Britta Simon i HR2day av Merces. Arbeta med [HR2day av Merces support team](mailto:servicedesk@merces.nl) för att lägga till användarna på HR2day av Merces-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

> [!NOTE]
> Om du behöver skapa en användare manuellt kan du kontakta [HR2day av Merces-klient support teamet](mailto:servicedesk@merces.nl).

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen HR2day av Merces på åtkomst panelen, bör du loggas in automatiskt på HR2day av Merces som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](./tutorial-list.md)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Vad är villkorlig åtkomst i Azure Active Directory?](../conditional-access/overview.md)