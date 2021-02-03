---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med Kendis-Scaling flexibel plattform | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Kendis-Scaling flexibel plattform.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/28/2021
ms.author: jeedes
ms.openlocfilehash: e02ff4926897fafc72e1a5081366faad5d2f03ba
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99509802"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-kendis-scaling-agile-platform"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Kendis-Scaling flexibel plattform

I den här självstudien får du lära dig att integrera Kendis-Scaling flexibel plattform med Azure Active Directory (Azure AD). När du integrerar Kendis-Scaling flexibel plattform med Azure AD kan du:

* Kontroll i Azure AD som har till gång till Kendis-Scaling flexibel plattform.
* Gör det möjligt för användarna att logga in automatiskt för att Kendis-Scaling smidig plattform med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Kendis-Scaling flexibel plattform enkel inloggning (SSO) som är aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Kendis-Scaling flexibel plattform stöder **SP-och IDP** -INITIERAd SSO
* Kendis-Scaling flexibel plattform stöder **just-in-Time** User-etablering


## <a name="adding-kendis-scaling-agile-platform-from-the-gallery"></a>Lägga till Kendis-Scaling flexibel plattform från galleriet

Om du vill konfigurera integreringen av Kendis-Scaling flexibel plattform i Azure AD måste du lägga till Kendis-Scaling flexibel plattform från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **Kendis flexibel plattform** i sökrutan.
1. Välj **Kendis – skala flexibel plattform** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-sso-for-kendis-scaling-agile-platform"></a>Konfigurera och testa Azure AD SSO för Kendis-Scaling flexibel plattform

Konfigurera och testa Azure AD SSO med Kendis-Scaling flexibel plattform med hjälp av en test användare som kallas **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren på Kendis-Scaling flexibel plattform.

Utför följande steg för att konfigurera och testa Azure AD SSO med Kendis-Scaling flexibel plattform:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera Kendis-Scaling flexibel plattform SSO](#configure-kendis-scaling-agile-platform-sso)** – om du vill konfigurera inställningar för enkel inloggning på program sidan.
    1. **[Skapa Kendis-Scaling smidig plattforms test användare](#create-kendis-scaling-agile-platform-test-user)** – om du vill ha en motsvarighet till B. Simon på Kendis-Scaling flexibel plattform som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal på sidan **Kendis-skalar du flexibel plattforms** program integrering hittar du avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://<SUBDOMAIN>.kendis.io`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<SUBDOMAIN>.kendis.io/login/saml`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<SUBDOMAIN>.kendis.io/login`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [Kendis-skalar ett flexibelt plattforms klient support team](mailto:support@kendis.io) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **konfigurera Kendis-Scaling flexibel plattform** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Kendis-Scaling flexibel plattform.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **Kendis-skalning flexibel plattform**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-kendis-scaling-agile-platform-sso"></a>Konfigurera Kendis-Scaling flexibel plattforms-SSO

1. Logga in på din Kendis-Scaling Agile Platform Company-webbplats som administratör i ett annat webbläsarfönster.

1. Gå till **inställningarna > SAML-konfigurationer**.

    ![inställningar för SAML-konfigurationer](./media/kendis-scaling-agile-platform-tutorial/settings.png)

1. Klicka på knappen **Redigera** längst ned på sidan och utför följande steg.

    ![SAML-konfigurationer](./media/kendis-scaling-agile-platform-tutorial/saml-configuration-settings.png)

    a. Kopiera **återanrops-URL** -värde, klistra in det här värdet i text rutan **SVARs-URL** i avsnittet grundläggande SAML-konfiguration i Azure Portal.

    b. I text rutan för **enkel inloggnings-URL för identitetsprovider** klistrar du in URL-värdet för **inloggning** som du har kopierat från Azure Portal.

    c.  I text rutan för **identitets leverantörs utfärdare** klistrar du in värdet för **Azure AD-identifieraren (enhets-ID)** som du har kopierat från Azure Portal.

    d. Öppna det hämtade **certifikatet (base64)** från Azure Portal i anteckningar och klistra in innehållet i text rutan **X. 509-certifikat** .

    e. **Välj standard grupp** i listan med alternativ. 

    f. Klicka på **Spara**.

### <a name="create-kendis-scaling-agile-platform-test-user"></a>Skapa Kendis-Scaling flexibel plattforms test användare

I det här avsnittet skapas en användare som kallas Britta Simon i Kendis-Scaling flexibel plattform. Kendis-Scaling flexibel plattform stöder just-in-Time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i Kendis-Scaling flexibel plattform, skapas en ny efter autentisering.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

#### <a name="sp-initiated"></a>SP initierad:

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till Kendis-Scaling flexibel plattforms inloggnings-URL där du kan starta inloggnings flödet.  

* Gå till Kendis-Scaling flexibel plattforms inloggnings-URL direkt och starta inloggnings flödet därifrån.

#### <a name="idp-initiated"></a>IDP initierad:

* Klicka på **testa det här programmet** i Azure Portal så bör du loggas in automatiskt på den Kendis-Scaling smidiga plattform som du har konfigurerat SSO för 

Du kan också använda Microsoft Mina appar för att testa programmet i vilket läge som helst. När du klickar på panelen Kendis-Scaling flexibel plattform i Mina appar, om den har kon figurer ATS i SP-läge omdirigeras du till programmets inloggnings sida för att initiera inloggnings flödet och om det kon figurer ATS i IDP-läge, bör du logga in automatiskt på den Kendis-Scaling Agile-plattform som du har konfigurerat SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Kendis-Scaling flexibel plattform kan du framtvinga kontroll av sessioner, vilket skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


