---
title: Atlassian JIRA/Confluence admin guide – Azure Active Directory | Microsoft Docs
description: Administratörs guide för att använda Atlassian JIRA och Confluence med Azure Active Directory (Azure AD).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 11/19/2018
ms.author: jeedes
ms.openlocfilehash: 07bd9353f08e9440e495dfa92c608b4d6f70f5cf
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88543990"
---
# <a name="atlassian-jira-and-confluence-admin-guide-for-azure-active-directory"></a>Atlassian JIRA och Confluence admin guide för Azure Active Directory

## <a name="overview"></a>Översikt

Plugin-programmet Azure Active Directory (enkel inloggning) i (SSO) gör att Microsoft Azure AD kunder kan använda sitt arbets-eller skol konto för att logga in på Atlassian JIRA-och Confluence Server-baserade produkter. Den implementerar SAML 2,0-baserad SSO.

## <a name="how-it-works"></a>Så här fungerar det

När användarna vill logga in på Atlassian-JIRA eller Confluence-programmet, ser de **inloggningen med Azure AD** -knappen på inloggnings sidan. När de väljer det måste de logga in med hjälp av inloggnings sidan för Azure AD-organisation (det vill säga sitt arbets-eller skol konto).

När användarna har autentiserats bör de kunna logga in på programmet. Om de redan har autentiserats med ID och lösen ord för sitt arbets-eller skol konto loggar de direkt in i programmet. 

Inloggning fungerar över JIRA och Confluence. Om användarna är inloggade i JIRA-programmet och Confluence öppnas i samma webbläsarfönster, behöver de inte ange autentiseringsuppgifterna för den andra appen. 

Användarna kan också komma åt Atlassian-produkten via Mina appar under arbets-eller skol kontot. De bör vara inloggade utan att behöva ange autentiseringsuppgifter.

> [!NOTE]
> Användar etablering görs inte via plugin-programmet.

## <a name="audience"></a>Målgrupp

JIRA-och Confluence-administratörer kan använda plugin-programmet för att aktivera SSO med hjälp av Azure AD.

## <a name="assumptions"></a>Antaganden

* JIRA-och Confluence-instanser är HTTPS-aktiverade.
* Användare har redan skapats i JIRA eller Confluence.
* Användare har roller tilldelade i JIRA eller Confluence.
* Administratörer har till gång till information som krävs för att konfigurera plugin-programmet.
* JIRA eller Confluence är även tillgängligt utanför företags nätverket.
* Plugin-programmet fungerar bara med den lokala versionen av JIRA och Confluence.

## <a name="prerequisites"></a>Förutsättningar

Observera följande information innan du installerar plugin-programmet:

* JIRA och Confluence är installerade på en Windows 64-bitars version.
* JIRA-och Confluence-versionerna är HTTPS-aktiverade.
* JIRA och Confluence är tillgängliga på Internet.
* Autentiseringsuppgifter för admin finns på plats för JIRA och Confluence.
* Admin-autentiseringsuppgifter är placerade för Azure AD.
* WebSudo har inaktiverats i JIRA och Confluence.

## <a name="supported-versions-of-jira-and-confluence"></a>Versioner av JIRA och Confluence som stöds

Plugin-programmet har stöd för följande versioner av JIRA och Confluence:

* JIRA Core och program vara: 6,0 till 7,12
* JIRA Service Desk: 3.0.0 till 3.5.0
* JIRA stöder också 5.2. Om du vill ha mer information klickar du på [Microsoft Azure Active Directory single sign-on for JIRA 5.2](https://docs.microsoft.com/azure/active-directory/saas-apps/jira52microsoft-tutorial) (Microsoft Azure Active Directory enkel inloggning för JIRA 5.2)
* Confluence: 5,0 till 5,10
* Confluence: 6.0.1
* Confluence: a
* Confluence: 6.2.1
* Confluence: 6.3.4
* Confluence: 6.4.0
* Confluence: 6.5.0
* Confluence: 6.6.2
* Confluence: 6.7.0
* Confluence: 6.8.1
* Confluence: 6.9.0
* Confluence: 6.10.0
* Confluence: 6.11.0
* Confluence: 6.12.0

## <a name="installation"></a>Installation

Följ dessa steg om du vill installera plugin-programmet:

1. Logga in på JIRA-eller Confluence-instansen som administratör.

2. Gå till administrations konsolen för JIRA/Confluence och välj **-** tillägg.

3. Från Microsoft Download Center hämtar du [Microsoft SAML SSO-plugin-programmet för JIRA](https://www.microsoft.com/download/details.aspx?id=56506) /  [Microsoft SAML SSO för Confluence](https://www.microsoft.com/download/details.aspx?id=56503).

   Lämplig version av plugin-programmet visas i Sök resultaten.

4. Välj plugin-programmet och den Universal plugin-hanteraren (UPM) installerar det.

När plugin-programmet har installerats visas det i avsnittet **användare installerade tillägg** i **Hantera tillägg**.

## <a name="plug-in-configuration"></a>Plugin-konfiguration

Innan du börjar använda plugin-programmet måste du konfigurera det. Välj plugin-programmet, Välj knappen **Konfigurera** och ange konfigurations informationen.

Följande bild visar konfigurations skärmen i både JIRA och Confluence:

![Konfigurations skärm för plugin-programmet](./media/ms-confluence-jira-plugin-adminguide/jira.png)

* **URL för metadata**: URL: en för att hämta federationsmetadata från Azure AD.

* **Identifierare**: den URL som Azure AD använder för att validera källan för begäran. Den mappas till **ID** -elementet i Azure AD. Plugin-programmet härleder automatiskt denna URL som https:// *\<domain:port>* /.

* **Svars-URL**: svars-URL: en i din identitets leverantör (IdP) som initierar SAML-inloggningen. Den mappas till **svars-URL** -elementet i Azure AD. Plugin-programmet härleder automatiskt denna URL som https:// *\<domain:port>* /plugins/servlet/SAML/auth.

* **Inloggnings-URL**: inloggnings-URL: en i din IDP som initierar SAML-inloggningen. Den mappas till **inloggnings** elementet i Azure AD. Plugin-programmet härleder automatiskt denna URL som https:// *\<domain:port>* /plugins/servlet/SAML/auth.

* **IDP entitets-ID**: entitets-ID: t som din IDP använder. Den här rutan fylls i när URL: en för metadata matchas.

* **Inloggnings-URL**: inloggnings-URL: en från din IDP. Den här rutan fylls i från Azure AD när URL: en för metadata matchas.

* **Utloggnings-URL**: utloggnings-URL: en från din IDP. Den här rutan fylls i från Azure AD när URL: en för metadata matchas.

* **X. 509-certifikat**: ditt IDP x. 509-certifikat. Den här rutan fylls i från Azure AD när URL: en för metadata matchas.

* **Namn på inloggnings knapp**: namnet på den inloggnings knapp som organisationen vill att användarna ska se på inloggnings sidan.

* **SAML-användar-ID-platser**: platsen där användar-ID för JIRA eller Confluence förväntas i SAML-svaret. Det kan vara i **NameID** eller i ett anpassat attributnamn.

* **Attributnamn**: namnet på attributet där användar-ID förväntas.

* **Aktivera identifiering av start sfär**: valet att göra om företaget använder Active Directory Federation Services (AD FS) (AD FS)-baserad inloggning.

* **Domän namn**: domän namnet om inloggning är AD FS baserat.

* **Aktivera enkel inloggning**: valet att göra om du vill logga ut från Azure AD när en användare loggar ut från JIRA eller Confluence.

## <a name="troubleshooting"></a>Felsökning

* **Du får flera certifikat fel**: Logga in på Azure AD och ta bort de flera certifikat som är tillgängliga mot appen. Se till att det bara finns ett certifikat.

* **Ett certifikat håller på att gå ut i Azure AD**:-tillägg tar hand om automatisk förnyelse av certifikatet. När ett certifikat håller på att gå ut måste ett nytt certifikat markeras som aktiva och oanvända certifikat ska tas bort. När en användare försöker logga in på JIRA i det här scenariot, hämtar plugin-programmet och sparar det nya certifikatet.

* **Du vill inaktivera WebSudo (inaktivera den säkra administratörs sessionen)**:

  * För JIRA är säkra administratörs sessioner (det vill säga lösen ords bekräftelse före åtkomst till administrations funktioner) aktiverade som standard. Om du vill ta bort den här möjligheten i JIRA-instansen anger du följande rad i JIRA-config. Properties-filen: `jira.websudo.is.disabled = true`

  * För Confluence följer du stegen på [Support webbplatsen för Confluence](https://confluence.atlassian.com/doc/configuring-secure-administrator-sessions-218269595.html).

* **Fält som ska fyllas i av metadata-URL: en är inte ifyllda**:

  * Kontrol lera att webb adressen är korrekt. Kontrol lera om du har mappat rätt klient-och app-ID.

  * Ange webb adressen i en webbläsare och se om du får XML-metadata för federationsmetadata.

* **Det finns ett internt Server fel**: Granska loggarna i logg katalogen i installationen. Om du får ett fel meddelande när användaren försöker logga in med hjälp av Azure AD SSO kan du dela loggarna med support teamet.

* **Fel meddelandet "Det gick inte att hitta användar-ID" visas när användaren försöker logga in**: skapa användar-ID: t i JIRA eller Confluence.

* **Det finns ett "app hittades inte"-fel i Azure AD**: se om rätt URL är mappad till appen i Azure AD.

* **Du behöver support**: kontakta [Azure AD SSO integration-teamet](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). Teamet svarar inom 24-48 arbets tid.

  Du kan också utlösa ett support ärende med Microsoft via Azure Portal-kanalen.

## <a name="plug-in-faq"></a>Vanliga frågor och svar om plugin

Se vanliga frågor och svar om du har frågor om det här plugin-programmet.

### <a name="what-does-the-plug-in-do"></a>Vad gör plugin-programmet?

Plugin-programmet tillhandahåller funktioner för enkel inloggning (SSO) för Atlassian JIRA (inklusive JIRA Core, JIRA Software, JIRA Service Desk) och Confluence lokala program. Plugin-programmet fungerar med Azure Active Directory (Azure AD) som en identitets leverantör (IdP).

### <a name="which-atlassian-products-does-the-plug-in-work-with"></a>Vilka Atlassian-produkter fungerar med plugin-programmet?

Plugin-programmet fungerar med lokala versioner av JIRA och Confluence.

### <a name="does-the-plug-in-work-on-cloud-versions"></a>Fungerar plugin-programmet på moln versioner?

Nej. Plugin-programmet har endast stöd för lokala versioner av JIRA och Confluence.

### <a name="which-versions-of-jira-and-confluence-does-the-plug-in-support"></a>Vilka versioner av JIRA och Confluence stöder plugin-programmet?

Plugin-programmet har stöd för dessa versioner:

* JIRA Core och program vara: 6,0 till 7,12
* JIRA Service Desk: 3.0.0 till 3.5.0
* JIRA stöder också 5.2. Om du vill ha mer information klickar du på [Microsoft Azure Active Directory single sign-on for JIRA 5.2](https://docs.microsoft.com/azure/active-directory/saas-apps/jira52microsoft-tutorial) (Microsoft Azure Active Directory enkel inloggning för JIRA 5.2)
* Confluence: 5,0 till 5,10
* Confluence: 6.0.1
* Confluence: a
* Confluence: 6.2.1
* Confluence: 6.3.4
* Confluence: 6.4.0
* Confluence: 6.5.0
* Confluence: 6.6.2
* Confluence: 6.7.0
* Confluence: 6.8.1
* Confluence: 6.9.0
* Confluence: 6.10.0
* Confluence: 6.11.0
* Confluence: 6.12.0

### <a name="is-the-plug-in-free-or-paid"></a>Är plugin-programmet kostnads fritt eller betalt?

Det är ett kostnads fritt tillägg.

### <a name="do-i-need-to-restart-jira-or-confluence-after-i-deploy-the-plug-in"></a>Måste jag starta om JIRA eller Confluence när jag har distribuerat plugin-programmet?

En omstart krävs inte. Du kan börja använda plugin-programmet direkt.

### <a name="how-do-i-get-support-for-the-plug-in"></a>Hur gör jag för att få support för plugin-programmet?

Du kan kontakta [Azure AD SSO integration-teamet](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) för all support som krävs för det här plugin-programmet. Teamet svarar inom 24-48 arbets tid.

Du kan också utlösa ett support ärende med Microsoft via Azure Portal-kanalen.

### <a name="would-the-plug-in-work-on-a-mac-or-ubuntu-installation-of-jira-and-confluence"></a>Fungerar plugin-programmet på en Mac-eller Ubuntu-installation av JIRA och Confluence?

Vi har testat bara plugin-programmet på 64-bitars Windows Server-installationer av JIRA och Confluence.

### <a name="does-the-plug-in-work-with-idps-other-than-azure-ad"></a>Fungerar plugin-programmet med IDP: er annat än Azure AD?

Nej. Det fungerar bara med Azure AD.

### <a name="what-version-of-saml-does-the-plug-in-work-with"></a>Vilken version av SAML fungerar plugin-programmet med?

Det fungerar med SAML 2,0.

### <a name="does-the-plug-in-do-user-provisioning"></a>Använder plugin-programmet användar etablering?

Nej. Plugin-programmet tillhandahåller endast SAML 2,0-baserad SSO. Användaren måste vara etablerad i programmet innan SSO-inloggningen.

### <a name="does-the-plug-in-support-cluster-versions-of-jira-and-confluence"></a>Stöder plugin-programmet kluster versioner av JIRA och Confluence?

Nej. Plugin-programmet fungerar med lokala versioner av JIRA och Confluence.

### <a name="does-the-plug-in-work-with-http-versions-of-jira-and-confluence"></a>Fungerar plugin-programmet med HTTP-versioner av JIRA och Confluence?

Nej. Plugin-programmet fungerar endast med HTTPS-aktiverade installationer.
