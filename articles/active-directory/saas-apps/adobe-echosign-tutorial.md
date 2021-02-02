---
title: 'Självstudie: Azure Active Directory integrering med Adobe Sign | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Adobe Sign.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/19/2021
ms.author: jeedes
ms.openlocfilehash: f8566386e73ab361f3078d5e6d27f92c51856afa
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99259459"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-sign"></a>Självstudie: Azure Active Directory integrering med Adobe Sign

I den här självstudien får du lära dig hur du integrerar Adobe Sign med Azure Active Directory (Azure AD). När du integrerar Adobe Sign med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Adobe Sign.
* Gör det möjligt för användarna att logga in automatiskt till Adobe-signera med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:
 
* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Adobe Sign-on (enkel inloggning) – aktive rad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Adobe Sign stöder **SP**-initierad enkel inloggning

## <a name="add-adobe-sign-from-the-gallery"></a>Lägg till Adobe Sign från galleriet

För att konfigurera integreringen av Adobe Sign med Azure AD måste du lägga till SAML SSO for Adobe Sign från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du **Adobe Sign** i sökrutan.
1. Välj **Adobe Sign** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-adobe-sign"></a>Konfigurera och testa Azure AD SSO för Adobe-tecken

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med Adobe Sign baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Adobe Sign upprättas.

För att konfigurera och testa enkel inloggning med Azure AD med Adobe Sign måste du utföra följande steg:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
1. **[Konfigurera Adobe Sign SSO](#configure-adobe-sign-sso)** – om du vill konfigurera enskilda Sign-On inställningar på program sidan.
    1. **[Skapa Adobe Sign-testanvändare](#create-adobe-sign-test-user)** – för att ha en motsvarighet för Britta Simon i Adobe Sign som är länkad till en Azure AD-representation av användaren.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning i Azure AD med Adobe Sign:

1. På sidan för **Adobe Sign** -programintegration i Azure Portal väljer du **enkel inloggning**.

1. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

1. På sidan **Konfigurera en enskild Sign-On med SAML** klickar du på Penn ikonen för att öppna dialog rutan **grundläggande SAML-konfiguration** .

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<companyname>.echosign.com/`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<companyname>.echosign.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [kundsupporten för Adobe Sign](https://helpx.adobe.com/in/contact/support.html) och be om dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **Konfigurera Adobe Sign** kopierar du lämpliga URL:er enligt dina behov.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Adobe Sign.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **Adobe Sign**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

### <a name="configure-adobe-sign-sso"></a>Konfigurera Adobe Sign SSO

1. Innan du konfigurerar konfigurationen kan du kontakta [support teamet för Adobe Sign client](https://helpx.adobe.com/in/contact/support.html) för att lägga till din domän i listan med Adobe-signerade tillåtna. Så här lägger du till domänen:

    a. [Supportteamet för Adobe Sign](https://helpx.adobe.com/in/contact/support.html) skickar dig en slumpmässigt genererad token. För din domän kommer token se ut ungefär på följande sätt: **adobe-sign-verification= xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx**

    b. Publicera verifieringstoken i en DNS-textpost och meddela [supportteamet för Adobe Sign](https://helpx.adobe.com/in/contact/support.html).

    > [!NOTE]
    > Det kan ta några dagar eller längre. Observera att DNS-spridningsfördröjningar innebär att ett värde som publiceras i DNS kanske inte visas förrän efter en timme eller mer. Din IT-administratör bör känna till hur denna token publiceras i en DNS-textpost.

    c. När du meddelar [supportteamet för Adobe Sign](https://helpx.adobe.com/in/contact/support.html) via supportbegäran validerar de domänen och lägger till den i ditt konto efter att token har publicerats.

    d. Så här publicerar du generellt token i en DNS-post:

    * Logga in på ditt domänkonto
    * Leta reda på sidan för att uppdatera DNS-posten. Den här sidan heter kanske DNS Management (DNS-hantering), Name Server Management (Namnserverhantering) eller Advanced Settings (Avancerade inställningar).
    * Hitta TXT-posterna för din domän.
    * Lägg till en TXT-post med det fullständiga tokenvärde som tillhandahålls av Adobe.
    * Spara ändringarna.

1. Öppna ett nytt webbläsarfönster och logga in på din Adobe Sign-företagswebbplats som administratör.

1. I SAML-menyn väljer du **konto inställningar**  >  **SAML-inställningar**.

    ![Skärm bild av sidan Adobe signera SAML-inställningar](./media/adobe-echosign-tutorial/settings.png "Konto")

1. I avsnittet **SAML-inställningar** utför du följande steg:

    ![Skärm bild som visar SAML-inställningarna, inklusive SAML obligatoriskt.](./media/adobe-echosign-tutorial/saml1.png "SAML-inställningar")

   ![Skärm bild av SAML-inställningar](./media/adobe-echosign-tutorial/saml.png "SAML-inställningar")

   a. Under **SAML Mode** (SAML-läge) väljer du **SAML Mandatory** (SAML är obligatoriskt).

   b. Välj **Allow Echosign Account Administrators to log in using their Echosign Credentials** (Tillåt Echosign-kontoadministratörer att logga in med sina Echosign-autentiseringsuppgifter).

   c. Under **User Creation** (Skapande av användare) väljer du **Automatically add users authenticated through SAML** (Lägg automatiskt till användare som autentiseras via SAML).

   d. Klistra in den **Azure AD-identifierare** som du har kopierat från Azure-portalen i textrutan **IdP-entitets-ID**.

   e. Klistra in den **inloggnings-URL** som du har kopierat från Azure-portalen i textrutan **IdP-inloggnings-URL**.

   f. Klistra in den **utloggnings-URL** som du har kopierat från Azure-portalen i textrutan **IdP-utloggnings-URL**.

   ex. Öppna den nedladdade **Certificate(Base64)**-filen i Anteckningar. Kopiera innehållet i den till Urklipp och klistra in det till textrutan **IdP-certifikat**.

   h. Välj **Spara ändringar**.

### <a name="create-adobe-sign-test-user"></a>Skapa Adobe Sign-testanvändare

För att Azure AD-användare ska kunna logga in på Adobe Sign måste de etableras till Adobe Sign. Det här är en manuell uppgift.

>[!NOTE]
>Du kan använda andra verktyg eller API:er för skapande av Adobe Sign-konton som tillhandahålls av Adobe Sign för att etablera Azure AD-användarkonton. 

1. Logga in på din **Adobe Sign**-företagsplats som administratör.

2. I menyn längst upp väljer du **Konto**. I den vänstra rutan väljer **du sedan användare & grupper**  >  **skapa en ny användare**.

    ![Skärm bild av Adobe Sign Company-webbplatsen med konto, användare &grupper och skapa en ny användare markerad](./media/adobe-echosign-tutorial/account.png "Konto")

3. I avsnittet **Skapa ny användare** utför du följande steg:

    ![Skärm bild av avsnittet Skapa ny användare](./media/adobe-echosign-tutorial/user.png "Skapa användare")

    a. Skriv **e-postadress**, **förnamn** och **efternamn** för ett giltigt Azure AD-konto som du vill etablera i respektive textrutor.

    b. Välj **Skapa användare**.

>[!NOTE]
>Azure Active Directory-kontoinnehavaren får ett e-postmeddelande med en länk för att bekräfta kontot innan det blir aktivt. 

### <a name="test-sso"></a>Testa SSO

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till Adobe-inloggnings-URL: en där du kan starta inloggnings flödet. 

* Gå till Adobe-inloggnings-URL: en direkt och starta inloggnings flödet därifrån.

* Du kan använda Microsoft Mina appar. När du klickar på panelen Adobe-inloggning i Mina appar, bör du loggas in automatiskt på det Adobe-tecken som du har konfigurerat SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Adobe Sign kan du framtvinga kontroll av sessioner, vilket skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
