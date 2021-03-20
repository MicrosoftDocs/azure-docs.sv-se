---
title: 'Självstudie: Azure Active Directory integrering med arbets plats för AutoTask | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Autotask Workplace.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/02/2021
ms.author: jeedes
ms.openlocfilehash: 1a2602ee3b8fad6e87a778ab966f7cb1f8aad8c2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "101649979"
---
# <a name="tutorial-azure-active-directory-integration-with-autotask-workplace"></a>Självstudie: Azure Active Directory integrering med arbets plats för AutoTask

I den här självstudien får du lära dig att integrera arbets platsen för automatisk aktivitet med Azure Active Directory (Azure AD). När du integrerar arbets platsen för automatisk aktivitet med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till arbets platsen för automatisk aktivitet.
* Gör det möjligt för användarna att logga in automatiskt på arbets platsen för automatiska aktiviteter med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Schemaläggning med enkel inloggning (SSO) på arbets plats för aktivitet.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Autotask Workplace har stöd för **SP- och IDP**-initierad enkel inloggning

## <a name="add-autotask-workplace-from-the-gallery"></a>Lägg till arbets ytan för AutoTask från galleriet

För att konfigurera integrering av Autotask Workplace med Azure AD behöver du lägga till Autotask Workplace från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du **arbets plats** för automatisk uppgift i sökrutan.
1. Välj **arbets ytan AutoTask** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-autotask-workplace"></a>Konfigurera och testa Azure AD SSO för arbets platsen för automatisk aktivitet

Konfigurera och testa Azure AD SSO med arbets platsen för automatisk aktivitet med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i arbets platsen för automatisk aktivitet.

Utför följande steg för att konfigurera och testa Azure AD SSO med arbets plats för automatisk aktivitet:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera AutoTask-arbetsplatsen SSO](#configure-autotask-workplace-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa arbets plats för arbets plats](#create-autotask-workplace-test-user)** för automatisk uppgift – om du vill ha en motsvarighet till B. Simon i arbets platsen för automatisk aktivitet som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal går du till sidan för program integrering på **arbets plats för arbets plats** och letar reda på avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, utför följande steg:

    ![Skärm bild som visar den grundläggande SAML-konfigurationen, där du kan ange identifierare, svara U R L och välja Spara.](common/idp-intiated.png)

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://<subdomain>.awp.autotask.net/singlesignon/saml/metadata`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<subdomain>.awp.autotask.net/singlesignon/saml/SSO`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![Skärm bild som visar ytterligare U R LS där du kan ange ett tecken på U R L.](common/metadata-upload-additional-signon.png)

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<subdomain>.awp.autotask.net/loginsso`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [kundsupporten för Autotask Workplace](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) och be om dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

7. I avsnittet **Konfigurera Autotask Workplace** kopierar du lämpliga URL:er enligt dina behov.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till arbets platsen för aktiviteter.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I programlistan väljer du **Autotask Workplace**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-autotask-workplace-sso"></a>Konfigurera AutoTask-arbetsplatsens SSO

1. I ett annat webbläsarfönster loggar du in på Workplace Online med autentiseringsuppgifter för administratör.

    > [!Note]
    > När du konfigurerar IdP måste en underdomän anges. För att fastställa rätt underdomän loggar du in på Workplace Online. När du loggat in bör du anteckna underdomänen i URL:en. Underdomänen är delen mellan ”https://” och ”.awp.autotask.net/” och ska vara us, eu, ca eller au.

2. Gå till **konfiguration**  >  **enkel inloggning** och utför följande steg:

    ![Konfiguration av enkel inloggning för Autotask](./media/autotaskworkplace-tutorial/configuration-1.png)

    a. Välj alternativet **XML-metadatfil** och ladda sedan upp den nedladdade **XML för federationsmetadata** från Azure-portalen.

    b. Klicka på **AKTIVERA ENKEL INLOGGNING**.

    ![Konfiguration för godkännande av enkel inloggning för Autotask](./media/autotaskworkplace-tutorial/configuration-2.png)

    c. Markera kryssrutan **I confirm this information is correct and I trust this IdP** (Jag bekräftar att denna information är korrekt och jag litar på denna IdP).

    d. Klicka på **GODKÄNN**.

> [!Note]
> Om du behöver hjälp med att konfigurera Autotask Workplace läser du [den här sidan](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) för att få hjälp med ditt Workplace-konto.

### <a name="create-autotask-workplace-test-user"></a>Skapa Autotask Workplace-testanvändare

I det här avsnittet skapar du en användare med namnet Britta Simon i Autotask Workplace. Ta hjälp från [supportteamet för Autotask Workplace](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) och lägg till användarna på Autotask Workplace-plattformen.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

#### <a name="sp-initiated"></a>SP initierad:

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till arbets platsens inloggnings-URL där du kan starta inloggnings flödet.  

* Gå till arbets platsens inloggnings-URL direkt och starta inloggnings flödet därifrån.

#### <a name="idp-initiated"></a>IDP initierad:

* Klicka på **testa det här programmet** i Azure Portal så bör du loggas in automatiskt på arbets platsen för automatisk aktivitet som du ställer in SSO för. 

Du kan också använda Microsoft Mina appar för att testa programmet i vilket läge som helst. När du klickar på panelen för automatisk aktivitets arbets yta i Mina appar, om den har kon figurer ATS i SP-läge, omdirigeras du till programmets inloggnings sida för att initiera inloggnings flödet och om det kon figurer ATS i IDP-läge, bör du logga in automatiskt på arbets platsen för automatisk aktivitet som du har konfigurerat SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat arbets plats för AutoTask kan du framtvinga kontroll av sessioner, vilket skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).