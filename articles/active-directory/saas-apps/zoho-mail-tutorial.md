---
title: 'Självstudie: Azure Active Directory integrering med Zoho | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Zoho.
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
ms.openlocfilehash: c5778f39a5091753a1658ec121379a4ed29a7542
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101648381"
---
# <a name="tutorial-azure-active-directory-integration-with-zoho"></a>Självstudie: Azure Active Directory integrering med Zoho

I den här självstudien får du lära dig hur du integrerar Zoho med Azure Active Directory (Azure AD). När du integrerar Zoho med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Zoho.
* Gör det möjligt för användarna att logga in automatiskt till Zoho med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Zoho ett behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Zoho-aktiverad prenumeration med enkel inloggning.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Zoho har stöd för **SP**-initierad enkel inloggning

## <a name="add-zoho-from-the-gallery"></a>Lägg till Zoho från galleriet

För att kunna konfigurera integreringen av Zoho i Azure AD måste du lägga till Zoho från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **Zoho** i sökrutan.
1. Välj **Zoho** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-zoho"></a>Konfigurera och testa Azure AD SSO för Zoho

Konfigurera och testa Azure AD SSO med Zoho med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Zoho.

Utför följande steg för att konfigurera och testa Azure AD SSO med Zoho:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera Zoho SSO](#configure-zoho-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa Zoho test User](#create-zoho-test-user)** -om du vill ha en motsvarighet till B. Simon i Zoho som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal går du till sidan för program integrering i **Zoho** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<company name>.zohomail.com`

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [Zoho-supporten](https://www.zoho.com/mail/contact.html) för att få värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **Konfigurera Zoho** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user&quot;></a>Skapa en Azure AD-testanvändare 

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare** och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name=&quot;assign-the-azure-ad-test-user&quot;></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Zoho.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **Zoho**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen &quot;standard åtkomst&quot; vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

### <a name=&quot;configure-zoho-sso&quot;></a>Konfigurera Zoho SSO

1. Logga in på din Zoho Mail-företagswebbplats som administratör.

2. Gå till **Kontrollpanelen**.
   
    ![Kontrollpanelen](./media/zoho-mail-tutorial/control-panel.png &quot;Kontrollpanelen")

3. Klicka på fliken **SAML-autentisering**.
   
    ![SAML-autentisering](./media/zoho-mail-tutorial/saml-authentication.png "SAML-autentisering")

4. Gör följande i avsnittet **SAML-autentiseringsinformation**:
   
    ![Information om SAML-autentisering](./media/zoho-mail-tutorial/details.png "Information om SAML-autentisering")
   
    a. I textrutan för **inloggnings-URL** klistrar du in **inloggnings-URL:en** som du har kopierat från Azure-portalen.
   
    b. I textrutan **Logout URL** (Utloggnings-URL) klistrar du in **inloggnings-URL:en** som du har kopierat från Azure-portalen.
   
    c. I textrutan **Change Password URL** (URL för Byt lösenord) klistrar du in **URL:en för Byt lösenord** som du har kopierat från Azure-portalen.
       
    d. Öppna ditt base-64-kodade certifikat som du har laddat ned från Azure-portalen i Anteckningar, kopierar innehållet i Urklipp och klistra sedan in den i textrutan **PublicKey**.
   
    e. Som **Algoritm** väljer du **RSA**.
   
    f. Klicka på **OK**.

### <a name="create-zoho-test-user"></a>Skapa Zoho-testanvändare

För att kunna göra det möjligt för Azure AD-användare att logga in på Zoho Mail måste de etableras i Zoho Mail. När det gäller Zoho Mail är etablering en manuell uppgift.

> [!NOTE]
> Du kan använda andra Zoho e-postverktyg eller API: er som tillhandahålls av Zoho-e-post för att etablera Azure AD-användarkonton.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Gör följande för att etablera ett användarkonto:

1. Logga in på din **Zoho Mail**-företagswebbplats som administratör.

1. Gå till **Kontrollpanelen \> E-post och dokument**.

1. Gå till **Användarinformation \> Lägg till användare**.
   
    ![Skärm bild som visar Zoho-e-postwebbplatsen med användar information och Lägg till användare vald.](./media/zoho-mail-tutorial/add-user-1.png "Lägg till användare")

1. Gör följande i dialogrutan **Lägg till användare**:
   
    ![Skärm bild som visar dialog rutan Lägg till användare där du kan ange de värden som beskrivs.](./media/zoho-mail-tutorial/add-user-2.png "Lägg till användare")
   
    a. I textrutan **Förnamn** skriver du förnamnet på användaren: **Britta**.

    b. I textrutan **Efternamn** skriver du efternamnet: **Simon**.

    c. I text rutan med **e-post-ID** skriver du e-post-ID för användaren som **brittasimon \@ contoso.com**.

    d. I textrutan **Lösenord** anger du användarens lösenord.
   
    e. Klicka på **OK**.  
      
    > [!NOTE]
    > Azure Active Directory-kontoinnehavaren får ett e-postmeddelande med en länk för att bekräfta kontot innan det blir aktivt.

### <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till Zoho-inloggnings-URL där du kan starta inloggnings flödet. 

* Gå till Zoho-inloggnings-URL: en direkt och starta inloggnings flödet därifrån.

* Du kan använda Microsoft Mina appar. När du klickar på panelen Zoho i Mina appar, bör du loggas in automatiskt på den Zoho som du ställer in SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Zoho kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).