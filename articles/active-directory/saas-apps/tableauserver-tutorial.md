---
title: 'Självstudie: Azure Active Directory-integrering med enkel inloggning (SSO) med Tableau-Server | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory-och Tableau-servern.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/27/2020
ms.author: jeedes
ms.openlocfilehash: 3c9d79ef4fd73adbe3ba376f1723693ea8e85197
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2021
ms.locfileid: "98736514"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tableau-server"></a>Självstudie: Azure Active Directory-integrering med enkel inloggning (SSO) med Tableau-Server

I den här självstudien får du lära dig hur du integrerar Tableau-servern med Azure Active Directory (Azure AD). När du integrerar Tableau-servern med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Tableau-servern.
* Gör det möjligt för användarna att logga in automatiskt till Tableau-servern med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.


## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Tableau Server enkel inloggning (SSO) aktive rad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Tableau Server stöder **SP** -INITIERAd SSO

## <a name="adding-tableau-server-from-the-gallery"></a>Lägga till Tableau-Server från galleriet

Om du vill konfigurera integrering av Tableau-servern i Azure AD måste du lägga till Tableau-servern från galleriet i listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **Tableau Server** i sökrutan.
1. Välj **Tableau Server** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-tableau-server"></a>Konfigurera och testa Azure AD SSO för Tableau-Server

Konfigurera och testa Azure AD SSO med Tableau-servern med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Tableau Server.

Utför följande steg för att konfigurera och testa Azure AD SSO med Tableau-servern:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera Tableau Server SSO](#configure-tableau-server-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa Tableau Server test User](#create-tableau-server-test-user)** -för att få en motsvarighet till B. Simon på Tableau-servern som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal på sidan **Tableau Server** Application Integration letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    a. I text rutan **inloggnings-URL** skriver du en URL med följande mönster: `https://azure.<domain name>.link`

    b. I rutan **identifierare** anger du en URL med följande mönster: `https://azure.<domain name>.link`

    c. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://azure.<domain name>.link/wg/saml/SSO/index.html`

    > [!NOTE]
    > Föregående värden är inte verkliga värden. Uppdatera värdena med den faktiska URL: en och identifieraren från sidan för konfiguration av Tableau-servern som beskrivs senare i självstudien.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. I avsnittet **Konfigurera Tableau-Server** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Tableau-servern.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **Tableau-Server**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-tableau-server-sso"></a>Konfigurera Tableau Server SSO

1. För att få SSO konfigurerat för ditt program måste du logga in på Tableau-serverns klient organisation som administratör.

2. På fliken **konfiguration** väljer du **användar identitet & åtkomst** och väljer sedan fliken **autentiseringsmetod** .

    ![Skärm bild som visar den autentisering som valts från användar identitet & åtkomst.](./media/tableauserver-tutorial/tutorial-tableauserver-auth.png)

3. Utför följande steg på sidan **konfiguration** :

    ![Skärm bild som visar konfigurations sidan där du kan ange de värden som beskrivs.](./media/tableauserver-tutorial/tutorial-tableauserver-config.png)

    a. Som **autentiseringsmetod** väljer du SAML.

    b. Markera kryss rutan för **Aktivera SAML-autentisering för servern**.

    c. Tableau Server retur-URL – den URL som Tableau-användare kommer att komma åt, till exempel `http://tableau_server` . Använd `http://localhost` rekommenderas inte. Användning av en URL med ett avslutande snedstreck (till exempel `http://tableau_server/` ) stöds inte. Kopiera **Tableau Server Return URL** och klistra in den i text rutan för att **signera URL-adresser** i avsnittet **grundläggande SAML-konfiguration** i Azure Portal

    d. SAML-entitets-ID – entitets-ID: t identifierar unikt Tableau-Server installationen till IdP. Du kan ange din Tableau-server-URL igen, om du vill, men den behöver inte vara Tableau-serverns URL. Kopiera **SAML entitets-ID** och klistra in det i text rutan **identifierare** i avsnittet **grundläggande SAML-konfiguration** i Azure Portal

    e. Klicka på **filen Ladda ned XML-metadata** och öppna den i text redigerings programmet. Hitta intygets konsument tjänst-URL med http post och index 0 och kopiera URL: en. Klistra in den i text rutan för **svars-URL** i avsnittet **grundläggande SAML-konfiguration** i Azure Portal

    f. Leta upp din federationsmetadata som hämtades från Azure Portal och ladda sedan upp den i **SAML-IDP metadata-fil**.

    ex. Ange namnen på de attribut som IdP använder för att lagra användar namn, visnings namn och e-postadresser.

    h. Klicka på **Spara**

    > [!NOTE]
    > Kunden måste ladda upp en PEM x509-certifikatfil med fil namns tillägget. CRT och en RSA-eller DSA-fil för privata nycklar som har fil namns tillägget. Key, som en nyckel fil för certifikat. Mer information om certifikat filen och certifikat nyckel filen finns i [det här](https://help.tableau.com/current/server/en-us/saml_requ.htm) dokumentet. Om du behöver hjälp med att konfigurera SAML på Tableau-servern kan du läsa den här artikeln [Konfigurera serverns breda SAML](https://help.tableau.com/current/server/en-us/config_saml.htm).

### <a name="create-tableau-server-test-user"></a>Skapa Tableau-Server test användare

Syftet med det här avsnittet är att skapa en användare som kallas B. Simon i Tableau-servern. Du måste etablera alla användare på Tableau-servern.

Användar namnet för användaren ska matcha det värde som du har konfigurerat i det anpassade Azure AD-attributet för **användar namn**. Med rätt mappning bör integrationen fungera för att konfigurera enkel inloggning för Azure AD.

> [!NOTE]
> Om du behöver skapa en användare manuellt måste du kontakta Tableau-serverns administratör i din organisation.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till Tableau Server-inloggnings-URL där du kan starta inloggnings flödet. 

* Gå till Tableau Server inloggnings-URL direkt och starta inloggnings flödet därifrån.

* Du kan använda Microsoft Mina appar. När du klickar på panelen Tableau server i Mina appar omdirigeras det till Tableau-inloggnings-URL: en. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Tableau-servern kan du framtvinga sessionsnycklar som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Sessions kontroller utökas från villkorlig åtkomst. [Lär dig hur du tvingar fram sessions kontroll med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)