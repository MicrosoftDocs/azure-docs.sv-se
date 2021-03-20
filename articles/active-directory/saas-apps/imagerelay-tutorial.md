---
title: 'Självstudie: Azure Active Directory integrering med Image Relay | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Image Relay.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/20/2019
ms.author: jeedes
ms.openlocfilehash: b76c2e346adb6c2afd146b0d73c8f20165145bac
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92460372"
---
# <a name="tutorial-azure-active-directory-integration-with-image-relay"></a>Självstudie: Azure Active Directory integrering med Image Relay

I den här självstudien lär du dig att integrera Image Relay med Azure Active Directory (AD Azure).
Integreringen av Image Relay med Azure AD medför följande fördelar:

* Du kan i Azure AD styra vem som har åtkomst till Image Relay.
* Du kan göra så att dina användare automatiskt loggas in på Image Relay (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Image Relay behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Image Relay-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Image Relay har stöd för **SP**-initierad enkel inloggning

## <a name="adding-image-relay-from-the-gallery"></a>Lägga till Image Relay från galleriet

För att konfigurera integreringen av Image Relay i Azure AD behöver du lägga till Image Relay från galleriet till din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Image Relay från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Image Relay**, väljer **Image Relay** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

    ![Image Relay i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning i Azure AD med Image Relay baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Image Relay upprättas.

För att kunna konfigurera och testa enkel inloggning i Azure AD med Image Relay slutför du följande byggstenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för Image Relay](#configure-image-relay-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Image Relay-testanvändare](#create-image-relay-test-user)** – för att ha en motsvarighet för Britta Simon i Image Relay som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning i Azure AD med Image Relay:

1. I [Azure-portalen](https://portal.azure.com/) går du till programintegreringssidan för **Image Relay** och väljer **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning med Image Relay-domän och URL:er](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<companyname>.imagerelay.com/`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<companyname>.imagerelay.com/sso/metadata`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [kundsupporten för Image Relay](http://support.imagerelay.com/) och be om dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

4. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **Konfigurera Image Relay** kopierar du lämpliga URL:er efter behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-image-relay-single-sign-on"></a>Konfigurera enkel inloggning för Image Relay

1. I ett annat webbläsarfönster loggar du in på din Image Relay-företagswebbplats som administratör.

2. I verktygsfältet längst upp klickar du på arbetsbelastningen **Users & Permissions** (Användare och behörigheter).

    ![Skärm bild som visar användare & behörigheter som valts i verktygsfältet.](./media/imagerelay-tutorial/tutorial_imagerelay_06.png) 

3. Klicka på **Create New Permission** (Skapa ny behörighet).

    ![Skärm bild som visar en text ruta där du kan ange behörighets rubrik och ett alternativ för att välja behörighets typ.](./media/imagerelay-tutorial/tutorial_imagerelay_08.png)

4. I arbetsbelastningen **Single Sign On Settings** (Inställningar för enkel inloggning) markerar du kryssrutan **This Group can only sign-in via Single Sign On** (Den här gruppen kan bara logga in via enkel inloggning) och klickar sedan på **Spara**.

    ![Skärm bild som visar inställningarna för enkel inloggning där du kan välja alternativet.](./media/imagerelay-tutorial/tutorial_imagerelay_09.png) 

5. Gå till **Kontoinställningar**.

    ![Skärm bild som visar verktygsfälts alternativet konto inställningar.](./media/imagerelay-tutorial/tutorial_imagerelay_10.png) 

6. Gå till arbetsbelastningen **Single Sign On Settings** (Inställningar för enkel inloggning).

    ![Skärm bild som visar meny alternativet enkel inloggning på inställningar.](./media/imagerelay-tutorial/tutorial_imagerelay_11.png)

7. Gör följande i dialogrutan **SAML-inställningar**:

    ![Skärm bild som visar dialog rutan SAML-inställningar där du kan ange informationen.](./media/imagerelay-tutorial/tutorial_imagerelay_12.png)

    a. I textrutan för **inloggnings-URL** klistrar du in värdet för **inloggnings-URL:en** som du har kopierat från Azure-portalen.

    b. I textrutan för **utloggnings-URL** klistrar du in värdet för den **utloggnings-URL** som du har kopierat från Azure-portalen.

    c. Som **Name Id Format** (Format för namn-ID) väljer du **urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress**.

    d. Som **Binding Options for Requests from the Service Provider (Image Relay)** (Bindningsalternativ för begäranden från tjänstleverantör (Image Relay)) väljer du **POST Binding** (POST-bindning).

    e. Under **x.509 Certificate** (x.509-certifikat) klickar du på **Update Certificate** (Uppdatera certifikat).

    ![Skärm bild som visar alternativet att uppdatera certifikat.](./media/imagerelay-tutorial/tutorial_imagerelay_17.png)

    f. Öppna det nedladdade certifikatet i Anteckningar, kopiera innehållet och klistra in det i textrutan **x.509 Certificate** (x.509-certifikat).

    ![Skärm bild som visar x x 509-certifikatet.](./media/imagerelay-tutorial/tutorial_imagerelay_18.png)

    ex. I avsnittet **Just-In-Time User Provisioning** (Just-in-time-användaretablering) väljer du **Enable Just-In-Time User Provisioning** (Aktivera just-in-time-användaretablering).

    ![Skärm bild som visar avsnittet användar etablering just-in-Time med Aktivera kontroll markerat.](./media/imagerelay-tutorial/tutorial_imagerelay_19.png)

    h. Välj den behörighetsgrupp (till exempel **SSO Basic** (Enkel inloggning, grundläggande)) som endast tillåts logga in via enkel inloggning.

    ![Skärm bild som visar avsnittet användar etablering för just-in-Time med S O Basic valt.](./media/imagerelay-tutorial/tutorial_imagerelay_20.png)

    i. Klicka på **Spara**.

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
    Till exempel BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till Image Relay.

1. I Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **Image Relay**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **Image Relay**.

    ![Länken för Image Relay i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-image-relay-test-user"></a>Skapa Image Relay-testanvändare

Målet med det här avsnittet är att skapa en användare med namnet Britta Simon i Image Relay.

**Utför följande steg för att skapa en användare som heter Britta Simon i Image Relay:**

1. Logga in på din Image Relay-företagswebbplats som administratör.

2. Gå till **Users & Permissions** (Användare och behörigheter) och välj **Create SSO User** (Skapa användare för enkel inloggning).

    ![Skärm bild som visar skapa S O-användare som valts på menyn.](./media/imagerelay-tutorial/tutorial_imagerelay_21.png) 

3. Ange **E-postadress**, **Förnamn**, **Efternamn** och **Företag** för den användare som du vill etablera och välj den behörighetsgrupp (till exempel SSO Basic (Enkel inloggning, grundläggande)) som endast tillåts logga in via enkel inloggning.

    ![Skärm bild som visar sidan Skapa en S O-användare där du kan ange den information som krävs.](./media/imagerelay-tutorial/tutorial_imagerelay_22.png)

4. Klicka på **Skapa**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Image Relay-panelen i åtkomstpanelen bör du automatiskt loggas in på Image Relay som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](./tutorial-list.md)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Vad är villkorlig åtkomst i Azure Active Directory?](../conditional-access/overview.md)