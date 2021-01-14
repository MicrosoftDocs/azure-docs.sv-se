---
title: 'Självstudie: Azure Active Directory integrering med Marketo | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Marketo.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/13/2021
ms.author: jeedes
ms.openlocfilehash: d4f8e71a0afbb1b00313d5e100ba8b3fd2b4e2ce
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98185758"
---
# <a name="tutorial-azure-active-directory-integration-with-marketo"></a>Självstudie: Azure Active Directory integrering med Marketo

I den här självstudien får du lära dig hur du integrerar Marketo med Azure Active Directory (Azure AD).
Att integrera Marketo med Azure AD ger följande fördelar:

* Du kan kontrol lera Azure AD som har till gång till Marketo.
* Du kan göra det möjligt för användarna att logga in automatiskt till Marketo (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med Marketo behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Aktive rad prenumeration för enkel inloggning med Marketo

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Marketo stöder **IDP** INITIERAd SSO

> [!NOTE]
> ID för det här programmet är ett fast sträng värde så att endast en instans kan konfigureras i en klient.

## <a name="adding-marketo-from-the-gallery"></a>Lägga till Marketo från galleriet

Om du vill konfigurera integrering av Marketo i Azure AD måste du lägga till Marketo från galleriet i listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. Skriv **Marketo** i sökrutan i avsnittet **Lägg till från galleriet** .
1. Välj **Marketo** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-marketo"></a>Konfigurera och testa Azure AD SSO för Marketo

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med Marketo baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i Marketo upprättas.

Utför följande steg för att konfigurera och testa enkel inloggning med Azure AD med Marketo:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa Azure AD SSO med Britta Simon.
    1. **[Tilldela Azure AD](#assign-the-azure-ad-test-user)** -Britta för att aktivera Simon för att använda Azure AD SSO.
2. **[Konfigurera MARKETO SSO](#configure-marketo-sso)** – för att konfigurera SSO-inställningar på program sidan.
    1. **[Skapa ett Marketo-test](#create-marketo-test-user)** för att få en motsvarighet till Britta Simon i Marketo som är länkad till Azure AD-representation av användare.
3. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal på sidan **Marketo** -programintegration hittar du avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    a. Skriv webb adressen i text rutan **identifierare** : `https://saml.marketo.com/sp`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://login.marketo.com/saml/assertion/\<munchkinid\>`

    c. Skriv en URL med följande mönster i textrutan **Vidarebefordransstatus**: `https://<munchkinid>.marketo.com/`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska svars-URL: en och det aktuella relä läget. Kontakta [Marketo-klientens support team](https://investors.marketo.com/contactus.cfm) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **Konfigurera Marketo** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare** och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Ett exempel är `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Marketo.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **Marketo**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-marketo-sso"></a>Konfigurera Marketo SSO

1. Om du vill automatisera konfigurationen i Marketo måste du installera **webb läsar tillägget Mina appar säkra inloggnings webbläsare** genom att klicka på **installera tillägget**.

    ![Mina Apps-tillägg](common/install-myappssecure-extension.png)

2. När du har lagt till tillägg i webbläsaren kan du klicka på **Konfigurera Marketo** för att dirigera dig till Marketo-programmet. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på Marketo. Webb läsar tillägget kommer automatiskt att konfigurera programmet åt dig och automatisera steg 3-6.

    ![Konfigurera konfiguration](common/setup-sso.png)

3. Om du vill konfigurera Marketo manuellt i ett annat webbläsarfönster loggar du in på din Marketo-företags webbplats som administratör.

1. Utför följande åtgärder för att hämta Munchkin-ID: t för ditt program:
   
    a. Logga in på Marketo-appen med administratörs behörighet.
   
    b. Klicka på knappen **administratör** i det övre navigerings fönstret.
   
    ![Konfigurera enskilda Sign-On1](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Gå till integrations menyn och klicka på **länken Munchkin**.
   
    ![Konfigurera enskilda Sign-On2](./media/marketo-tutorial/tutorial_marketo_11.png)
   
    d. Kopiera Munchkin-ID: t som visas på skärmen och slutför svars-URL: en i konfigurations guiden för Azure AD.
   
    ![Konfigurera enskilda Sign-On3](./media/marketo-tutorial/tutorial_marketo_12.png) 

2. Följ stegen nedan om du vill konfigurera SSO i programmet:
   
    a. Logga in på Marketo-appen med administratörs behörighet.
   
    b. Klicka på knappen **administratör** i det övre navigerings fönstret.
   
    ![Konfigurera enskilda Sign-On4](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Gå till integrations menyn och klicka på **enkel inloggning**.
   
    ![Konfigurera enskilda Sign-On5](./media/marketo-tutorial/tutorial_marketo_07.png) 
   
    d. Om du vill aktivera SAML-inställningarna klickar du på knappen **Redigera** .
   
    ![Konfigurera enskilda Sign-On6](./media/marketo-tutorial/tutorial_marketo_08.png) 
   
    e. **Aktive rad** Inställningar för enskilda Sign-On.
   
    f. Klistra in **Azure AD-identifieraren** i text rutan **utfärdar-ID** .
   
    ex. Ange URL: en som i text rutan **entitets-ID** `http://saml.marketo.com/sp` .
   
    h. Välj elementet användar-ID plats som **namn identifierare**.
   
    ![Konfigurera enskilda Sign-On7](./media/marketo-tutorial/tutorial_marketo_09.png)
   
    > [!NOTE]
    > Om användar identifieraren inte är UPN-värde ändrar du värdet i fliken attribut.
   
    i. Ladda upp certifikatet, som du har laddat ned från konfigurations guiden för Azure AD. **Spara** inställningarna.
   
    j. Redigera inställningarna för omdirigera sidor.
   
    k. Klistra in **inloggnings webb adressen** i text rutan för **INLOGGNINGs-URL** : en.
   
    l. Klistra in **URL:** en för utloggning i text rutan för **utloggnings-URL** .
   
    m. I **fel-URL**: en kopierar du din webbplats för **Marketo-instansen** och klickar på knappen **Spara** för att spara inställningarna.
   
    ![Konfigurera enskilda Sign-On8](./media/marketo-tutorial/tutorial_marketo_10.png)

3. Om du vill aktivera SSO för användare utför du följande åtgärder:
   
    a. Logga in på Marketo-appen med administratörs behörighet.
   
    b. Klicka på knappen **administratör** i det övre navigerings fönstret.
   
    ![Konfigurera enskilda Sign-On9](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Gå till menyn **säkerhet** och klicka på **inloggnings inställningar**.
   
    ![Konfigurera enskilda Sign-On10](./media/marketo-tutorial/tutorial_marketo_13.png)
   
    d. Markera alternativet **KRÄV SSO** och **Spara** inställningarna.
   
    ![Konfigurera enskilda Sign-On11](./media/marketo-tutorial/tutorial_marketo_14.png)


### <a name="create-marketo-test-user"></a>Skapa användare av Marketo-test

I det här avsnittet skapar du en användare som heter Britta Simon i Marketo. Följ dessa steg om du vill skapa en användare i Marketo-plattform.

1. Logga in på Marketo-appen med administratörs behörighet.

2. Klicka på knappen **administratör** i det övre navigerings fönstret.
   
    ![testa Användare1](./media/marketo-tutorial/tutorial_marketo_06.png) 

3. Gå till menyn **säkerhet** och klicka på **användare & roller**
   
    ![testa användare2](./media/marketo-tutorial/tutorial_marketo_19.png)  

4. Klicka på länken **Bjud in ny användare** på fliken användare
   
    ![testa user3](./media/marketo-tutorial/tutorial_marketo_15.png) 

5. Fyll i följande information i guiden Bjud in ny användare
   
    a. Ange användarens **e-** postadress i text rutan
   
    ![testa user4](./media/marketo-tutorial/tutorial_marketo_16.png)
   
    b. Ange **förnamnet** i text rutan
   
    c. Ange **efter namnet**  i text rutan
   
    d. Klicka på **Nästa**

6. På fliken **behörigheter** väljer du **userRoles** och klickar på **Nästa**
   
    ![testa user5](./media/marketo-tutorial/tutorial_marketo_17.png)
7. Klicka på knappen **Skicka** för att skicka användar inbjudan
   
    ![testa user6](./media/marketo-tutorial/tutorial_marketo_18.png)

8. Användaren får e-postaviseringen och måste klicka på länken och ändra lösen ordet för att aktivera kontot. 

### <a name="test-sso"></a>Testa SSO

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ.

* Klicka på testa det här programmet i Azure Portal så bör du loggas in automatiskt på marknaden för vilken du konfigurerar SSO

* Du kan använda Microsoft Mina appar. När du klickar på panelen Marketo i Mina appar, bör du loggas in automatiskt på den Marketo som du konfigurerade SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Marketo kan du framtvinga kontroll över sessioner, vilket skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).