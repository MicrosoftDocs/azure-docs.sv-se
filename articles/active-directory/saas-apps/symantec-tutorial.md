---
title: 'Självstudie: Azure Active Directory integrering med Symantec Web Security Service (WSS) | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Symantec Web Security Service (WSS).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/25/2018
ms.author: jeedes
ms.openlocfilehash: 7e2087fdd5568156a96645b25cb105cc5f3ca6b7
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88544952"
---
# <a name="tutorial-azure-active-directory-integration-with-symantec-web-security-service-wss"></a>Självstudie: Azure Active Directory integrering med Symantec Web Security Service (WSS)

I den här självstudien får du lära dig hur du integrerar ditt Symantec Web Security Service-konto (WSS) med ditt Azure Active Directory-konto (Azure AD) så att WSS kan autentisera en slutanvändare som har etablerats i Azure AD med hjälp av SAML-autentisering och tillämpa principregler på användar- eller gruppnivå.

Integreringen av Symantec Web Security Service (WSS) med Azure AD medför följande fördelar:

- Hantera alla slutanvändare och grupper som används av WSS-kontot från Azure AD-portalen.

- Låt slutanvändarna autentisera sig själva i WSS med sina autentiseringsuppgifter för Azure AD.

- Tillämpa de principregler på användar- och gruppnivå som definierats i ditt WSS-konto.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Symantec Web Security Service (WSS) behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* En Symantec Web Security Service-prenumeration (WSS) med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Symantec Web Security Service (WSS) stöder **IDP**-initierad enkel inloggning

## <a name="adding-symantec-web-security-service-wss-from-the-gallery"></a>Lägga till Symantec Web Security Service (WSS) från galleriet

För att konfigurera integreringen av Symantec Web Security Service (WSS) med Azure AD måste du lägga till Symantec Web Security Service (WSS) från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Symantec Web Security Service (WSS) från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Symantec Web Security Service (WSS)**, väljer **Symantec Web Security Service (WSS)** från resultatpanelen och klickar på **Lägg till** för att lägga till programmet.

     ![Symantec Web Security Service (WSS) i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa enkel inloggning i Azure AD med Symantec Web Security Service (WSS) baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Symantec Web Security Service (WSS) upprättas.

För att konfigurera och testa enkel inloggning i Azure AD med Symantec Web Security Service (WSS) måste du utföra följande uppgifter:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **Konfigurera enkel inloggning för Symantec Web Security Service (WSS)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa testanvändare för Symantec Web Security Service (WSS)](#create-symantec-web-security-service-wss-test-user)** – för att skapa en motsvarighet till Britta Simon i Symantec Web Security Service (WSS) som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning i Azure AD med Symantec Web Security Service (WSS):

1. Välj **Enkel inloggning** på sidan för programintegrering med **Symantec Web Security Service (WSS)** på [Azure-portalen](https://portal.azure.com/).

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I dialogrutan **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning med Symantec Web Security Service-domäner (WSS) och -URL:er](common/idp-intiated.png)

    a. Skriv en URL i text rutan **identifierare** : `https://saml.threatpulse.net:8443/saml/saml_realm`

    b. Skriv en URL i textrutan **Svars-URL**: `https://saml.threatpulse.net:8443/saml/saml_realm/bcsamlpost`

    > [!NOTE]
    > Kontakta [Symantec Web Security Service-kundsupporten ](https://www.symantec.com/contact-us) om värdena för **Identifierare** och **Svars-URL** inte fungerar. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

### <a name="configure-symantec-web-security-service-wss-single-sign-on"></a>Konfigurera enkel inloggning för Symantec Web Security Service (WSS)

Information om hur du konfigurerar enkel inloggning på Symantec Web Security Service-sidan finns i onlinedokumentationen för WSS. Den nedladdade **XML:en med federationsmetadata** måste importeras till WSS-portalen. Kontakta [Symantec Web Security Service-supportteamet (WSS)](https://www.symantec.com/contact-us) om du behöver hjälp med konfigurationen på WSS-portalen.

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

I det här avsnittet ska du göra det möjligt för Britta Simon att använda enkel inloggning i Azure genom att ge åtkomst till Symantec Web Security Service (WSS).

1. På Azure-portalen väljer du **Företagsprogram**, väljer **Alla program** och väljer sedan **Symantec Web Security Service (WSS)**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Skriv och välj **Symantec Web Security Service (WSS)** i programlistan.

    ![Symantec Web Security Service-länken (WSS) i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-symantec-web-security-service-wss-test-user"></a>Skapa Symantec Web Security Service-testanvändare (WSS)

I det här avsnittet ska du skapa en användare med namnet Britta Simon i Symantec Web Security Service (WSS). Det associerade slutanvändarnamnet kan skapas manuellt på WSS-portalen eller så kan du vänta tills användarna/grupperna som har etablerats i Azure AD synkroniseras med WSS-portalen efter några minuter (cirka 15 minuter). Användare måste skapas och aktiveras innan du använder enkel inloggning. Slutanvändardatorns offentliga IP-adress, som kommer att användas för att navigera på webbplatser, måste också etableras på Symantec Web Security Service-portalen (WSS).

> [!NOTE]
> Klicka [här](https://www.bing.com/search?q=my+ip+address&qs=AS&pq=my+ip+a&sc=8-7&cvid=29A720C95C78488CA3F9A6BA0B3F98C5&FORM=QBLH&sp=1) för att hämta datorns offentliga IP-adress.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska du testa funktionen för enkel inloggning nu när du har konfigurerat ditt WSS-konto så att det använder Azure AD för SAML-autentisering.

När du har konfigurerat din webbläsare för proxy-trafik till WSS omdirigeras du till Azure-inloggningssidan när du öppnar webbläsaren och försöker navigera till en plats. Ange autentiseringsuppgifterna för testanvändaren som har etablerats i Azure AD (det vill säga BrittaSimon) och tillhörande lösenord. När autentiseringen är klar kommer du att kunna navigera till webbplatsen som du valde. Om du skapar en principregel på WSS-sidan för att blockera Britta Simon så att hon inte kan navigera till en särskild webbplats, så visas WSS-blockeringssidan när du försöker navigera till den webbplatsen som användaren Britta Simon.

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

