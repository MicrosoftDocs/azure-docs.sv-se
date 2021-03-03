---
title: 'Självstudie: Azure Active Directory integration med Zoho One | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Zoho.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/20/2021
ms.author: jeedes
ms.openlocfilehash: 12ac4d9fbf30873f0392a6d767d7568129bad112
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101650659"
---
# <a name="tutorial-azure-active-directory-integration-with-zoho-one"></a>Självstudie: Azure Active Directory integrering med Zoho One

I den här självstudien får du lära dig att integrera Zoho en med Azure Active Directory (Azure AD). När du integrerar Zoho en med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Zoho.
* Gör det möjligt för användarna att logga in automatiskt för att Zoho en med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Zoho ett behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Zoho en aktive rad prenumeration med enkel inloggning.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Zoho One stöder **SP** -och **IDP** -initierad SSO

> [!NOTE]
> ID för det här programmet är ett fast sträng värde så att endast en instans kan konfigureras i en klient.

## <a name="add-zoho-one-from-the-gallery"></a>Lägg till Zoho från galleriet

Om du vill konfigurera integreringen av Zoho i Azure AD måste du lägga till Zoho från galleriet i listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **Zoho ett** i sökrutan.
1. Välj **Zoho en** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-zoho-one"></a>Konfigurera och testa Azure AD SSO för Zoho One

Konfigurera och testa Azure AD SSO med Zoho med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och en relaterad användare i Zoho.

Utför följande steg för att konfigurera och testa Azure AD SSO med Zoho en:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera Zoho en SSO](#configure-zoho-one-sso)** -för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa Zoho en test användare](#create-zoho-one-test-user)** – för att få en motsvarighet till B. Simon i Zoho som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal på sidan **Zoho One** Application Integration letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, utför följande steg:

    a. Skriv webb adressen i text rutan **identifierare** : `one.zoho.com`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://accounts.zoho.com/samlresponse/<saml-identifier>`

    > [!NOTE]
    > Föregående **svars-URL** -värde är inte verkligt. Du får `<saml-identifier>` värdet från #step4 av **Konfigurera Zoho ett enda inloggnings** avsnitt, som beskrivs senare i självstudien.

    c. Klicka på **Ange ytterligare URL:er**.

    d. I text rutan **relä tillstånd** anger du URL: en: `https://one.zoho.com`

5. Utför följande steg om du vill konfigurera programmet i **SP** initierat läge:

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://accounts.zoho.com/samlauthrequest/<domain_name>?serviceurl=https://one.zoho.com` 

    > [!NOTE] 
    > Föregående **URL-** värde för inloggning är inte verkligt. Du uppdaterar värdet med den faktiska Sign-On-URL: en i avsnittet **Konfigurera Zoho ett enda inloggning** , som beskrivs senare i självstudien. 

6. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

7. På sidan **Konfigurera Zoho** , kopierar du lämpliga URL: er enligt ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Zoho One.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **Zoho ett**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

### <a name="configure-zoho-one-sso"></a>Konfigurera Zoho en SSO

1. Logga in på din Zoho-företags webbplats som administratör i ett annat webbläsarfönster.

2. Klicka på **Konfigurera** under **SAML-autentisering** på fliken **organisation** .

    ![Zoho en organisation](./media/zoho-one-tutorial/set-up.png)

3. På popup-sidan utför du följande steg:

    ![Zoho en sig](./media/zoho-one-tutorial/save.png)

    a. I text rutan **inloggnings-URL** klistrar du in värdet för **inloggnings-URL: en** som du har kopierat från Azure Portal.

    b. I text rutan för utloggnings **-URL** klistrar du in värdet för **utloggnings-URL**, som du har kopierat från Azure Portal.

    c. Klicka på **Bläddra** för att ladda upp det **certifikat (base64)** som du har laddat ned från Azure Portal.

    d. Klicka på **Spara**.

4. När du har sparat inställningen för SAML-autentisering kopierar du värdet för **SAML-identifieraren** och lägger till det med **svars-URL:** en i stället för `<saml-identifier>` `https://accounts.zoho.com/samlresponse/one.zoho.com` och klistrar in det genererade värdet i text rutan **SVARs-URL** under avsnittet **grundläggande SAML-konfiguration** .

    ![Zoho ett SAML](./media/zoho-one-tutorial/saml-identifier.png)

5. Gå till fliken **domäner** och klicka sedan på **Lägg till domän**.

    ![Zoho en domän](./media/zoho-one-tutorial/add-domain.png)

6. På sidan **Lägg till domän** utför du följande steg:

    ![Zoho en Lägg till domän](./media/zoho-one-tutorial/add-domain-name.png)

    a. I text rutan **domän namn** skriver du domän som contoso.com.

    b. Klicka på **Lägg till**.

    >[!Note]
    >När du har lagt till domänen följer du [de här](https://www.zoho.com/one/help/admin-guide/domain-verification.html) stegen för att verifiera din domän. När domänen har verifierats kan du använda ditt domän namn i **inloggnings-URL: en** i avsnittet **grundläggande SAML-konfiguration** i Azure Portal.

### <a name="create-zoho-one-test-user"></a>Skapa Zoho en test användare

Om du vill att Azure AD-användare ska kunna logga in på Zoho ett måste de vara etablerade i Zoho One. I Zoho One är etableringen en manuell uppgift.

**Gör följande för att etablera ett användarkonto:**

1. Logga in för att Zoho en som säkerhets administratör.

2. På fliken **användare** klickar du på **användar logo typ**.

    ![Zoho en användare](./media/zoho-one-tutorial/user.png)

3. Utför följande steg på sidan **Lägg till användare** :

    ![Zoho en Lägg till användare](./media/zoho-one-tutorial/add-user.png)
    
    a. I text rutan **namn** anger du namnet på den användare som **Britta Simon**.
    
    b. I textrutan **E-postadress** anger du användarens e-postadress, som brittasimon@contoso.com.

    >[!Note]
    >Välj din verifierade domän i listan domän.

    c. Klicka på **Lägg till**.

### <a name="test-sso"></a>Testa SSO

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

#### <a name="sp-initiated"></a>SP initierad:

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till Zoho med en inloggnings-URL där du kan starta inloggnings flödet.  

* Gå till Zoho för en inloggnings-URL direkt och starta inloggnings flödet därifrån.

#### <a name="idp-initiated"></a>IDP initierad:

* Klicka på **testa det här programmet** i Azure Portal så bör du loggas in automatiskt på den Zoho som du ställer in SSO för. 

Du kan också använda Microsoft Mina appar för att testa programmet i vilket läge som helst. När du klickar på Zoho en panel i Mina appar, om den har kon figurer ATS i SP-läge, kommer du att omdirigeras till programmets inloggnings sida för att initiera inloggnings flödet och om det kon figurer ATS i IDP-läge, bör du logga in automatiskt på den Zoho som du ställer in SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Zoho kan du framtvinga kontroll, vilket skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).