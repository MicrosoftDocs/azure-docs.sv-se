---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med Google Cloud (G Suite)-anslutning | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Google Cloud (G Suite) Connector.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/11/2021
ms.author: jeedes
ms.openlocfilehash: a846899ba8f9b9e7c0d2e54744f5e5044ca7a2d6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98732134"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-google-cloud-g-suite-connector"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med Google Cloud (G Suite)-anslutning

I den här självstudien får du lära dig hur du integrerar Google Cloud (G Suite) Connector med Azure Active Directory (Azure AD). När du integrerar Google Cloud-anslutningen (G Suite) med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Google Cloud (G Suite)-anslutning.
* Gör det möjligt för användarna att logga in automatiskt till Google Cloud (G Suite)-anslutning med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

- En Azure AD-prenumeration.
- Google Cloud (G Suite) anslutnings tjänsten för enkel inloggning (SSO) aktive rad.
- En Google Apps-prenumeration eller Google Cloud Platform-prenumeration.

> [!NOTE]
> Vi rekommenderar att du inte använder en produktionsmiljö för att testa stegen i den här självstudien. Det här dokumentet har skapats med den nya användarupplevelsen Enkel inloggning. Om du fortfarande använder den gamla upplevelsen ser installationen annorlunda ut. Du kan aktivera den nya upplevelsen i inställningarna för enkel inloggning i G Suite-programmet. Gå till **Azure AD, företags program**, Välj **Google Cloud (G Suite) Connector**, Välj **enkel inloggning** och klicka sedan på **prova vår nya upplevelse**.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

1. **F: stöder denna integrering Google Cloud Platform SSO-integrering med Azure AD?**

    S: Ja. Google Cloud Platform och Google Apps delar samma autentiseringsplattform. Så om du vill göra GCP-integreringen måste du konfigurera enkel inloggning med Google Apps.

2. **F: är Chromebooks och andra Chrome-enheter kompatibla med enkel inloggning med Azure AD?**
  
    A: Ja, användarna kan logga in på sina Chromebook-enheter med sina autentiseringsuppgifter för Azure AD. I den här [artikeln om support för Google Cloud (G Suite)](https://support.google.com/chrome/a/answer/6060880) kan du läsa mer om varför användare kan uppmanas att ange autentiseringsuppgifter två gånger.

3. **F: om jag aktiverar enkel inloggning kommer användarna att kunna använda sina Azure AD-autentiseringsuppgifter för att logga in på en Google-produkt, till exempel Google Classroom, GMail, Google Drive, YouTube och så vidare?**

    S: Ja, beroende på [vilken Google Cloud (G Suite) anslutning](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583) du väljer att aktivera eller inaktivera för din organisation.

4. **F: kan jag aktivera enkel inloggning för endast en delmängd av mina Google Cloud (G Suite) Connector-användare?**

    S: Nej, om du aktiverar enkel inloggning kräver omedelbart alla dina Google Cloud (G Suite)-anslutnings användare att autentisera med sina autentiseringsuppgifter för Azure AD. Eftersom Google Cloud (G Suite) Connector inte stöder flera identitets leverantörer, kan identitets leverantören för din Google Cloud (G Suite) anslutnings miljö antingen vara Azure AD eller Google, men inte båda samtidigt.

5. **F: om en användare är inloggad via Windows, autentiseras de automatiskt till Google Cloud (G Suite)-anslutning utan att du behöver ange ett lösen ord?**

    A: det finns två alternativ för att aktivera det här scenariot. Antingen kan användare logga in på Windows 10-enheter via [Azure Active Directory Join](../devices/overview.md). Alternativt kan användare logga in på Windows-enheter som är domänanslutna till en lokal Active Directory som har aktiverats för enkel inloggning till Azure AD via en [Active Directory Federation Services](../hybrid/plan-connect-user-signin.md)-distribution (AD FS). Båda alternativen kräver att du utför stegen i följande självstudie för att aktivera enkel inloggning mellan Azure AD och Google Cloud (G Suite) Connector.

6. **F: Vad gör jag om jag får fel meddelandet "Ogiltigt e-postmeddelande"?**

    A: för den här installationen krävs e-postattributet för att användarna ska kunna logga in. Det här attributet kan inte anges manuellt.

    E-postadressattribut fylls i automatiskt för alla användare med en giltig Exchange-licens. Om användaren inte är e-postaktiverad, får hen det här felet allteftersom programmet behöver få det här attributet för att ge åtkomst.

    Du kan gå till portal.office.com med ett administratörs konto. Klicka sedan på administrations Center, fakturering, prenumerationer, välj din Microsoft 365 prenumeration och klicka på tilldela till användare, Välj de användare som du vill kontrol lera deras prenumeration och klicka på Redigera licenser i den högra rutan.

    När Microsoft 365 licens har tilldelats kan det ta några minuter att tillämpa. Efter det kommer attributet user.mail att fyllas i automatiskt och problemet ska vara löst.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Google Cloud (G Suite) Connector stöder **SP** -INITIERAd SSO

* Google Cloud (G Suite) Connector stöder [ **Automatisk** användar etablering](./g-suite-provisioning-tutorial.md)

## <a name="adding-google-cloud-g-suite-connector-from-the-gallery"></a>Lägga till Google Cloud (G Suite)-koppling från galleriet

Om du vill konfigurera integreringen av Google Cloud (G Suite) Connector i Azure AD måste du lägga till Google Cloud (G Suite)-koppling från galleriet till listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du **Google Cloud (G Suite) Connector** i sökrutan.
1. Välj **Google Cloud (G Suite) koppling** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-google-cloud-g-suite-connector"></a>Konfigurera och testa enkel inloggning med Azure AD för Google Cloud (G Suite)-anslutning

Konfigurera och testa Azure AD SSO med Google Cloud (G Suite) Connector med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Google Cloud-anslutningen (G Suite).

Utför följande steg för att konfigurera och testa Azure AD SSO med Google Cloud (G Suite) Connector:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera Google Cloud (G Suite) Connector SSO](#configure-google-cloud-g-suite-connector-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa ett Google Cloud (g Suite) anslutnings test](#create-google-cloud-g-suite-connector-test-user)** för att få en motsvarighet till B. Simon i Google Cloud (G Suite) Connector som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal, på sidan för **kopplings program integration i Google Cloud (G Suite)** , letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg om du vill konfigurera för **Gmail**:

    a. I text rutan **inloggnings-URL** skriver du en URL med följande mönster: `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://mail.google.com`

    b. I textrutan **Identifierare** anger du en URL med följande mönster:

    ```http
    google.com/a/<yourdomain.com>
    google.com
    https://google.com
    https://google.com/a/<yourdomain.com>
    ```

    c. I textrutan **Svars-URL** skriver du en URL med följande mönster:  

    ```http
    https://www.google.com/acs
    https://www.google.com/a/<yourdomain.com>/acs
    ```

1. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg om du vill konfigurera för **Google Cloud Platform**:

    a. I text rutan **inloggnings-URL** skriver du en URL med följande mönster: `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://console.cloud.google.com`

    b. I textrutan **Identifierare** anger du en URL med följande mönster:
    
    ```http
    google.com/a/<yourdomain.com>
    google.com
    https://google.com
    https://google.com/a/<yourdomain.com>
    ```
    
    c. I textrutan **Svars-URL** skriver du en URL med följande mönster:  
    
    ```http
    https://www.google.com/acs
    https://www.google.com/a/<yourdomain.com>/acs
    ```

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Google Cloud-anslutningen (G Suite) tillhandahåller inte enhets-ID/identifierade värden för enkel inloggnings konfiguration så när du avmarkerar alternativet för **domänautentiseringsuppgifter** är identifieraren `google.com` . Om du markerar alternativet för **domänbaserad utfärdare** blir det `google.com/a/<yourdomainname.com>` . Om du vill kontrol lera/avmarkera alternativet för **domänanslutet utfärdare** måste du gå till avsnittet **Konfigurera anslutnings-SSO för Google Cloud (G Suite)** som beskrivs senare i självstudien. Om du vill ha mer information kan du kontakta [support teamet för Google Cloud (G Suite)](https://www.google.com/contact/).

1. Ditt Google Cloud (G Suite)-anslutnings program förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar till dina SAML token-konfiguration av attribut. Följande skärmbild visar ett exempel på detta. Standardvärdet för **unikt användar-ID** är **User. UserPrincipalName** men Google Cloud (G Suite) Connector förväntar sig att mappas till användarens e-postadress. Till det kan du använda **user.mail**-attributet från listan eller rätt attributvärde baserat på organisationens konfiguration.

    ![image](common/default-attributes.png)


1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera Google Cloud (G Suite) Connector** kopierar du lämpliga URL: er baserat på ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare** och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Google Cloud (G Suite) Connector.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **Google Cloud (G Suite) Connector**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-google-cloud-g-suite-connector-sso"></a>Konfigurera Google Cloud (G Suite) Connector SSO

1. Öppna en ny flik i webbläsaren och logga in på [Google Cloud (G Suite) Connector admin-konsolen](https://admin.google.com/) med ditt administratörs konto.

2. Klicka på **Säkerhet**. Om du inte ser länken, kan den vara dold under menyn **Fler kontroller** längst ned på skärmen.

    ![Klicka på Säkerhet.][10]

3. På sidan **Säkerhet** klickar du på **Konfigurera enkel inloggning (SSO).**

    ![Klicka på enkel inloggning.][11]

4. Utför följande konfigurationsändringar:

    ![Konfigurera enkel inloggning][12]

    a. Välj **Installera enkel inloggning med tredjeparts identitetsprovider**.

    b. I fältet **URL för inloggnings sidan** i Google Cloud (G Suite) Connector klistrar du in värdet för **inloggnings-URL: en** som du har kopierat från Azure Portal.

    c. I fältet **utloggnings sidans URL** i Google Cloud (G Suite) Connector klistrar du in värdet för **inloggnings-URL: en** som du har kopierat från Azure Portal.

    > [!NOTE]
    > Google Cloud (G Suite) baseras på SAML-utloggnings protokoll. I **URL-** fältet för utloggnings sidan måste du därför använda SAML-utloggnings-URL: en, d.v.s. inloggnings-URL: en som värde för samma.

    d. I Google Cloud (G Suite)-anslutning för **verifierings certifikatet** laddar du upp det certifikat som du har laddat ned från Azure Portal.   

    e. Markera/avmarkera alternativet **Använd ett domänbaserat Issuer** -alternativ enligt kommentaren ovan i avsnittet **grundläggande SAML-konfiguration** i Azure AD.

    f. I fältet **ändra lösen ordets URL** i Google Cloud (G Suite)-anslutning klistrar du in värdet för **ändra lösen ordets URL** som du har kopierat från Azure Portal.

    ex. Klicka på **Spara**.

### <a name="create-google-cloud-g-suite-connector-test-user"></a>Skapa Google Cloud (G Suite)-anslutnings test användare

Syftet med det här avsnittet är att [skapa en användare i Google Cloud (G Suite)-anslutning](https://support.google.com/a/answer/33310?hl=en) som kallas B. Simon. När användaren har skapats manuellt i Google Cloud (G Suite) Connector kommer användaren nu att kunna logga in med sina Microsoft 365 inloggnings uppgifter.

Google Cloud-anslutningen (G Suite) stöder även automatisk användar etablering. Om du vill konfigurera automatisk användar etablering måste du först [Konfigurera Google Cloud (G Suite) Connector för automatisk användar etablering](./g-suite-provisioning-tutorial.md).

> [!NOTE]
> Se till att användaren redan finns i Google Cloud (G Suite) Connector om etableringen i Azure AD inte har Aktiver ATS innan du testar enkel inloggning.

> [!NOTE]
> Om du vill skapa en användare manuellt kan du kontakta [Google-supportteamet](https://www.google.com/contact/).

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigerar till inloggnings-URL: en för Google Cloud (G Suite)-anslutnings tjänsten där du kan starta inloggnings flödet. 

* Gå till Google Cloud (G Suite) anslutnings inloggnings-URL: en direkt och starta inloggnings flödet därifrån.

* Du kan använda Microsoft Mina appar. När du klickar på anslutnings panelen för Google Cloud (G Suite) i Mina appar omdirigeras den till inloggnings-URL: en för Google Cloud (G Suite) Connector. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Google Cloud (G Suite)-anslutningen kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).

<!--Image references-->

[10]: ./media/google-apps-tutorial/gapps-security.png
[11]: ./media/google-apps-tutorial/security-gapps.png
[12]: ./media/google-apps-tutorial/gapps-sso-config.png
