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
ms.date: 02/04/2021
ms.author: jeedes
ms.openlocfilehash: a22a214104357b9ad99238a8db157839a1c9fd46
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104591953"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-akamai"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Akamai

I den här självstudien får du lära dig hur du integrerar Akamai med Azure Active Directory (Azure AD). När du integrerar Akamai med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Akamai.
* Gör det möjligt för användarna att logga in automatiskt till Akamai med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Med Azure Active Directory-och Akamai Enterprise Application Access integration får du sömlös åtkomst till äldre program som finns i molnet eller lokalt. Den integrerade lösningen drar nytta av alla moderna funktioner i Azure Active Directory som [Azure AD villkorlig åtkomst](../conditional-access/overview.md), [Azure AD Identity Protection](../identity-protection/overview-identity-protection.md) och [Azure AD Identity Governance](../governance/identity-governance-overview.md) för äldre program åtkomst utan app-ändringar eller agent installation.

I bilden nedan beskrivs var Akamai EAA passar i det bredare hybrid scenariot för säker åtkomst.

![Akamai EAA passar i det bredare scenariot med hybrid säker åtkomst](./media/header-akamai-tutorial/introduction-1.png)

### <a name="key-authentication-scenarios"></a>Scenarier för nyckel autentisering

Förutom Azure Active Directory inbyggt integrerings stöd för moderna autentiseringsprotokoll som öppen ID Connect, SAML och WS-utfodras, utökar Akamai i EAA säker åtkomst för äldre-baserade autentiseringscertifikat för både intern och extern åtkomst med Azure AD, vilket möjliggör moderna scenarier (t. ex. lösen ords lös åtkomst) till dessa program. Du måste bland annat:

* Huvudbaserade autentiseringsmekanismer
* Fjärrskrivbord
* SSH (Secure Shell)
* Kerberos-autentisering av appar
* VNC (Virtual Network data behandling)
* Anonym autentisering eller inga inbyggta autentiseringscertifikat
* NTLM-autentisering av appar (skydd med dubbla prompter för användaren)
* Forms-Based program (skydd med dubbla prompter för användaren)

### <a name="integration-scenarios"></a>Integrations scenarier

Microsoft och Akamai EAA-partnerskap ger flexibiliteten att uppfylla dina affärs behov genom att stödja flera integrerings scenarier baserat på ditt företags behov. Dessa kan användas för att ge täckning på noll dagar för alla program och gradvis klassificera och konfigurera lämpliga princip klassificeringar.

#### <a name="integration-scenario-1"></a>Integrations scenario 1

Akamai EAA konfigureras som ett enda program i Azure AD. Administratören kan konfigurera principen för villkorlig åtkomst i programmet och när villkoren är uppfyllda kan användarna få åtkomst till Akamai EAA-portalen.

- **Proffs**:

* Du behöver bara konfigurera IDP en gång.

**Nack delar**:

* Användare har slut på två program portaler.

* Enskild gemensam princip täckning för villkorlig åtkomst för alla program.

![Integrations scenario 1](./media/header-akamai-tutorial/scenario-1.png)

#### <a name="integration-scenario-2"></a>Integrations scenario 2

Akamai EAA-programmet konfigureras separat på Azure AD-portalen. Admin kan konfigurera enskilda principer för villkorlig åtkomst för program och när villkoren är uppfyllda kan användare omdirigeras direkt till det specifika programmet.

- **Proffs**:

* Du kan definiera enskilda CA-principer.

* Alla appar visas på panelen 0365 Rutmärket och myApps.microsoft.com.


**Nack delar**:

* Du måste konfigurera flera IDP.

![Integrations scenario 2](./media/header-akamai-tutorial/scenario-2.png)

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Akamai för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

- Akamai stöder IDP initierad SSO.

#### <a name="important"></a>Viktigt

Alla inställningar som anges nedan är desamma för **integrerings scenario 1** och **Scenario 2**. För **integrerings scenario 2** måste du konfigurera enskilda IDP i Akamai-EAA och URL-egenskapen måste ändras så att den pekar på programmets URL.

![Skärm bild av fliken Allmänt för AZURESSO-SP i Akamai Enterprise Application Access. URL-fältet för autentiserings konfiguration är markerat.](./media/header-akamai-tutorial/important.png)

## <a name="add-akamai-from-the-gallery"></a>Lägg till Akamai från galleriet

Om du vill konfigurera integreringen av Akamai i Azure AD måste du lägga till Akamai från galleriet i listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **Akamai** i sökrutan.
1. Välj **Akamai** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-akamai"></a>Konfigurera och testa Azure AD SSO för Akamai

Konfigurera och testa Azure AD SSO med Akamai med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Akamai.

Utför följande steg för att konfigurera och testa Azure AD SSO med Akamai:

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

1. I Azure Portal går du till sidan för program integrering i **Akamai** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

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

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare** och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Akamai.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **Akamai**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-akamai-sso"></a>Konfigurera Akamai SSO

### <a name="setting-up-idp"></a>Konfigurera IDP

**Konfiguration av AKAMAI EAA-IDP**

1. Logga in på **Akamai Enterprise Application Access** Console.
1. I **Akamais EAA-konsolen** väljer du **identitets**  >  **identitets leverantörer** och klickar på **Lägg till identitetsprovider**.

    ![Skärm bild av fönstret Akamai i EAA-konsolens identitets leverantörer. Välj identitets leverantörer på menyn identitet och välj Lägg till identitets leverantör.](./media/header-akamai-tutorial/configure-1.png)

1. Utför följande steg på sidan **Skapa ny identitetsprovider** :

    ![Skärm bild av dialog rutan skapa nya identitets leverantörer i Akamai EAA-konsolen.](./media/header-akamai-tutorial/configure-2.png)

    a. Ange det **unika namnet**.

    b. Välj **SAML från tredje part** och klicka på **skapa identitetsprovider och konfigurera**.

### <a name="general-settings"></a>Allmänna inställningar

1. **Identitets spärr** – ange namnet på (SP Base URL – kommer att användas för Azure AD-konfiguration).

    > [!NOTE]
    > Du kan välja att ha en egen anpassad domän (kräver en DNS-post och ett certifikat). I det här exemplet ska vi använda Akamai-domänen.

1. **Akamai-moln zon** – Välj lämplig moln zon.
1. **Certifikat validering** -kontrol lera Akamai-dokumentationen (valfritt).

    ![Skärm bild av fliken Allmänt i Akamai i EAA-konsolen med inställningar för identitets spärr, Akamai Cloud Zone och certifikat validering.](./media/header-akamai-tutorial/configure-3.png)

### <a name="authentication-configuration"></a>Konfiguration av autentisering

1. URL – ange URL-adressen till samma som din identitets spärr (det är där användare omdirigeras efter autentisering).
2. Logga ut URL: uppdatera utloggnings-URL: en.
3. Signera SAML-begäran: standardvärdet är avmarkerat.
4. Lägg till programmet i Azure AD-konsolen för IDP metadata-fil.

    ![Skärm bild av Akamai i EAA-konsolen som visar inställningar för URL, utloggnings-URL, signera SAML-begäran och IDP-metadatafil.](./media/header-akamai-tutorial/configure-4.png)

### <a name="session-settings"></a>Sessionsinställningar

Lämna inställningarna som standard.

![Skärm bild av dialog rutan inställningar för Akamai i EAA-konsolen.](./media/header-akamai-tutorial/session-settings.png)

### <a name="directories"></a>Kataloger

Hoppa över katalog konfigurationen.

![Skärm bild av fliken Akamai i EAA-konsolen.](./media/header-akamai-tutorial/directories.png)

### <a name="customization-ui"></a>Anpassnings gränssnitt

Du kan lägga till anpassning till IDP.

![Skärm bild av fliken anpassning av Akamai i EAA-konsolen med inställningar för anpassa användar gränssnitt, språk inställningar och teman.](./media/header-akamai-tutorial/customization.png)

### <a name="advanced-settings"></a>Avancerade inställningar

Hoppa över avancerade inställningar/Se Akamai-dokumentationen för mer information.

![Skärm bild av fliken Avancerade inställningar i Akamai i EAA-konsolen med inställningar för EAA-klient, Avancerat och OIDC till SAML-bryggning.](./media/header-akamai-tutorial/advance-settings.png)

### <a name="deployment"></a>Distribution

1. Klicka på distribuera identitets leverantör.

    ![Skärm bild av fliken distribution av Akamai-EAA-konsolen som visar knappen för att distribuera indrags leverantören.](./media/header-akamai-tutorial/deployment.png)

2. Verifiera att distributionen har slutförts.

### <a name="header-based-authentication"></a>Rubrik baserad autentisering

Akamai-huvudbaserad autentisering

1. Välj **anpassat HTTP-** formulär guiden Lägg till program.

    ![Skärm bild av guiden Lägg till program i Akamai i EAA-konsolen visas CustomHTTP som anges i avsnittet åtkomst till appar.](./media/header-akamai-tutorial/configure-5.png)

2. Ange **program namn** och **Beskrivning**.

    ![Skärm bild av dialog rutan anpassad HTTP-app med inställningar för program namn och beskrivning.](./media/header-akamai-tutorial/configure-6.png)

    ![Skärm bild av fliken Allmänt i Akamais EAA-konsolen med allmänna inställningar för MYHEADERAPP.](./media/header-akamai-tutorial/configure-7.png)

    ![Skärm bild av Akamai EAA-konsolen med inställningar för certifikat och plats.](./media/header-akamai-tutorial/configure-8.png)

#### <a name="authentication"></a>Autentisering

1. Välj fliken **autentisering** .

    ![Skärm bild av Akamai EAA-konsolen med fliken autentisering vald.](./media/header-akamai-tutorial/configure-9.png)

2. Tilldela **identitets leverantören**.

    ![Skärm bild av fliken Akamai i EAA-konsolen för MYHEADERAPP som visar identitets leverantören som är inställd på Azure AD SSO.](./media/header-akamai-tutorial/configure-10.png)

#### <a name="services"></a>Tjänster

Klicka på Spara och gå till autentisering.

![Skärm bild av fliken Akamai i EAA-konsolen för MYHEADERAPP som visar knappen Spara och gå till AdvancedSettings i det nedre högra hörnet.](./media/header-akamai-tutorial/configure-11.png)

#### <a name="advanced-settings"></a>Avancerade inställningar

1. Under **kundens HTTP-rubriker** anger du attributet **CustomerHeader** och **SAML**.

    ![Skärm bild av fliken Avancerade inställningar för Akamai i EAA-konsolen som visar fältet SSO protokollförd URL markerat under autentisering.](./media/header-akamai-tutorial/configure-12.png)

1. Klicka på **Spara och gå till distributions** knapp.

    ![Skärm bild av fliken Avancerade inställningar för Akamai i EAA-konsolen som visar knappen Spara och gå till distribution i det nedre högra hörnet.](./media/header-akamai-tutorial/configure-13.png)

#### <a name="deploy-the-application"></a>Distribuera programmet

1. Klicka på knappen för att **distribuera program** .

    ![Skärm bild av fliken distribution av Akamai-EAA-konsolen som visar knappen distribuera program.](./media/header-akamai-tutorial/configure-14.png)

1. Verifiera att programmet har distribuerats.

    ![Skärm bild av fliken distribution av Akamai i EAA-konsolen som visar programmets status meddelande: "programmet har distribuerats".](./media/header-akamai-tutorial/configure-15.png)

1. End-User upplevelse.

    ![Skärm bild av den öppna skärmen för myapps.microsoft.com med en bakgrunds bild och en inloggnings dialog ruta.](./media/header-akamai-tutorial/end-user-1.png)

    ![Skärm bild som visar en del av ett Apps-fönster med ikoner för tillägg, HRWEB, Akamai-CorpApps, utgifter, grupper och åtkomst granskningar. ](./media/header-akamai-tutorial/end-user-2.png)

1. Villkorlig åtkomst.

    ![Skärm bild av meddelandet: Godkänn inloggnings förfrågan. Vi har skickat ett meddelande till din mobila enhet. Fortsätt genom att svara.](./media/header-akamai-tutorial/conditional-access-1.png)

    ![Skärm bild av en program skärm som visar en ikon för MyHeaderApp.](./media/header-akamai-tutorial/conditional-access-2.png)

#### <a name="remote-desktop"></a>Fjärrskrivbord

1. Välj **RDP** i guiden Lägg till program.

    ![Skärm bild av guiden Lägg till program i Akamai i EAA-konsolen som visar RDP i listan med appar i avsnittet åtkomst appar.](./media/header-akamai-tutorial/configure-16.png)

1. Ange **program namn** och **Beskrivning**.

    ![Skärm bild av dialog rutan för RDP-appar som visar inställningar för program namn och beskrivning.](./media/header-akamai-tutorial/configure-17.png)

    ![Skärm bild av fliken Allmänt i Akamais EAA-konsolen med program identitets inställningar för SECRETRDPAPP.](./media/header-akamai-tutorial/configure-18.png)

1. Ange den anslutning som ska betjäna detta.

    ![Skärm bild av Akamai EAA-konsolen med inställningar för certifikat och plats. Associerade kopplingar är inställt på USWST-CON1.](./media/header-akamai-tutorial/configure-19.png)

#### <a name="authentication"></a>Autentisering

Klicka på **Spara och gå till tjänster**.

![Skärm bild av fliken Akamai i EAA-konsolen för SECRETRDPAPP som visar knappen Spara och gå till tjänster visas i det nedre högra hörnet.](./media/header-akamai-tutorial/configure-20.png)

#### <a name="services"></a>Tjänster

Klicka på **Spara och gå till avancerade inställningar**.

![Skärm bild av fliken Akamai i EAA-konsolen för SECRETRDPAPP som visar knappen Spara och gå till AdvancedSettings i det nedre högra hörnet.](./media/header-akamai-tutorial/configure-21.png)

#### <a name="advanced-settings"></a>Avancerade inställningar

1. Klicka på **Spara och gå till distribution**.

    ![Skärm bild av fliken Avancerade inställningar för Akamai i EAA-konsolen för SECRETRDPAPP som visar inställningarna för fjärr skrivbords konfiguration.](./media/header-akamai-tutorial/configure-22.png)

    ![Skärm bild av fliken Avancerade inställningar för Akamai i EAA-konsolen för SECRETRDPAPP som visar inställningarna för konfiguration av autentisering och hälso kontroll.](./media/header-akamai-tutorial/configure-23.png)

    ![Skärm bild av inställningarna för anpassade HTTP-huvuden för Akamai i EAA-konsolen för SECRETRDPAPP med knappen Spara och gå till distribution i det nedre högra hörnet.](./media/header-akamai-tutorial/configure-24.png)

1. End-User upplevelse

    ![Skärm bild av ett myapps.microsoft.com-fönster med en bakgrunds bild och en inloggnings dialog ruta.](./media/header-akamai-tutorial/end-user-3.png)

    ![Skärm bild av myapps.microsoft.com apps-fönstret med ikoner för tillägg, HRWEB, Akamai-CorpApps, utgifter, grupper och åtkomst granskningar.](./media/header-akamai-tutorial/end-user-2.png)

1. Villkorlig åtkomst

    ![Skärm bild av meddelandet för villkorlig åtkomst: Godkänn inloggnings förfrågan. Vi har skickat ett meddelande till din mobila enhet. Fortsätt genom att svara.](./media/header-akamai-tutorial/conditional-access-4.png)

    ![Skärm bild av en program skärm som visar ikoner för MyHeaderApp och SecretRDPApp.](./media/header-akamai-tutorial/conditional-access-5.png)

    ![Skärm bild av Windows Server 2012 RS-skärm som visar allmänna användar ikoner. Ikonerna för administratör, user0 och Användare1 visar att de är inloggade.](./media/header-akamai-tutorial/conditional-access-6.png)

1. Du kan också ange URL: en för RDP-programmet direkt.

#### <a name="ssh"></a>SSH

1. Gå till Lägg till program och välj **SSH**.

    ![Skärm bild av guiden Lägg till program i Akamai i EAA-konsolen som visar SSH som visas bland apparna i avsnittet åtkomst appar.](./media/header-akamai-tutorial/configure-25.png)

1. Ange **program namn** och **Beskrivning**.

    ![Skärm bild av dialog rutan SSH app som visar inställningar för program namn och beskrivning.](./media/header-akamai-tutorial/configure-26.png)

1. Konfigurera program identitet.

    ![Skärm bild av fliken Allmänt i Akamais EAA-konsolen med program identitets inställningar för SSH-SECURE.](./media/header-akamai-tutorial/configure-27.png)

    a. Ange namn/beskrivning.

    b. Ange program serverns IP/FQDN och port för SSH.

    c. Ange SSH-användarnamn/lösen fras * kontrol lera Akamai EAA.

    d. Ange det externa värd namnet.

    e. Ange platsen för anslutningen och välj kopplingen.

#### <a name="authentication"></a>Autentisering

Klicka på **Spara och gå till tjänster**.

![Skärm bild av fliken Akamai i EAA-konsolen för SSH-SECURE som visar knappen Spara och gå till tjänster finns i det nedre högra hörnet.](./media/header-akamai-tutorial/configure-28.png)

#### <a name="services"></a>Tjänster

Klicka på **Spara och gå till avancerade inställningar**.

![Skärm bild av fliken Akamai i EAA-konsolen för SSH-SECURE som visar knappen Spara och gå till AdvancedSettings i det nedre högra hörnet.](./media/header-akamai-tutorial/configure-29.png)

#### <a name="advanced-settings"></a>Avancerade inställningar

Klicka på Spara och to go-distribution.

![Skärm bild av fliken Avancerade inställningar för Akamai i EAA-konsolen för SSH-SECURE som visar inställningarna för konfiguration av autentisering och hälso kontroll.](./media/header-akamai-tutorial/configure-30.png)

![Skärm bild av inställningarna för anpassade HTTP-huvuden för Akamai i EAA-konsolen för SSH-SECURE med knappen Spara och gå till distribution i det nedre högra hörnet.](./media/header-akamai-tutorial/configure-31.png)

#### <a name="deployment"></a>Distribution

1. Klicka på **distribuera program**.

    ![Skärm bild av fliken distribution av Akamai i EAA-konsolen för SSH-SECURE som visar knappen distribuera program.](./media/header-akamai-tutorial/configure-32.png)

1. End-User upplevelse

    ![Skärm bild av dialog rutan logga in i ett myapps.microsoft.com-fönster.](./media/header-akamai-tutorial/end-user-3.png)

    ![Skärm bild av fönstret appar för myapps.microsoft.com som visar ikoner för tillägg, HRWEB, Akamai-CorpApps, utgifter, grupper och åtkomst granskningar.](./media/header-akamai-tutorial/end-user-4.png)

1. Villkorlig åtkomst

    ![Skärm bild som visar meddelandet: Godkänn inloggnings förfrågan. Vi har skickat ett meddelande till din mobila enhet. Fortsätt genom att svara.](./media/header-akamai-tutorial/conditional-access-4.png)

    ![Skärm bild av en program skärm som visar ikoner för MyHeaderApp, SSH Secure och SecretRDPApp.](./media/header-akamai-tutorial/conditional-access-7.png)

    ![Skärm bild av ett kommando fönster för ssh-secure-go.akamai-access.com som visar en lösen ords prompt.](./media/header-akamai-tutorial/conditional-access-8.png)

    ![Skärm bild av ett kommando fönster för ssh-secure-go.akamai-access.com som visar information om programmet och som visar en kommando tolk.](./media/header-akamai-tutorial/conditional-access-9.png)

### <a name="kerberos-authentication"></a>Kerberos-autentisering

I exemplet nedan kommer vi att publicera en intern webb server <code>http://frp-app1.superdemo.live</code> och aktivera SSO med hjälp av KCD.

#### <a name="general-tab"></a>Fliken Allmänt

![Skärm bild av fliken Allmänt i Akamai i EAA-konsolen för MYKERBOROSAPP.](./media/header-akamai-tutorial/general-tab.png)

#### <a name="authentication-tab"></a>Fliken Autentisering

Tilldela identitets leverantören.

![Skärm bild av fliken Akamai i EAA-konsolen för MYKERBOROSAPP som visar att identitetsprovider har angetts till Azure AD SSO.](./media/header-akamai-tutorial/authentication-tab.png)

#### <a name="services-tab"></a>Fliken tjänster

![Skärm bild av fliken Akamai i EAA-konsolen för MYKERBOROSAPP.](./media/header-akamai-tutorial/services-tab.png)

#### <a name="advanced-settings"></a>Avancerade inställningar

![Skärm bild av fliken Avancerade inställningar i Akamai i EAA-konsolen för MYKERBOROSAPP som visar inställningar för relaterade program och autentisering.](./media/header-akamai-tutorial/advance-settings-2.png)

> [!NOTE]
> SPN för webb servern har SPN@Domain formatet t `HTTP/frp-app1.superdemo.live@SUPERDEMO.LIVE` . ex.: för den här demon. Lämna kvar resten av inställningarna till standard.

#### <a name="deployment-tab"></a>Fliken distribution

![Skärm bild av fliken distribution av Akamai-EAA-konsolen för MYKERBOROSAPP som visar knappen distribuera program.](./media/header-akamai-tutorial/deployment-tab.png)

#### <a name="adding-directory"></a>Lägger till katalog

1. Välj **AD** i list rutan.

    ![Skärm bild av fönstret Akamais fönstret i EAA-konsol med en dialog ruta för att skapa ny katalog med AD valt i list rutan för katalog typ.](./media/header-akamai-tutorial/configure-33.png)

1. Ange nödvändiga data.

    ![Skärm bild av SUPERDEMOLIVE-fönstret för Akamai i EAA-konsolen med inställningar för mappning av DirectoryName, katalog tjänst, anslutning och attribut.](./media/header-akamai-tutorial/configure-34.png)

1. Verifiera skapandet av katalogen.

    ![Skärm bild av fönstret Akamais fönstret i EAA-konsolen som visar att katalogen superdemo.live har lagts till.](./media/header-akamai-tutorial/directory-domain.png)

1. Lägg till de grupper/organisationsenheter som behöver åtkomst.

    ![Skärm bild av inställningarna för katalogen superdemo.live. Ikonen som du väljer för att lägga till grupper eller organisationsenheter är markerad.](./media/header-akamai-tutorial/add-group.png)

1. I listan nedan kallas gruppen EAAGroup och har en medlem.

    ![Skärm bild av Akamaia EAA-konsol grupper i SUPERDEMOLIVE DIRECTORY-fönstret. EAAGroup med 1 användare visas under grupper.](./media/header-akamai-tutorial/eaagroup.png)

1. Lägg till katalogen i identitets leverantören genom att klicka på **identitets**  >  **identitets leverantörer** och på fliken **kataloger** och klicka på **tilldela katalog**.

    ![Skärm bild av fliken Akamai i EAA-konsolen för Azure AD SSO, som visar superdemo.live i listan över för tillfället tilldelade kataloger.](./media/header-akamai-tutorial/assign-directory.png)

### <a name="configure-kcd-delegation-for-eaa-walkthrough"></a>Konfigurera KCD delegering för EAA-genom gång

#### <a name="step-1-create-an-account"></a>Steg 1: skapa ett konto 

1. I exemplet ska vi använda ett konto som heter **EAADelegation**. Du kan göra detta med hjälp av **Active Directory användare och dator** fästning.

    ![Skärm bild av fliken Akamai i EAA-konsolen för Azure AD SSO. Katalogen superdemo.live visas under för tillfället tilldelade kataloger.](./media/header-akamai-tutorial/assign-directory.png)

    > [!NOTE]
    > Användar namnet måste vara i ett särskilt format baserat på **identitets spärrens namn**. Från bild 1 ser vi att det är **corpapps.login.go.Akamai-Access.com**

1. Användarens inloggnings namn blir:`HTTP/corpapps.login.go.akamai-access.com`

    ![Skärm bild som visar EAADelegation-egenskaper med förnamnet inställt på "EAADelegation" och användarens inloggnings namn angivet till HTTP/corpapps. Logi. go. Akamai-Access. com.](./media/header-akamai-tutorial/eaadelegation.png)

#### <a name="step-2-configure-the-spn-for-this-account"></a>Steg 2: Konfigurera SPN för det här kontot

1. Baserat på det här exemplet är SPN-namnet enligt nedan.

2. Setspn-s **http/corpapps. Logi. go. Akamai-Access. com eaadelegation**

    ![Skärm bild av en administratörs kommando tolk som visar resultatet av kommandot Setspn-s http/corpapps. Logi. go. Akamai-Access. com eaadelegation.](./media/header-akamai-tutorial/spn.png)

#### <a name="step-3-configure-delegation"></a>Steg 3: Konfigurera delegering

1. Klicka på fliken delegering för EAADelegation-kontot.

    ![Skärm bild av en administratörs kommando tolk som visar kommandot för att konfigurera SPN.](./media/header-akamai-tutorial/spn.png)

    * Ange Använd valfritt autentiseringsprotokoll.
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

    ![Skärm bild av en administratörs kommando tolk som visar resultatet av kommandot för att skapa en Keytab-fil för AKAMAI-EAA.](./media/header-akamai-tutorial/administrator.png)

#### <a name="step-5-import-keytab-in-the-akamai-eaa-console"></a>Steg 5: importera Keytab i AKAMAIs EAA-konsolen

1. Klicka på fliken **system**-  >  **tabbar**.

    ![Skärm bild av Akamai EAA-konsolen som visar de flikar som väljs på system menyn.](./media/header-akamai-tutorial/keytabs.png)

1. I Keytab-typen väljer du **Kerberos-delegering**.

    ![Skärm bild av Akamai-EAAKEYTAB för EAA-konsolen med Keytab-inställningarna. Keytab-typen är inställt på Kerberos-delegering.](./media/header-akamai-tutorial/keytab-delegation.png)

1. Se till att Keytab visas som distribuerad och verifierad.

    ![Skärm bild av skärmen med Akamai i EAA-konsolen som visar EAA-Keytab som "Keytab distribuerad och verifierad".](./media/header-akamai-tutorial/keytabs-2.png)

1. Användarupplevelse

    ![Skärm bild av inloggnings dialog rutan på myapps.microsoft.com. ](./media/header-akamai-tutorial/end-user-3.png)

    ![Skärm bild av fönstret appar för myapps.microsoft.com som visar ikoner för appar.](./media/header-akamai-tutorial/end-user-4.png)

1. Villkorlig åtkomst

    ![Skärm bild som visar meddelandet Godkänn inloggnings förfrågan. meddelandet.](./media/header-akamai-tutorial/conditional-access-4.png)

    ![Skärm bild av en program skärm som visar ikoner för MyHeaderApp, SSH Secure, SecretRDPApp och myKerberosApp.](./media/header-akamai-tutorial/conditional-access-10.png)

    ![Skärm bild av välkomst skärmen för myKerberosApp. Meddelandet "Välkommen superdemo\user1" visas över en bakgrunds bild.](./media/header-akamai-tutorial/conditional-access-11.png)

### <a name="create-akamai-test-user"></a>Skapa Akamai test användare

I det här avsnittet skapar du en användare som heter B. Simon i Akamai. Arbeta med [Akamai-klientens support team](https://www.akamai.com/us/en/contact-us/) för att lägga till användare i Akamai-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning. 

## <a name="test-sso"></a>Testa SSO

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ.

* Klicka på testa det här programmet i Azure Portal så bör du loggas in automatiskt på den Akamai som du ställer in SSO för.

* Du kan använda Microsoft Mina appar. När du klickar på panelen Akamai i Mina appar, bör du loggas in automatiskt på den Akamai som du ställer in SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Akamai kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).