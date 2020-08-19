---
title: 'Självstudie: Azure Active Directory integrering med YouEarnedIt | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och YouEarnedIt.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.openlocfilehash: 48f00d657bfe2a1bcd335be1cc366d574383e869
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88546210"
---
# <a name="tutorial-azure-active-directory-integration-with-youearnedit"></a>Självstudie: Azure Active Directory integrering med YouEarnedIt

I den här självstudien får du lära dig hur du integrerar YouEarnedIt med Azure Active Directory (Azure AD).
Du får följande fördelar när du integrerar YouEarnedIt med Azure AD:

* Du kan styra i Azure AD vilka som har åtkomst till YouEarnedIt.
* Du kan göra så att dina användare automatiskt loggas in på YouEarnedIt (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att kunna konfigurera Azure AD-integrering med YouEarnedIt behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* YouEarnedIt-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* YouEarnedIt har stöd för **SP**-initierad enkel inloggning

## <a name="adding-youearnedit-from-the-gallery"></a>Lägga till YouEarnedIt från galleriet

För att kunna konfigurera integreringen av YouEarnedIt i Azure AD måste du lägga till YouEarnedIt från galleriet till din lista över hanterade SaaS-appar.

**Gör följande för att lägga till YouEarnedIt från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **YouEarnedIt** i sökrutan, välj **YouEarnedIt** på resultatpanelen och klicka på knappen **Lägg till** för att lägga till programmet.

     ![YouEarnedIt i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD YouEarnedIt baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i YouEarnedIt upprättas.

För att kunna konfigurera och testa enkel inloggning med Azure AD med YouEarnedIt måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för YouEarnedIt](#configure-youearnedit-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa YouEarnedIt-testanvändare](#create-youearnedit-test-user)** – så att det finns en motsvarighet till Britta Simon i YouEarnedIt som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Gör följande för att konfigurera enkel inloggning med Azure AD med YouEarnedIt:

1. I [Azure-portalen](https://portal.azure.com/) går du till sidan för **YouEarnedIt**-programintegrering och väljer **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![YouEarnedIt – enkel inloggning-information för domän och -URL:er](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL med följande mönster:

    | Miljö  | Mönster  |
    |:--- |:--- |
    | Produktion | `https://<company name>.youearnedit.com/users/sign_in` |
    | Begränsat läge  |`https://<company name>.sandbox.youearnedit.com/users/sign_in` |

    b. I textrutan **Identifierare** anger du en URL med följande mönster:

    | Miljö  | Mönster  |
    |:--- |:--- |
    | Produktion | `<company name>.youearnedit.com` |
    | Begränsat läge  |`<company name>.sandbox.youearnedit.com` |

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta din tilldelade YouEarnedIt Customer Success-ansvarig för att få dessa värden.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **Konfigurera YouEarnedIt** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-youearnedit-single-sign-on"></a>Konfigurera enkel inloggning med YouEarnedIt

För att kunna konfigurera enkel inloggning på **YouEarnedIt**-sidan måste du skicka det nedladdade **certifikatet (Base64)** och lämpliga kopierade URL:er från Azure-portalen till din tilldelade YouEarnedIt Customer Success-ansvarig. De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

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

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att bevilja åtkomst till YouEarnedIt.

1. I Azure Portal väljer du **Företagsprogram**, **Alla program** och sedan **YouEarnedIt**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan över program skriver och väljer du **YouEarnedIt**.

    ![YouEarnedIt-länken i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-youearnedit-test-user"></a>Skapa YouEarnedIt-testanvändare

I det här avsnittet skapar du en användaren med namnet Britta Simon i YouEarnedIt. Kontakta din tilldelade YouEarnedIt Customer Success-ansvarig för att lägga till användarna på YouEarnedIt-plattformen.

> [!NOTE]
> YouEarnedIt förväntar sig att identitetsprovidern tillhandahåller EmailAddress eller UserName i NameID-attributet. Autentiseringen misslyckas om motsvarande UserName eller EmailAddress inte hittas i databasen eller inte matchar exakt. Detta kräver att kontona importeras i YouEarnedIt-systemet innan integreringen av enkel inloggning görs (normalt via API eller CSV-import).

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på YouEarnedIt-panelen i åtkomstpanelen bör du automatiskt loggas in på YouEarnedIt som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
