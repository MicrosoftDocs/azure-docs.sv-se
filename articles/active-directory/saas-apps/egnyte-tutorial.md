---
title: 'Självstudie: Azure Active Directory integrering med Egnyte | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Egnyte.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/27/2020
ms.author: jeedes
ms.openlocfilehash: 3d102b492326971ef186ba50a557ad8d1df5b6ec
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92454209"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-egnyte"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Egnyte

I den här självstudien får du lära dig hur du integrerar Egnyte med Azure Active Directory (Azure AD). När du integrerar Egnyte med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Egnyte.
* Gör det möjligt för användarna att logga in automatiskt till Egnyte med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Egnyte för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Egnyte stöder **SP**-initierad enkel inloggning
* När du har konfigurerat Egnyte kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram sessions kontroll med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)

> [!NOTE]
> ID för det här programmet är ett fast sträng värde så att endast en instans kan konfigureras i en klient.

## <a name="adding-egnyte-from-the-gallery"></a>Lägga till Egnyte från galleriet

För att konfigurera integrering av Egnyte i Azure AD behöver du lägga till Egnyte från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **Egnyte** i sökrutan.
1. Välj **Egnyte** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso"></a>Konfigurera och testa Azure AD SSO

I det här avsnittet får du konfigurera och testa enkel inloggning i Azure AD med Egnyte baserat på testanvändaren **Britta Simon**.
För att enkel inloggning ska fungera så måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Egnyte upprättas.

För att konfigurera och testa enkel inloggning med Azure AD för Egnyte, måste du utföra följande:

Om du vill konfigurera och testa Azure AD SSO med Egnyte, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera EGNYTE SSO](#configure-egnyte-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa Egnyte test User](#create-egnyte-test-user)** -om du vill ha en motsvarighet till B. Simon i Egnyte som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **Egnyte** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning med Egnyte-domäner och -URL:er](common/sp-signonurl.png)

    a. I text rutan **inloggnings-URL** skriver du en URL med följande mönster: `https://<companyname>.egnyte.com`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<companyname>.egnyte.com/samlconsumer/AzureAD`
    
    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdet med den faktiska Sign-On-URL: en och svars-URL: en. Kontakta [supportteamet för Egnyte-klienten](https://www.egnyte.com/corp/contact_egnyte.html) för att få det här värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

4. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

5. I avsnittet **Konfigurera Egnyte** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Egnyte.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. Välj **Egnyte** i programlistan.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-egnyte-sso"></a>Konfigurera Egnyte SSO

1. Logga in på din Egnyte-företags webbplats som administratör i ett annat webbläsarfönster.

2. Klicka på **Inställningar**.
   
    ![Inställningar 1](./media/egnyte-tutorial/ic787819.png "Inställningar")

3. Klicka på **Inställningar** på menyn.

    ![Inställningar](./media/egnyte-tutorial/ic787820.png "Inställningar")

4. Klicka på fliken **Konfiguration** och sedan på **Säkerhet**.

    ![Säkerhet](./media/egnyte-tutorial/ic787821.png "Säkerhet")

5. Gör följande i avsnittet **Enkel inloggningsautentisering**:

    ![Autentisering med enkel inloggning](./media/egnyte-tutorial/ic787822.png "Autentisering med enkel inloggning")   
    
    a. För **Enkel inloggningsautentisering** väljer du **SAML 2.0**.
   
    b. Som **identitetsprovider** väljer du **AzureAD**.
   
    c. Klistra in **inloggnings-URL:en** som du har kopierat från Azure-portalen till textrutan **Inloggnings-URL för identitetsprovider**.
   
    d. Klistra in **Azure AD-identifieraren** som du har kopierat från Azure-portalen till textrutan för **entitets-ID för identitetsprovider**.
      
    e. Öppna ditt base-64-kodade certifikat som du har laddat ned från Azure-portalen i Anteckningar, kopiera innehållet till Urklipp och klistra sedan in det i textrutan **Identity Provider Certificate** (Certifikat för identitetsprovider).
   
    f. Välj **e-postadress** som **standardanvändarmappning**.
   
    ex. Välj **inaktiverad** för **Use domain-specific issuer value** (Använd domänspecifikt utfärdarvärde).
   
    h. Klicka på **Spara**.

### <a name="create-egnyte-test-user"></a>Skapa Egnyte-testanvändare

Om du vill att Azure AD-användare ska kunna logga in på Egnyte måste de tillhandahållas i Egnyte. Etablering utförs manuellt med Egnyte.

**Utför följande steg för att tillhandahålla ett användarkonto:**

1. Logga in på din **Egnyte** företags webbplats som administratör.

2. Gå till **Settings (Inställningar) \> Users & Groups (Användare och grupper)**.

3. Klicka på **Add New User** (Lägg till ny användare) och välj sedan vilken typ av användare som du vill lägga till.
   
    ![Användare](./media/egnyte-tutorial/ic787824.png "Användare")

4. I avsnittet **New Power User** (Ny privilegierad användare) utför du följande steg:
    
    ![Ny standard användare](./media/egnyte-tutorial/ic787825.png "Ny standard användare")   

    a. I text rutan **e-postadress** anger du e-postmeddelandet som **Brittasimon \@ contoso.com**.

    b. I textrutan för **användarnamn** anger du användarnamnet för användaren, till exempel **Brittasimon**.

    c. Välj **Single Sign-On** (Enkel inloggning) som **Authentication Type** (Autentiseringstyp).
   
    d. Klicka på **Spara**.
    
    >[!NOTE]
    >Azure Active Directory-kontoinnehavaren får en e-postavisering.
    >

>[!NOTE]
>Du kan använda andra verktyg för Egnyte av användar konton eller API: er som tillhandahålls av Egnyte för att etablera Azure AD-användarkonton.
>

### <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Egnyte-panelen i åtkomstpanelen bör du automatiskt loggas in på Egnyte som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](./tutorial-list.md)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Vad är villkorlig åtkomst i Azure Active Directory?](../conditional-access/overview.md)

- [Vad är session Control i Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)