---
title: 'Självstudie: Azure Active Directory integrering med arbets plats i tidigare | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och tidigare arbets plats.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/08/2019
ms.author: jeedes
ms.openlocfilehash: 8a21fecbe39008a79b60bdc9f133c3540d440207
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88545095"
---
# <a name="tutorial-azure-active-directory-integration-with-soonr-workplace"></a>Självstudie: Azure Active Directory integrering med arbets plats i tidigare

I den här självstudien får du lära dig hur du integrerar den tidigare arbets platsen med Azure Active Directory (Azure AD).
Att integrera snart arbets platsen med Azure AD ger följande fördelar:

* Du kan kontrol lera Azure AD som har åtkomst till arbets platsen i tidigare.
* Du kan göra det möjligt för användarna att vara automatiskt inloggade på arbets platsen för arbets platsen (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med arbets platsen i arbets platsen behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/)
* Aktive rad prenumeration för enkel inloggning på arbets plats

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Arbets platsen i tidigare har stöd för **SP-och IDP** -INITIERAd SSO

## <a name="adding-soonr-workplace-from-the-gallery"></a>Lägga till arbets plats i tidigare från galleriet

Om du vill konfigurera integreringen av arbets platsen i tidigare i Azure AD måste du lägga till arbets ytan i galleriet i listan över hanterade SaaS-appar.

**Gör så här för att lägga till en arbets plats från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program klickar du på knappen **nytt program** överst i dialog rutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **arbets plats i arbets plats**, väljer **arbets plats** i arbets ytan i resultat panelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

    ![Arbets plats i tidigare i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa enkel inloggning med Azure AD med hjälp av en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i arbets platsen närmast etableras.

Om du vill konfigurera och testa enkel inloggning med Azure AD med arbets platsen i tidigare måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning på arbets platsen](#configure-soonr-workplace-single-sign-on)** för att konfigurera de enkla inloggnings inställningarna på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa test användare på arbets platsen](#create-soonr-workplace-test-user)** för att få en motsvarighet till Britta Simon på arbets platsen i arbets platsen som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med Azure AD med arbets platsen i tidigare arbets plats:

1. På sidan [Azure Portal](https://portal.azure.com/)väljer du **enkel inloggning**på sidan program integration i **tidigare arbets plats** .

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, utför följande steg:

    ![Information om enkel inloggning för arbets plats domän och URL: er](common/idp-intiated.png)

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://<servername>.soonr.com/singlesignon/saml/metadata`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<servername>.soonr.com/singlesignon/saml/SSO`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![Information om enkel inloggning för arbets plats domän och URL: er](common/metadata-upload-additional-signon.png)

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<servername>.soonr.com/singlesignon/saml/SSO`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [arbets platsens klient support team](https://awp.autotask.net/help/) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

7. På sidan **Konfigurera tidigare arbets plats** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-soonr-workplace-single-sign-on"></a>Konfigurera enkel inloggning för arbets platsens arbets plats

Om du vill konfigurera enkel inloggning på sidan för tidig **arbets plats** måste du skicka den hämtade **XML-metadata för federationsmetadata** och lämpliga kopierade url: er från Azure Portal till [support teamet för tidigare arbets plats](https://awp.autotask.net/help/). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

> [!Note]
> Om du behöver hjälp med att konfigurera Autotask Workplace läser du [den här sidan](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) för att få hjälp med ditt Workplace-konto.

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

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till arbets platsen i tidigare.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **arbets plats i tidigare**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **arbets plats i tidigare**.

    ![Länken för den tidigare arbets platsen i listan program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-soonr-workplace-test-user"></a>Skapa test användare på arbets platsen i tidigare arbets plats

I det här avsnittet skapar du en användare som heter Britta Simon på arbets platsen i tidigare. Arbeta med [support teamet för närmast arbets plats](https://awp.autotask.net/help/) för att lägga till användare i den tidigare arbets platsens plattform. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen arbets plats i arbets ytan i åtkomst panelen, bör du loggas in automatiskt till den tidigare arbets ytan där du ställer in SSO. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

