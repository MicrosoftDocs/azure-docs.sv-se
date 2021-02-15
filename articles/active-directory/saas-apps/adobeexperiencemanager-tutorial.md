---
title: 'Självstudie: Azure Active Directory integration med Adobe Experience Manager | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Adobe Experience Manager.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/17/2021
ms.author: jeedes
ms.openlocfilehash: 6cb490408cd66d5747156ef48ea9b4b2daa92abf
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/10/2021
ms.locfileid: "100094726"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-experience-manager"></a>Självstudie: Azure Active Directory integration med Adobe Experience Manager

I den här självstudien får du lära dig hur du integrerar Adobe Experience Manager med Azure Active Directory (Azure AD). När du integrerar Adobe Experience Manager med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Adobe Experience Manager.
* Gör det möjligt för användarna att logga in automatiskt till Adobe Experience Manager med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Enkel inloggning (SSO) för Adobe Experience Manager-aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Adobe Experience Manager stöder **SP- och IDP**-initierad enkel inloggning

* Adobe Experience Manager stöder **JIT**-användaretablering (Just-In-Time)

## <a name="adding-adobe-experience-manager-from-the-gallery"></a>Lägga till Adobe Experience Manager från galleriet

För att konfigurera integreringen av Adobe Experience Manager i Azure AD måste du lägga till Adobe Experience Manager från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **Adobe Experience Manager** i sökrutan.
1. Välj **Adobe Experience Manager** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-sso-for-adobe-experience-manager"></a>Konfigurera och testa Azure AD SSO för Adobe Experience Manager

Konfigurera och testa Azure AD SSO med Adobe Experience Manager med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Adobe Experience Manager.

Utför följande steg för att konfigurera och testa Azure AD SSO med Adobe Experience Manager:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
2. **[Konfigurera Adobe Experience Manager SSO](#configure-adobe-experience-manager-sso)** – för att konfigurera enskilda Sign-On inställningar på program sidan.
    1. **[Skapa Adobe Experience Manager-testanvändare](#create-adobe-experience-manager-test-user)** – för att skapa en motsvarighet till Britta Simon i Adobe Experience Manager som är länkad till Azure AD-representationen av användaren.
6. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. På sidan för program integrering i **Adobe Experience Manager** på Azure Portal, letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

    a. I textrutan **Identifierare** skriver du ett unikt värde som du även definierar på AEM-servern.

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<AEM Server Url>/saml_login`

    > [!NOTE]
    > Värdet för svars-URL:en är inte verkligt. Uppdatera värdet för Svars-URL med den faktiska svars-URL:en. Kontakta [Adobe Experience Manager-kundsupporten](https://helpx.adobe.com/support/experience-manager.html) och be om det här värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I textrutan **Inloggnings-URL** skriver du URLe:n för Adobe Experience Manager-servern.

6. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

7. I avsnittet **Konfigurera Adobe Experience Manager** kopierar du lämpliga URL:er baserat på dina behov.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Adobe Experience Manager.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan med program väljer du **Adobe Experience Manager**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-adobe-experience-manager-sso"></a>Konfigurera Adobe Experience Manager SSO

1. Öppna ett nytt webbläsarfönster och öppna **Adobe Experience Manager**-administrationsportalen.

2. Välj **Inställningar**  >  **säkerhets**  >  **användare**.

    ![Skärm bild som visar panelen användare i Adobe Experience Manager.](./media/adobe-experience-manager-tutorial/user-1.png)

3. Välj **Administratör** eller en annan relevant användare.

    ![Skärm bild som visar adminisrator-användaren.](./media/adobe-experience-manager-tutorial/tutorial-admin-6.png)

4. Välj **konto inställningar**  >  **Hantera TrustStore**.

    ![Skärm bild som visar hantera TrustStore under konto inställningar.](./media/adobe-experience-manager-tutorial/manage-trust.png)

5. Klicka på **Välj certifikatsfil** under **Lägg till certifikat från CER-fil**. Bläddra till och välj certifikatfilen, som du redan har hämtat från Azure-portalen.

    ![Skärm bild som visar knappen Välj certifikat fil.](./media/adobe-experience-manager-tutorial/user-2.png)

6. Certifikatet läggs till i TrustStore. Observera certifikatets alias.

    ![Skärm bild som visar att certifikatet har lagts till i TrustStore.](./media/adobe-experience-manager-tutorial/tutorial-admin-7.png)

7. Välj **authentication-service** på sidan **Användare**.

    ![Sreenshot som fokuserar på autentiserings tjänsten på skärmen.](./media/adobe-experience-manager-tutorial/tutorial-admin-8.png)

8. Välj **konto inställningar**  >  **skapa/hantera nyckel lagring**. Skapa KeyStore genom att ange ett lösenord.

    ![Skärm bild som visar hur du hanterar nyckel lagring.](./media/adobe-experience-manager-tutorial/tutorial-admin-9.png)

9. Gå tillbaka till administrationsskärmen. Välj sedan **Inställningar**  >  **Åtgärds**  >  **webb konsol**.

    ![Skärm bild som visar webb konsolen under åtgärder i avsnittet Inställningar.](./media/adobe-experience-manager-tutorial/tutorial-admin-1.png)

    Konfigurationssidan öppnas.

    ![Konfigurera enkel inloggning – knappen Spara](./media/adobe-experience-manager-tutorial/tutorial-admin-2.png)

10. Leta upp **Adobe Granite SAML 2.0 Authentication Handler**. Välj sedan ikonen **Lägg till**.

    ![Skärm bild som visar Adobe Granite SAML 2,0 Authentication handler.](./media/adobe-experience-manager-tutorial/tutorial-admin-3.png)

11. Utför följande åtgärder på den här sidan.

    ![Konfigurera enkel inloggning – knappen Spara](./media/adobe-experience-manager-tutorial/tutorial-admin-4.png)

    a. Ange **/** i rutan **Sökväg**.

    b. I rutan **IDP URL** (IDP-URL) anger du värdet för **Inloggnings-URL** som du kopierade från Azure-portalen.

    c. I rutan **Certifikatalias för IDP**  anger du värdet för **Certifikatalias** som du lade till i TrustStore.

    d. I rutan **Säkerhetstilldelat entitets-ID** anger du det unika värdet för **Azure Ad-identifieraren** som du konfigurerade på Azure-portalen.

    e. I rutan **URL för konsumenttjänst för försäkran** anger du värdet för **svars-URL:en** som du konfigurerade på Azure-portalen.

    f. I rutan för **lösenord för nyckelarkiv** anger du **lösenordet** som du angav i KeyStore.

    ex. I rutan **ID för användarattribut** anger du **namn-ID:t** eller ett annat användar-ID som är relevant i ditt fall.

    h. Välj **Skapa CRX-användare automatiskt**.

    i. I rutan **Utloggnings-URL** anger du det unika värdet för **utloggnings-URL:en** som du hämtade från Azure-portalen.

    j. Välj **Spara**.

### <a name="create-adobe-experience-manager-test-user"></a>Skapa Adobe Experience Manager-testanvändare

I det här avsnittet ska du skapa en användare med namnet Britta Simon i Adobe Experience Manager. Om du valde alternativet **Skapa CRX-användare automatiskt** skapas användare automatiskt efter en lyckad autentisering.

Om du vill skapa användare manuellt arbetar du med [support teamet för Adobe Experience Manager](https://helpx.adobe.com/support/experience-manager.html) för att lägga till användarna i Adobe Experience Manager-plattformen.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

#### <a name="sp-initiated"></a>SP initierad:

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till Adobe Experience Manager-inloggnings-URL där du kan starta inloggnings flödet.  

* Gå till inloggnings-URL: en för Adobe Experience Manager direkt och starta inloggnings flödet därifrån.

#### <a name="idp-initiated"></a>IDP initierad:

* Klicka på **testa det här programmet** i Azure Portal så bör du loggas in automatiskt till Adobe Experience Manager för vilken du konfigurerar SSO 

Du kan också använda Microsoft Mina appar för att testa programmet i vilket läge som helst. När du klickar på panelen Adobe Experience Manager i Mina appar, om den har kon figurer ATS i SP-läge, omdirigeras du till programmets inloggnings sida för att initiera inloggnings flödet och om det kon figurer ATS i IDP-läge, bör du loggas in automatiskt på den Adobe Experience Manager för vilken du konfigurerar SSO. Mer information om Mina appar finns i [Introduktion till Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Adobe Experience Manager kan du framtvinga sessionshantering, vilket skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
