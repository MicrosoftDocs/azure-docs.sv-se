---
title: 'Självstudie: Azure Active Directory integration med en genom koppling | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och UNIFI.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: jeedes
ms.openlocfilehash: 1fa1c40c96694b71bc762050998257102b687c18
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101650647"
---
# <a name="tutorial-azure-active-directory-integration-with-unifi"></a>Självstudie: Azure Active Directory integrering med en enhetlig

I den här självstudien får du lära dig hur du integrerar en ENHETLIGhet med Azure Active Directory (Azure AD). När du integrerar en enhetlig integrering med Azure AD kan du:

* Kontroll i Azure AD som har till gång till en ENHETLIGhet.
* Gör det möjligt för användarna att bli inloggade automatiskt för att kunna använda sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* FÖRENAR enkel inloggning (SSO)-prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* En ENHETLIGhet stöder **SP-och IDP** -initierad SSO.
* En enhetlig användar etablering har stöd för **Automatisk** användar etablering.

## <a name="add-unifi-from-the-gallery"></a>Lägg till en enhetlig från galleriet

För att konfigurera integreringen av UNIFI med Azure AD måste du lägga till UNIFI från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** **skriver du** i rutan Sök.
1. Välj **alternativet för att** skapa en resultat panel och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-unifi"></a>Konfigurera och testa Azure AD SSO för att bli mer enhetlig

Konfigurera och testa Azure AD SSO med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i funktionen.

Utför följande steg för att konfigurera och testa Azure AD SSO med hjälp av funktionen:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera](#configure-unifi-sso)** interaktivt läge för enkel inloggning – för att konfigurera inställningar för enkel inloggning på program sidan.
    1. **[Skapa en enhetlig test användare](#create-unifi-test-user)** – för att få en motsvarighet till B. Simon i en samman koppling som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal går du till sidan **för integrering av program integration** och letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, utför följande steg:

    Skriv webb adressen i text rutan **identifierare** : `INVIEWlabs`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I rutan **Inloggnings-URL** anger du följande URL: `https://app.discoverunifi.com/login`

6. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

7. I avsnittet **Konfigurera UNIFI** kopierar du lämpliga URL:er baserat på dina behov.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att ge åtkomst till att gå till varandra.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. Välj **UNIFI** i programlistan.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-unifi-sso"></a>Konfigurera en enkel inloggning

1. Öppna ett nytt webbläsarfönster och logga in på din **UNIFI**-företagswebbplats som administratör.

2. Klicka på **Användare**.

    ![Skärm bild som visar användare som valts från webbplatsen för att gå vidare.](./media/unifi-tutorial/app-1.png)

3. Klicka på **Lägg till ny identitetsprovider**.

    ![Skärm bild som visar att AD New Identity Provider har valts.](./media/unifi-tutorial/app-2.png)

4. Utför följande steg i avsnittet **Lägg till identitetsprovider**:

    ![Skärm bild som visar Lägg till identitets leverantör där du kan ange de värden som beskrivs.](./media/unifi-tutorial/app-3.png) 

    a. Skriv namnet på identitetsprovidern i textrutan **Providernamn**.

    b. I textrutan för **provider-URL** klistrar du in värdet för **Inloggnings-URL**, som du har kopierat från Azure-portalen.

    c. Öppna certifikatet som du laddade ned från Azure-portalen i Anteckningar. Ta bort taggen **---BEGIN CERTIFICATE---** och **---END CERTIFICATE---** och klistra sedan in resten av innehållet i textrutan **Certifikat**.

    d. Markera kryssrutan **Är standardprovider**.

### <a name="create-unifi-test-user"></a>Skapa UNIFI-testanvändare

I det här avsnittet ska du skapa en användare med namnet Britta Simon. **UNIFI** har stöd för automatisk användaretablering, vilket betyder att inga manuella steg krävs. Användare skapas automatiskt efter en lyckad autentisering från Azure AD.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

#### <a name="sp-initiated"></a>SP initierad:

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till en enhetlig inloggnings-URL där du kan initiera inloggnings flödet.  

* Gå till direkt inloggnings-URL: en direkt och starta inloggnings flödet därifrån.

#### <a name="idp-initiated"></a>IDP initierad:

* Klicka på **testa det här programmet** i Azure Portal så bör du loggas in automatiskt till den uppslags nivå som du konfigurerade SSO för. 

Du kan också använda Microsoft Mina appar för att testa programmet i vilket läge som helst. När du klickar på panelen för att anpassa i Mina appar, om det kon figurer ATS i SP-läge, omdirigeras du till programmets inloggnings sida för att initiera inloggnings flödet och om det har kon figurer ATS i IDP-läge, bör du logga in automatiskt på den server som du konfigurerar SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat en genom gång kan du framtvinga kontroll av sessioner, vilket skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).