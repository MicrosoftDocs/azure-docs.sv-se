---
title: 'Självstudie: Azure Active Directory integrering med oändligt Campus | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och oändlig campus.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 08cee87d94b20e99bcc46d42cbac7eb4272561bb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92460250"
---
# <a name="tutorial-azure-active-directory-integration-with-infinite-campus"></a>Självstudie: Azure Active Directory integrering med oändligt Campus

I den här självstudien får du lära dig att integrera oändlig campus med Azure Active Directory (Azure AD).
Att integrera oändlig campus med Azure AD ger följande fördelar:

* Du kan kontrol lera Azure AD som har åtkomst till oändliga campus.
* Du kan göra det möjligt för användarna att logga in automatiskt till oändligt Campus (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med oändligt Campus behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/)
* Obegränsad enkel inloggning med en aktive rad Campus-prenumeration
* Du måste vara en Azure Active Directory administratör och ha en campus-säkerhetsroll för "student information system (SIS)" för att slutföra konfigurationen.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Oändlig Campus stöder **SP** -INITIERAd SSO

## <a name="adding-infinite-campus-from-the-gallery"></a>Lägga till oändligt Campus från galleriet

Om du vill konfigurera integreringen av oändliga campus i Azure AD måste du lägga till oändligt Campus från galleriet i listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till oändliga Campus från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program klickar du på knappen **nytt program** överst i dialog rutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök skriver du **oändligt Campus**, väljer **oändligt Campus** från resultat panelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

    ![Oändligt campus i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa enkel inloggning med Azure AD med oändligt Campus baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i oändliga Campus upprättas.

För att konfigurera och testa enkel inloggning med Azure AD med oändligt Campus måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera oändligt läge för enkel inloggning i Campus](#configure-infinite-campus-single-sign-on)** för att konfigurera enskilda Sign-On inställningar på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa en oändlig Campus-test användare](#create-infinite-campus-test-user)** – för att få en motsvarighet till Britta Simon i oändlig Campus som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med oändliga campus i Azure AD:

1. I [Azure Portal](https://portal.azure.com/)på sidan **oändlig Campus** -programintegration väljer du **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet grundläggande SAML-konfiguration utför du följande steg (Observera att domänen varierar beroende på värd modell, men att värdet **fullständigt kvalificerande domän** måste matcha din oändliga Campus-installation):

    a. I text rutan **inloggnings-URL** skriver du en URL med följande mönster: `https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>/SIS`

    b. I text rutan **identifierare** skriver du en URL med följande mönster: `https://<DOMAIN>.infinitecampus.com/campus/<DISTRICTNAME>`

    c. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>`

    ![Oändlig information om enkel inloggning med oändliga Campus-domäner och URL: er](common/sp-identifier-reply.png)

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på kopieringsknappen för att kopiera **App Federation Metadata-URL** och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

### <a name="configure-infinite-campus-single-sign-on"></a>Konfigurera oändligt Campus-Sign-On

1. Logga in på oändligt Campus som säkerhets administratör i ett annat webbläsarfönster.

2. Klicka på **system administration** på vänster sida av menyn.

    ![Administratören](./media/infinitecampus-tutorial/tutorial_infinitecampus_admin.png)

3. Navigera till **användar säkerhet**  >  **SAML Management**  >  **SSO Service Provider Configuration**.

    ![SAML-](./media/infinitecampus-tutorial/tutorial_infinitecampus_saml.png)

4. Utför följande steg på sidan **konfiguration av SSO-** tjänstprovider:

    ![SSO](./media/infinitecampus-tutorial/tutorial_infinitecampus_sso.png)

    a. Välj **aktivera enkel SAML-inloggning**.

    b. Redigera det **valfria attributnamnet** som innehåller **namn**

    c. I avsnittet **Välj ett alternativ för att hämta ID för IDP-Server** väljer du URL för **metadata**, klistrar in URL-värdet för **appens federationens metadata** , som du har kopierat från Azure Portal i rutan och klickar sedan på **Synkronisera**.

    d. När du har klickat på **Synkronisera** hämtas värdena automatiskt i **konfigurations sidan för SSO-tjänsteprovidern** . Dessa värden kan kontrol leras för att matcha värdena som visas i steg 4 ovan.

    e. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **användar namn** `brittasimon@yourcompanydomain.extension` . Till exempel BrittaSimon@contoso.com.

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

> [!NOTE]
> Om du vill att alla dina Azure-användare ska ha enkel inloggning till oändligt campus och förlitar sig på oändliga Campus-system för interna behörigheter för att kontrol lera åtkomst, kan du ange egenskapen för **användar tilldelningen som krävs** för programmet till Nej och hoppa över följande steg.

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till oändligt campus.

1. I Azure Portal väljer du **företags program**, väljer **alla program** och väljer sedan **oändligt Campus**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **oändligt Campus**.

    ![Den oändliga Campus-länken i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-infinite-campus-test-user"></a>Skapa oändligt Campus-test användare

Oändligt campus har en demografiskt centrerad arkitektur. Kontakta ett [oändligt Campus support team](mailto:sales@infinitecampus.com) för att lägga till användarna på den oändliga Campus-plattformen.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen oändligt campus i åtkomst panelen, bör du loggas in automatiskt på den oändliga Campus som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](./tutorial-list.md)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Vad är villkorlig åtkomst i Azure Active Directory?](../conditional-access/overview.md)