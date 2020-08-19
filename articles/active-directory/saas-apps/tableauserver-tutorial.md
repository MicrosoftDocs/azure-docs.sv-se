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
ms.date: 05/07/2020
ms.author: jeedes
ms.openlocfilehash: a074b3f0accf0e0cf800f6b06ce8466e984f8693
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88546602"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tableau-server"></a>Självstudie: Azure Active Directory-integrering med enkel inloggning (SSO) med Tableau-Server

I den här självstudien får du lära dig hur du integrerar Tableau-servern med Azure Active Directory (Azure AD). När du integrerar Tableau-servern med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Tableau-servern.
* Gör det möjligt för användarna att logga in automatiskt till Tableau-servern med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Tableau Server enkel inloggning (SSO) aktive rad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Tableau Server stöder **SP** -INITIERAd SSO
* När du har konfigurerat Tableau-servern kan du framtvinga kontroll av sessioner, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen utökas från villkorlig åtkomst. [Lär dig hur du tvingar fram sessions kontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-tableau-server-from-the-gallery"></a>Lägga till Tableau-Server från galleriet

Om du vill konfigurera integrering av Tableau-servern i Azure AD måste du lägga till Tableau-servern från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **Tableau Server** i sökrutan.
1. Välj **Tableau Server** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-tableau-server"></a>Konfigurera och testa enkel inloggning med Azure AD för Tableau-Server

Konfigurera och testa Azure AD SSO med Tableau-servern med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Tableau Server.

Om du vill konfigurera och testa Azure AD SSO med Tableau Server slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera Tableau Server SSO](#configure-tableau-server-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa Tableau Server test User](#create-tableau-server-test-user)** -för att få en motsvarighet till B. Simon på Tableau-servern som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)på sidan **Tableau Server** Application Integration letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

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

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare**och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Tableau-servern.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **Tableau-Server**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-tableau-server-sso"></a>Konfigurera Tableau Server SSO

1. För att få SSO konfigurerat för ditt program måste du logga in på Tableau-serverns klient organisation som administratör.

2. På fliken **konfiguration** väljer du **användar identitet & åtkomst**och väljer sedan fliken **autentiseringsmetod** .

    ![Konfigurera enkel inloggning](./media/tableauserver-tutorial/tutorial-tableauserver-auth.png)

3. Utför följande steg på sidan **konfiguration** :

    ![Konfigurera enkel inloggning](./media/tableauserver-tutorial/tutorial-tableauserver-config.png)

    a. Som **autentiseringsmetod**väljer du SAML.

    b. Markera kryss rutan för **Aktivera SAML-autentisering för servern**.

    c. Tableau Server retur-URL – den URL som Tableau-användare kommer att komma åt, till exempel <http://tableau_server> . Använd `http://localhost` rekommenderas inte. Användning av en URL med ett avslutande snedstreck (till exempel `http://tableau_server/` ) stöds inte. Kopiera **Tableau Server Return URL** och klistra in den i text rutan för att **signera URL-adresser** i avsnittet **grundläggande SAML-konfiguration** i Azure Portal

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

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Tableau server på åtkomst panelen, bör du loggas in automatiskt på den Tableau-server som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Testa Tableau-servern med Azure AD](https://aad.portal.azure.com/)

- [Vad är session Control i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)