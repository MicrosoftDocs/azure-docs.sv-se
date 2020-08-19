---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Akamai | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Akamai.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/03/2020
ms.author: jeedes
ms.openlocfilehash: fbec82e25424fd5220aa992cf2dd0e8449e6a0a4
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88523141"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-akamai"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Akamai

I den här självstudien får du lära dig hur du integrerar Akamai med Azure Active Directory (Azure AD). När du integrerar Akamai med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Akamai.
* Gör det möjligt för användarna att logga in automatiskt till Akamai med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Med Azure Active Directory-och Akamai Enterprise Application Access integration får du sömlös åtkomst till äldre program som finns i molnet eller lokalt. Den integrerade lösningen drar nytta av alla moderna funktioner i Azure Active Directory som [Azure AD villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal), [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) och [Azure AD Identity Governance](https://docs.microsoft.com/azure/active-directory/governance/identity-governance-overview) för äldre program åtkomst utan app-ändringar eller agent installation.

I bilden nedan beskrivs var Akamai EAA passar i det bredare hybrid scenariot för säker åtkomst

![Akamai EAA passar i det bredare scenariot med hybrid säker åtkomst](./media/header-akamai-tutorial/introduction01.png)

### <a name="key-authentication-scenarios"></a>Scenarier för nyckel autentisering

Förutom Azure Active Directory inbyggt integrerings stöd för moderna autentiseringsprotokoll som öppen ID Connect, SAML och WS-utfodras, utökar Akamai i EAA säker åtkomst för äldre-baserade autentiseringscertifikat för både intern och extern åtkomst med Azure AD, vilket möjliggör moderna scenarier (t. ex. lösen ords lös åtkomst) till dessa program. Det här omfattar:

* Huvudbaserade autentiseringsmekanismer
* Fjärrskrivbord
* SSH (Secure Shell)
* Kerberos-autentisering av appar
* VNC (Virtual Network data behandling)
* Anonym autentisering eller inga inbyggta autentiseringscertifikat
* NTLM-autentisering av appar (skydd med dubbla prompter för användaren)
* Formulärbaserade program (skydd med dubbla prompter för användaren)

### <a name="integration-scenarios"></a>Integrations scenarier

Microsoft och Akamai EAA-partnerskap ger flexibiliteten att uppfylla dina affärs behov genom att stödja flera integrerings scenarier baserat på ditt företags behov. Dessa kan användas för att ge täckning på noll dagar för alla program och gradvis klassificera och konfigurera lämpliga princip klassificeringar.

#### <a name="integration-scenario-1"></a>Integrations scenario 1

Akamai EAA konfigureras som ett enda program i Azure AD. Administratören kan konfigurera CA-principen för programmet och när villkoren är uppfyllda kan användarna få åtkomst till Akamai EAA-portalen.

- **Proffs**:

* Du behöver bara konfigurera IDP en gång

**Nack delar**:

* Användare har slut på två program portaler

* Enskild gemensam princip för certifikat utfärdare för alla program.

![Integrations scenario 1](./media/header-akamai-tutorial/scenario1.png)

#### <a name="integration-scenario-2"></a>Integrations scenario 2

Akamai EAA-programmet konfigureras separat på Azure AD-portalen. Administratören kan konfigurera en enskild princip för certifikat utfärdaren i programmen och när villkoren är uppfyllda kan användare omdirigeras direkt till det specifika programmet.

- **Proffs**:

* Du kan definiera enskilda CA-principer

* Alla appar visas på panelen 0365 Rutmärket och myApps.microsoft.com.


**Nack delar**:

* Du måste konfigurera flera IDP.

![Integrations scenario 2](./media/header-akamai-tutorial/scenario2.png)

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Akamai för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

- Akamai stöder IDP initierad SSO

#### <a name="important"></a>Viktigt

Alla inställningar som anges nedan är desamma för **integrerings scenario 1** och **Scenario 2**. För **integrerings scenario 2** måste du konfigurera enskilda IDP i Akamai-EAA och URL-egenskapen måste ändras så att den pekar på programmets URL.

![Viktigt](./media/header-akamai-tutorial/important.png)

## <a name="adding-akamai-from-the-gallery"></a>Lägga till Akamai från galleriet

Om du vill konfigurera integreringen av Akamai i Azure AD måste du lägga till Akamai från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **Akamai** i sökrutan.
1. Välj **Akamai** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-akamai"></a>Konfigurera och testa enkel inloggning med Azure AD för Akamai

Konfigurera och testa Azure AD SSO med Akamai med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Akamai.

Om du vill konfigurera och testa Azure AD SSO med Akamai, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    * **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera Akamai SSO](#configure-akamai-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    * **[Konfigurera IDP](#setting-up-idp)**
    * **[Rubrik baserad autentisering](#header-based-authentication)**
    * **[Fjärrskrivbord](#remote-desktop)**
    * **[SSH](#ssh)**
    * **[Kerberos-autentisering](#kerberos-authentication)**
    * **[Skapa Akamai test User](#create-akamai-test-user)** -om du vill ha en motsvarighet till B. Simon i Akamai som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **Akamai** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://<Yourapp>.login.go.akamai-access.com/saml/sp/response`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https:// <Yourapp>.login.go.akamai-access.com/saml/sp/response`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren och svars-URL. Kontakta [Akamai client support team](https://www.akamai.com/us/en/contact-us/) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. I avsnittet **Konfigurera Akamai** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Akamai.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **Akamai**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-akamai-sso"></a>Konfigurera Akamai SSO

### <a name="setting-up-idp"></a>Konfigurera IDP

**Konfiguration av AKAMAI EAA-IDP**

1. Logga in på **Akamai Enterprise Application Access** Console.
1. I **Akamais EAA-konsolen**väljer du **identitets**  >  **identitets leverantörer** och klickar på **Lägg till identitetsprovider**.

    ![Konfigurera Akamai](./media/header-akamai-tutorial/configure01.png)

1. Utför följande steg på sidan **Skapa ny identitetsprovider** :

    ![Konfigurera Akamai](./media/header-akamai-tutorial/configure02.png)

    a. Ange det **unika namnet**.

    b. Välj **SAML från tredje part** och klicka på **skapa identitetsprovider och konfigurera**.

### <a name="general-settings"></a>Allmänna inställningar

1. **Identitets spärr** – ange namnet på (SP Base URL – kommer att användas för Azure AD-konfiguration)

    > [!NOTE]
    > Du kan välja att ha en egen anpassad domän (kräver en DNS-post och ett certifikat). I det här exemplet ska vi använda Akamai-domänen.

1. **Akamai-moln zon** – Välj lämplig moln zon.
1. **Certifikat validering** -kontrol lera Akamai-dokumentationen (valfritt)

    ![Konfigurera Akamai](./media/header-akamai-tutorial/configure03.png)

### <a name="authentication-configuration"></a>Konfiguration av autentisering

1. URL – ange URL-adressen till samma som din identitets spärr (det är där användare omdirigeras efter autentisering).
2. Logga ut URL: uppdatera utloggnings-URL: en.
3. Signera SAML-begäran: standardvärdet är avmarkerat.
4. Lägg till programmet i Azure AD-konsolen för IDP metadata-fil.

    ![Konfigurera Akamai](./media/header-akamai-tutorial/configure04.png)

### <a name="session-settings"></a>Sessionsinställningar

Lämna inställningarna som standard.

![Konfigurera Akamai](./media/header-akamai-tutorial/sessionsettings.png)

### <a name="directories"></a>Kataloger

Hoppa över katalog konfigurationen.

![Konfigurera Akamai](./media/header-akamai-tutorial/directories.png)

### <a name="customization-ui"></a>Anpassnings gränssnitt

Du kan lägga till anpassning till IDP.

![Konfigurera Akamai](./media/header-akamai-tutorial/customizationui.png)

### <a name="advanced-settings"></a>Avancerade inställningar

Hoppa över avancerade inställningar/Se Akamai-dokumentationen för mer information.

![Konfigurera Akamai](./media/header-akamai-tutorial/advancesettings.png)

### <a name="deployment"></a>Distribution

1. Klicka på distribuera identitets leverantör.

    ![Konfigurera Akamai](./media/header-akamai-tutorial/deployment.png)

2. Verifiera att distributionen lyckades

### <a name="header-based-authentication"></a>Rubrik baserad autentisering

Akamai-huvudbaserad autentisering

1. Välj **anpassat HTTP-** formulär guiden Lägg till program.

    ![Konfigurera Akamai](./media/header-akamai-tutorial/configure05.png)

2. Ange **program namn** och **Beskrivning**.

    ![Konfigurera Akamai](./media/header-akamai-tutorial/configure06.png)

    ![Konfigurera Akamai](./media/header-akamai-tutorial/configure07.png)

    ![Konfigurera Akamai](./media/header-akamai-tutorial/configure08.png)

#### <a name="authentication"></a>Autentisering

1. Välj fliken **autentisering** .

    ![Konfigurera Akamai](./media/header-akamai-tutorial/configure09.png)

2. Tilldela **identitets leverantören**

    ![Konfigurera Akamai](./media/header-akamai-tutorial/configure10.png)

#### <a name="services"></a>Tjänster

Klicka på Spara och gå till autentisering.

![Konfigurera Akamai](./media/header-akamai-tutorial/configure11.png)

#### <a name="advanced-settings"></a>Avancerade inställningar

1. Under **kundens HTTP-rubriker**anger du attributet **CustomerHeader** och **SAML**.

    ![Konfigurera Akamai](./media/header-akamai-tutorial/configure12.png)

1. Klicka på **Spara och gå till distributions** knapp.

    ![Konfigurera Akamai](./media/header-akamai-tutorial/configure13.png)

#### <a name="deploy-the-application"></a>Distribuera programmet

1. Klicka på knappen för att **distribuera program** .

    ![Konfigurera Akamai](./media/header-akamai-tutorial/configure14.png)

1. Verifiera att programmet har distribuerats.

    ![Konfigurera Akamai](./media/header-akamai-tutorial/configure15.png)

1. Slut användar upplevelse.

    ![Konfigurera Akamai](./media/header-akamai-tutorial/enduser01.png)

    ![Konfigurera Akamai](./media/header-akamai-tutorial/enduser02.png)

1. Villkorlig åtkomst.

    ![Konfigurera Akamai](./media/header-akamai-tutorial/conditionalaccess01.png)

    ![Konfigurera Akamai](./media/header-akamai-tutorial/conditionalaccess02.png)

#### <a name="remote-desktop"></a>Fjärrskrivbord

1. Välj **RDP** i guiden Lägg till program.

    ![Konfigurera Akamai](./media/header-akamai-tutorial/configure16.png)

1. Ange **program namn** och **Beskrivning**.

    ![Konfigurera Akamai](./media/header-akamai-tutorial/configure17.png)

    ![Konfigurera Akamai](./media/header-akamai-tutorial/configure18.png)

1. Ange den anslutning som ska betjäna detta.

    ![Konfigurera Akamai](./media/header-akamai-tutorial/configure19.png)

#### <a name="authentication"></a>Autentisering

Klicka på **Spara och gå till tjänster**.

![Konfigurera Akamai](./media/header-akamai-tutorial/configure20.png)

#### <a name="services"></a>Tjänster

Klicka på **Spara och gå till avancerade inställningar**.

![Konfigurera Akamai](./media/header-akamai-tutorial/configure21.png)

#### <a name="advanced-settings"></a>Avancerade inställningar

1. Klicka på **Spara och gå till distribution**.

    ![Konfigurera Akamai](./media/header-akamai-tutorial/configure22.png)

    ![Konfigurera Akamai](./media/header-akamai-tutorial/configure23.png)

    ![Konfigurera Akamai](./media/header-akamai-tutorial/configure24.png)

1. Slut användar upplevelse

    ![Konfigurera Akamai](./media/header-akamai-tutorial/enduser03.png)

    ![Konfigurera Akamai](./media/header-akamai-tutorial/enduser02.png)

1. Villkorlig åtkomst

    ![Konfigurera Akamai](./media/header-akamai-tutorial/conditionalaccess04.png)

    ![Konfigurera Akamai](./media/header-akamai-tutorial/conditionalaccess05.png)

    ![Konfigurera Akamai](./media/header-akamai-tutorial/conditionalaccess06.png)

1. Du kan också ange URL: en för RDP-programmet direkt.

#### <a name="ssh"></a>SSH

1. Gå till Lägg till program och välj **SSH**.

    ![Konfigurera Akamai](./media/header-akamai-tutorial/configure25.png)

1. Ange **program namn** och **Beskrivning**.

    ![Konfigurera Akamai](./media/header-akamai-tutorial/configure26.png)

1. Konfigurera program identitet.

    ![Konfigurera Akamai](./media/header-akamai-tutorial/configure27.png)

    a. Ange namn/beskrivning.

    b. Ange program serverns IP/FQDN och port för SSH.

    c. Ange SSH-användarnamn/lösen fras * kontrol lera Akamai EAA.

    d. Ange det externa värd namnet.

    e. Ange platsen för anslutningen och välj kopplingen.

#### <a name="authentication"></a>Autentisering

Klicka på **Spara och gå till tjänster**.

![Konfigurera Akamai](./media/header-akamai-tutorial/configure28.png)

#### <a name="services"></a>Tjänster

Klicka på **Spara och gå till avancerade inställningar**.

![Konfigurera Akamai](./media/header-akamai-tutorial/configure29.png)

#### <a name="advanced-settings"></a>Avancerade inställningar

Klicka på Spara och gå till distribution

![Konfigurera Akamai](./media/header-akamai-tutorial/configure30.png)

![Konfigurera Akamai](./media/header-akamai-tutorial/configure31.png)

#### <a name="deployment"></a>Distribution

1. Klicka på **distribuera program**.

    ![Konfigurera Akamai](./media/header-akamai-tutorial/configure32.png)

1. Slut användar upplevelse

    ![Konfigurera Akamai](./media/header-akamai-tutorial/enduser03.png)

    ![Konfigurera Akamai](./media/header-akamai-tutorial/enduser04.png)

1. Villkorlig åtkomst

    ![Konfigurera Akamai](./media/header-akamai-tutorial/conditionalaccess04.png)

    ![Konfigurera Akamai](./media/header-akamai-tutorial/conditionalaccess07.png)

    ![Konfigurera Akamai](./media/header-akamai-tutorial/conditionalaccess08.png)

    ![Konfigurera Akamai](./media/header-akamai-tutorial/conditionalaccess09.png)

### <a name="kerberos-authentication"></a>Kerberos-autentisering

I exemplet nedan kommer vi att publicera en intern webb server <code>http://frp-app1.superdemo.live</code> och aktivera SSO med hjälp av KCD

#### <a name="general-tab"></a>Fliken Allmänt

![Konfigurera Akamai](./media/header-akamai-tutorial/generaltab.png)

#### <a name="authentication-tab"></a>Fliken Autentisering

Tilldela identitets leverantören

![Konfigurera Akamai](./media/header-akamai-tutorial/authenticationtab.png)

#### <a name="services-tab"></a>Fliken tjänster

![Konfigurera Akamai](./media/header-akamai-tutorial/servicestab.png)

#### <a name="advanced-settings"></a>Avancerade inställningar

![Konfigurera Akamai](./media/header-akamai-tutorial/advancesettings02.png)

> [!NOTE]
> SPN för webb servern har SPN@Domain formatet t `HTTP/frp-app1.superdemo.live@SUPERDEMO.LIVE` . ex.: för den här demon. Lämna kvar resten av inställningarna till standard.

#### <a name="deployment-tab"></a>Fliken distribution

![Konfigurera Akamai](./media/header-akamai-tutorial/deploymenttab.png)

#### <a name="adding-directory"></a>Lägger till katalog

1. Välj **AD** i list rutan.

    ![Konfigurera Akamai](./media/header-akamai-tutorial/configure33.png)

1. Ange nödvändiga data.

    ![Konfigurera Akamai](./media/header-akamai-tutorial/configure34.png)

1. Verifiera skapandet av katalogen.

    ![Konfigurera Akamai](./media/header-akamai-tutorial/directorydomain.png)

1. Lägg till de grupper/organisationsenheter som behöver åtkomst.

    ![Konfigurera Akamai](./media/header-akamai-tutorial/addgroup.png)

1. I listan nedan kallas gruppen EAAGroup och har en medlem.

    ![Konfigurera Akamai](./media/header-akamai-tutorial/eaagroup.png)

1. Lägg till katalogen i identitets leverantören genom att klicka på **identitets**  >  **identitets leverantörer** och på fliken **kataloger** och klicka på **tilldela katalog**.

    ![Konfigurera Akamai](./media/header-akamai-tutorial/assigndirectory.png)

### <a name="configure-kcd-delegation-for-eaa-walkthrough"></a>Konfigurera KCD delegering för EAA-genom gång

#### <a name="step-1-create-an-account"></a>Steg 1: skapa ett konto 

1. I exemplet ska vi använda ett konto som heter **EAADelegation**. Du kan göra detta med hjälp av **Active Directory användare och dator** fästning.

    ![Konfigurera Akamai](./media/header-akamai-tutorial/assigndirectory.png)

    > [!NOTE]
    > Användar namnet måste vara i ett särskilt format baserat på **identitets spärrens namn**. Från bild 1 ser vi att det är **corpapps.login.go.Akamai-Access.com**

1. Användarens inloggnings namn blir:`HTTP/corpapps.login.go.akamai-access.com`

    ![Konfigurera Akamai](./media/header-akamai-tutorial/eaadelegation.png)

#### <a name="step-2-configure-the-spn-for-this-account"></a>Steg 2: Konfigurera SPN för det här kontot

1. Baserat på det här exemplet är SPN-namnet enligt nedan.

2. Setspn-s **http/corpapps. Logi. go. Akamai-Access. com eaadelegation**

    ![Konfigurera Akamai](./media/header-akamai-tutorial/spn.png)

#### <a name="step-3-configure-delegation"></a>Steg 3: Konfigurera delegering

1. Klicka på fliken delegering för EAADelegation-kontot.

    ![Konfigurera Akamai](./media/header-akamai-tutorial/spn.png)

    * Ange Använd valfritt autentiseringsprotokoll
    * Klicka på Lägg till och Lägg till app pool-kontot för Kerberos-webbplatsen. Den bör automatiskt matcha till rätt SPN om den är korrekt konfigurerad.

#### <a name="step-4-create-a-keytab-file-for-akamai-eaa"></a>Steg 4: skapa en Keytab-fil för AKAMAI-EAA

1. Här är den allmänna syntaxen.

1. ktpass/out ActiveDirectorydomain. keytab/Princ `HTTP/yourloginportalurl@ADDomain.com`  /mapuser serviceaccount@ADdomain.com /pass + RdnPass/crypto alla/ptype KRB5_NT_PRINCIPAL

1. Exempel på förklaring

    | Kodfragment | Förklaring |
    | - | - |
    | Ktpass/out EAADemo. keytab | Namnet på den utgående Keytab-filen |
    | /princ HTTP/corpapps.login.go.akamai-access.com@superdemo.live | // HTTP/yourIDPName@YourdomainName |
    | /mapuser eaadelegation@superdemo.live | Delegerings konto i EAA |
    | /pass RANDOMPASS | Konto lösen ord för EAA-delegering |
    | /crypto alla ptype KRB5_NT_PRINCIPAL | Läs dokumentationen om Akamai-EAA |
    | | |

1. Ktpass/out EAADemo. keytab/Princ HTTP/corpapps.login.go.akamai-access.com@superdemo.live /mapuser eaadelegation@superdemo.live /pass RANDOMPASS/crypto alla ptype KRB5_NT_PRINCIPAL

    ![Konfigurera Akamai](./media/header-akamai-tutorial/administrator.png)

#### <a name="step-5-import-keytab-in-the-akamai-eaa-console"></a>Steg 5: importera Keytab i AKAMAIs EAA-konsolen

1. Klicka på fliken **system**-  >  **tabbar**.

    ![Konfigurera Akamai](./media/header-akamai-tutorial/keytabs.png)

1. I Keytab-typen väljer du **Kerberos-delegering**.

    ![Konfigurera Akamai](./media/header-akamai-tutorial/keytabdelegation.png)

1. Se till att Keytab visas som distribuerad och verifierad.

    ![Konfigurera Akamai](./media/header-akamai-tutorial/keytabs02.png)

1. Användarupplevelse

    ![Konfigurera Akamai](./media/header-akamai-tutorial/enduser03.png)

    ![Konfigurera Akamai](./media/header-akamai-tutorial/enduser04.png)

1. Villkorlig åtkomst

    ![Konfigurera Akamai](./media/header-akamai-tutorial/conditionalaccess04.png)

    ![Konfigurera Akamai](./media/header-akamai-tutorial/conditionalaccess10.png)

    ![Konfigurera Akamai](./media/header-akamai-tutorial/conditionalaccess11.png)

### <a name="create-akamai-test-user"></a>Skapa Akamai test användare

I det här avsnittet skapar du en användare som heter B. Simon i Akamai. Arbeta med [Akamai-klientens support team](https://www.akamai.com/us/en/contact-us/) för att lägga till användare i Akamai-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning. 

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Akamai på åtkomst panelen, bör du loggas in automatiskt på den Akamai som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Akamai med Azure AD](https://aad.portal.azure.com/)
