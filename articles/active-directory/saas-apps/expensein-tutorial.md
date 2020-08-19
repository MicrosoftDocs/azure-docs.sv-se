---
title: 'Självstudie: Azure Active Directory integrering med kostnad Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och kostnad.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/17/2020
ms.author: jeedes
ms.openlocfilehash: 7d0b0a6c935182737a7f207b7c7831fefcd491ea
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88555345"
---
# <a name="tutorial-integrate-expensein-with-azure-active-directory"></a>Självstudie: integrera utgifter med Azure Active Directory

I den här självstudien får du lära dig hur du integrerar kostnad med Azure Active Directory (Azure AD). När du integrerar kostnads hantering med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till kostnad.
* Gör det möjligt för användarna att logga in automatiskt till utgifter med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Kostnad för enkel inloggning (SSO) aktive rad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö. 
* Kostnad har stöd för **SP-och IDP** -initierad SSO.
* När du har konfigurerat kostnad kan du framtvinga kontroll av sessioner, vilket skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


## <a name="adding-expensein-from-the-gallery"></a>Lägga till utgifter från galleriet

Om du vill konfigurera en integrering av utgifterna i Azure AD måste du lägga till utgifts hantering från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. Skriv **kostnadi** i sökrutan i avsnittet **Lägg till från galleriet** .
1. Välj **utgifts** hantering från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-expensein"></a>Konfigurera och testa Azure AD SSO för kostnad

Konfigurera och testa Azure AD SSO med kostnad med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och en relaterad användare i kostnad.

Om du vill konfigurera och testa Azure AD SSO med kostnad, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** för att testa enkel inloggning i Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** att aktivera B. Simon för att använda enkel inloggning i Azure AD.
1. **[Konfigurera kostnad för enkel inloggning](#configure-expensein-sso)** för att konfigurera SSO-inställningar på program sidan.
    1. **[Skapa utgifts-och test användare](#create-expensein-test-user)** för att få en motsvarighet till B. Simon i kostnad, som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för **Reseräkning** -programintegration och letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **grundläggande SAML-konfiguration** behöver användaren inte utföra några steg eftersom appen redan är Förintegrerad med Azure.

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    Skriv en URL i text rutan **inloggnings-URL** :  `https://app.expensein.com/saml`

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , klickar du på Kopiera för att kopiera URL för **metadata för app Federation** och klickar på **Hämta** för att ladda ned **certifikatet (base64)** och spara det på din dator.

   ![Länk för nedladdning av certifikatet](./media/expensein-tutorial/copy-metdataurl-certificate.png)

1. I avsnittet **Konfigurera utgifter** kopierar du lämpliga URL: er baserat på ditt krav.

   ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare**och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till kostnad.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **utgifter**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .


## <a name="configure-expensein-sso"></a>Konfigurera kostnad för SSO

1. Om du vill automatisera konfigurationen i kostnad måste du installera **webb läsar tillägget Mina appar med säker inloggning** genom att klicka på **installera tillägget**.

    ![Mina Apps-tillägg](common/install-myappssecure-extension.png)

1. När du har lagt till tillägg i webbläsaren, klickar du på **Konfigurera kostnadi** för att dirigera dig till programmet för kostnad Därifrån anger du administratörsautentiseringsuppgifter för att logga in på kostnad. Webb läsar tillägget kommer automatiskt att konfigurera programmet åt dig och automatisera steg 3-5.

    ![Konfigurera konfiguration](common/setup-sso.png)

1. Om du vill konfigurera kostnad manuellt loggar du in på din kostnadsbaserade företags plats som administratör.

1. Klicka på **administratör** överst på sidan och gå sedan till **enkel inloggning** och klicka på **Lägg till provider**.

     ![Reseräkning-konfiguration](./media/expenseIn-tutorial/config01.png)

1. Utför följande steg på popup-sidan för den **nya identitets leverantören** :

    ![Reseräkning-konfiguration](./media/expenseIn-tutorial/config02.png)

    a. Skriv namnet i text rutan **Providernamn** . till exempel Azure.

    b. Välj **Ja** för **Tillåt Provider Intitiated-inloggning**.

    c. I text rutan **mål-URL** , klistra in värdet för **inloggnings-URL**, som du har kopierat från Azure Portal.

    d. I text rutan **utfärdare** klistrar du in värdet för **Azure AD-identifierare**, som du har kopierat från Azure Portal.

    e. Öppna certifikatet (base64) i anteckningar, kopiera innehållet och klistra in det i text rutan **certifikat** .

    f. Klicka på **Skapa**.

### <a name="create-expensein-test-user"></a>Skapa kostnad för test användare

Om du vill att Azure AD-användare ska kunna logga in på utgifter måste de tillhandahållas i kostnad. I kostnad, är etableringen en manuell uppgift.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på kostnad, som administratör.

2. Klicka på **administratör** överst på sidan och navigera sedan till **användare** och klicka på **ny användare**.

     ![Reseräkning-konfiguration](./media/expenseIn-tutorial/config03.png)

3. Utför följande steg i popup-fönstret **information** :

    ![Reseräkning-konfiguration](./media/expenseIn-tutorial/config04.png)

    a. I text rutan **förnamn** anger du det första namnet på användaren, t. ex. **B**.

    b. I text rutan **efter namn** anger du det senaste namnet på användaren som **Simon**.

    c. I textrutan **E-post** anger du användarens e-postadress som `B.Simon@contoso.com`.

    d. Klicka på **Skapa**.

## <a name="test-sso"></a>Testa SSO

När du väljer fliken kostnads hantering i åtkomst panelen, bör du loggas in automatiskt på den kostnad som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova kostnad med Azure AD](https://aad.portal.azure.com/)

- [Vad är session Control i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Skydda utgifts hantering med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
