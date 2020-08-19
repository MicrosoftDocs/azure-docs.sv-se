---
title: 'Självstudie: Azure Active Directory integrering med Proofpoint på begäran | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Proofpoint on Demand.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: jeedes
ms.openlocfilehash: c3cebb7a63dd26f4dfb1fa46cb3b31a8b149da1d
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88553444"
---
# <a name="tutorial-azure-active-directory-integration-with-proofpoint-on-demand"></a>Självstudie: Azure Active Directory integrering med Proofpoint på begäran

I den här självstudien lär du dig hur du integrerar Proofpoint on Demand med Azure Active Directory (Azure AD).
Du får följande fördelar när du integrerar Proofpoint on Demand med Azure AD:

* Du kan styra i Azure AD vilka som har åtkomst till Proofpoint on Demand.
* Du kan göra så att dina användare automatiskt loggas in på Proofpoint on Demand (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att kunna konfigurera Azure AD-integrering med Proofpoint on Demand behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Proofpoint on Demand-prenumeration med enkel inloggning aktiverat

> [!NOTE]
> Om du använder MFA eller lösenordsbaserad autentisering med Azure AD stänger du av AuthnContext-värdet i SAML-begäran. Annars genererar Azure AD fel vid matchning av AuthnContext och skickar inte tillbaka token till programmet.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Proofpoint on Demand har stöd för **SP**-initierad enkel inloggning

## <a name="adding-proofpoint-on-demand-from-the-gallery"></a>Lägga till Proofpoint on Demand från galleriet

För att kunna konfigurera integreringen av Proofpoint on Demand i Azure AD måste du lägga till Proofpoint on Demand från galleriet i din lista över hanterade SaaS-appar.

**Gör följande för att lägga till Proofpoint on Demand från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **Proofpoint on Demand**, välj **Proofpoint on Demand** på resultatpanelen och klicka på knappen **Lägg till** i programmet.

     ![Proofpoint on Demand i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med Proofpoint on Demand baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Proofpoint on Demand upprättas.

För att kunna konfigurera och testa enkel inloggning i Azure AD med Proofpoint on Demand måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för Proofpoint on Demand](#configure-proofpoint-on-demand-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Proofpoint on Demand-testanvändare](#create-proofpoint-on-demand-test-user)** – så att det finns en motsvarighet till Britta Simon i Proofpoint on Demand som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Gör följande för att konfigurera enkel inloggning med Azure AD med Proofpoint on Demand:

1. I [Azure-portalen](https://portal.azure.com/), på sidan för **Proofpoint on Demand**-programintegrering, väljer du **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Proofpoint on Demand – enkel inloggning-information för domän och -URL:er](common/sp-identifier-reply.png)

    a. I text rutan **inloggnings-URL** skriver du en URL med följande mönster: `https://<hostname>.pphosted.com/ppssamlsp_hostname`

    b. I rutan **identifierare** anger du en URL med följande mönster: `https://<hostname>.pphosted.com/ppssamlsp`

    c. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<hostname>.pphosted.com:portnumber/v1/samlauth/samlconsumer`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska inloggnings-URL:en, identifieraren och svars-URL:en. Kontakta [Proofpoint on Demand-kundsupporten](https://www.proofpoint.com/us/support-services) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **Konfigurera Proofpoint on Demand** kopierar du en eller flera lämpliga URL:er, enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-proofpoint-on-demand-single-sign-on"></a>Konfigurera enkel inloggning med Proofpoint on Demand

För att kunna konfigurera enkel inloggning på **Proofpoint on Demand**-sidan måste du skicka det nedladdade **certifikatet (Base64)** och lämpliga kopierade URL:er från Azure-portalen till [Proofpoint on Demand-supporten](https://www.proofpoint.com/us/support-services). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

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

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att bevilja åtkomst till Proofpoint on Demand.

1. I Azure-portalen väljer du **Företagsprogram**, väljer **Alla program** och sedan **Proofpoint on Demand**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Proofpoint on Demand** i programlistan.

    ![Proofpoint on Demand-länken i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-proofpoint-on-demand-test-user"></a>Skapa Proofpoint on Demand-testanvändare

I det här avsnittet skapar du en användare med namnet Britta Simon i Proofpoint on Demand. Kontakta [Proofpoint on Demand-kundsupporten](https://www.proofpoint.com/us/support-services) för att lägga till användare på Proofpoint on Demand-plattformen.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Proofpoint on Demand-panelen i åtkomstpanelen bör du automatiskt loggas in på Proofpoint on Demand som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

