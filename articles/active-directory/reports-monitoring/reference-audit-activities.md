---
title: Referens för granskningsaktiviteter i Azure Active Directory (Azure AD) | Microsoft Docs
description: Få en översikt över vilka granskningsaktiviteter som kan loggas in dina granskningsloggar i Azure Active Directory (AD Azure).
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 01/24/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 56f9976faf2a803e747bce8b851a3555e6d746b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "89231154"
---
# <a name="azure-ad-audit-activity-reference"></a>Referens över granskningsaktiviteter i Azure AD

Med Azure Active Directory (Azure AD)-rapporter kan du få den information du behöver för att avgöra hur din miljö fungerar.

Rapporteringsarkitekturen i Azure AD består av följande komponenter:

- **Aktivitetsrapporter** 
    - [Inloggningar](concept-sign-ins.md) – ger information om användningen av hanterade program och användar inloggnings aktiviteter
    - [Gransknings loggar](concept-audit-logs.md) – ger spårning genom loggar för alla ändringar som gjorts av olika funktioner i Azure AD. 
    
- **Säkerhetsrapporter** 
    - [Riskfyllda inloggningar](../identity-protection/overview-identity-protection.md) – en riskfylld inloggning är en indikator för ett inloggnings försök som kan ha utförts av någon som inte är en legitim ägare till ett användar konto. 
    - [Användare som har flaggats för risk](../identity-protection/overview-identity-protection.md) – en riskfylld användare är en indikator för ett användar konto som kan ha komprometterats. 

I den här artikeln står vilka granskningsaktiviteter som kan loggas in dina granskningsloggar.

## <a name="access-reviews"></a>Åtkomstgranskningar

|Granskningskategori|Aktivitet|
|---|---|
|Åtkomstgranskningar|Åtkomstgranskning avslutades|
|Åtkomstgranskningar|Lägg till godkännare för begäran|
|Åtkomstgranskningar|Lägg till granskare i åtkomstgranskning|
|Åtkomstgranskningar|Tillämpa åtkomstgranskning|
|Åtkomstgranskningar|Skapa åtkomstgranskning|
|Åtkomstgranskningar|Skapa program|
|Åtkomstgranskningar|Skapa godkännande av begäran|
|Åtkomstgranskningar|Ta bort åtkomstgranskning|
|Åtkomstgranskningar|Ta bort program|
|Åtkomstgranskningar|Länka programkontroll|
|Åtkomstgranskningar|Publicera till Azure AD-åtkomstgranskningar|
|Åtkomstgranskningar|Ta bort granskare från åtkomstgranskning|
|Åtkomstgranskningar|Begär granskningsstopp|
|Åtkomstgranskningar|Begär tillämpning av granskningsresultat|
|Åtkomstgranskningar|Granska medlemskap i Rbac-roller|
|Åtkomstgranskningar|Granska apptilldelning|
|Åtkomstgranskningar|Granska gruppmedlemskap|
|Åtkomstgranskningar|Granska begäran om godkännande|
|Åtkomstgranskningar|Ta bort länk till programkontroll|
|Åtkomstgranskningar|Uppdatera åtkomstgranskning|
|Åtkomstgranskningar|Uppdatera registrerings status för Azure AD åtkomst granskningar|
|Åtkomstgranskningar|Uppdatera inställningar för e-postavisering om åtkomstgranskning|
|Åtkomstgranskningar|Uppdatera inställningen för upprepnings antal för åtkomst granskning|
|Åtkomstgranskningar|Uppdatera upprepnings varaktighet för åtkomst granskning i dagar|
|Åtkomstgranskningar|Uppdatera inställning för upprepnings slut typ för åtkomst granskning|
|Åtkomstgranskningar|Uppdatera inställningen för upprepnings typ för åtkomst granskning|
|Åtkomstgranskningar|Uppdatera inställningar för påminnelse om åtkomstgranskning|
|Åtkomstgranskningar|Uppdatera program|
|Åtkomstgranskningar|Uppdatera godkännande av begäran|
|Åtkomstgranskningar|Användaren är inaktive rad|

## <a name="account-provisioning"></a>Kontoetablering

|Granskningskategori|Aktivitet|
|---|---|
|Programhantering|Hämta beviljanden av V2-programbehörighet|
|Programhantering|Hämta tjänstens huvudnamn för V2-program i aktuell klient|
|Programhantering|Uppdatera V1-program|
|Programhantering|Uppdatera V2-program|
|Programhantering|Uppdatera behörighetsbeviljande för V2-program|
|Programhantering|Lägg till OAuth2PermissionGrant|
|Programhantering|Lägg till approlltilldelning till tjänstens huvudnamn|

## <a name="application-proxy"></a>Programproxy

|Granskningskategori|Aktivitet|
|---|---|
|Programhantering|Lägga till ett program|
|Programhantering|Lägg till ägare till program|
|Programhantering|Lägg till ägare till tjänstens huvudnamn|
|Programhantering|Lägg till princip till tjänstens huvudnamn|
|Kataloghantering|Lägg till tjänstens huvudnamn|
|Kataloghantering|Lägg till autentiseringsuppgifter för tjänstens huvudnamn|
|Kataloghantering|Samtyck till program|
|Kataloghantering|Ta bort program|
|Kataloghantering|Ta bort program permanent|
|Kataloghantering|Ta bort OAuth2PermissionGrant|
|Kataloghantering|Ta bort approlltilldelning från tjänstens huvudnamn|
|Kataloghantering|Ta bort ägare från program|
|Resurs|Ta bort ägare från tjänstens huvudnamn|
|Resurs|Ta bort princip från tjänstens huvudnamn|
|Resurs|Ta bort tjänstens huvudnamn|


## <a name="automated-password-rollover"></a>Automatiserad lösenordsförnyelse

|Granskningskategori|Aktivitet|
|---|---|
|Programhantering|Ta bort autentiseringsuppgifter för tjänstens huvudnamn|


## <a name="b2c"></a>B2C

|Granskningskategori|Aktivitet|
|---|---|
|Programhantering|Återställ program|
|Programhantering|Återkalla medgivande|
|Programhantering|Uppdatera programmet|
|Programhantering|Uppdatera externa hemligheter|
|Programhantering|Uppdatera tjänstens huvudnamn|
|Programhantering|Utfärda ett åtkomsttoken till programmet|
|Programhantering|Utfärda en auktoriseringskod till programmet|
|Programhantering|Utfärda ett id_token för programmet|
|Programhantering|Verifiera autentiseringsuppgifterna för lokalt konto|
|Programhantering|Verifiera användarautentisering|
|Programhantering|Lägga till V2-programbehörigheter|
|Programhantering|Lägga till en nyckel som baseras på en ASCII-hemlighet till en CPIM-nyckelcontainer|
|Programhantering|Lägg till en nyckel till en CPIM-nyckelcontainer|
|Programhantering|AdminPolicyDatas-SetResources|
|Programhantering|AdminUserJourneys-GetResources|
|Programhantering|AdminUserJourneys-RemoveResources|
|Autentisering|AdminUserJourneys-SetResources|
|Autentisering|Skapa IdentityProvider|
|Autentisering|Skapa V1-program|
|Autentisering|Skapa V2-program|
|Autentisering|Skapa en anpassad domän i klienten|
|Auktorisering|Skapa ny AdminUserJourney|
|Auktorisering|Skapa json för lokaliserad resurs|
|Auktorisering|Skapa ny anpassad IDP|
|Auktorisering|Skapa ny IDP|
|Auktorisering|Skapa eller uppdatera en B2C-katalogresurs|
|Auktorisering|Skapa princip|
|Auktorisering|Skapa trustFramework-princip|
|Auktorisering|Skapa trustFramework-princip med konfigurerbart prefix|
|Auktorisering|Skapa användarattribut|
|Auktorisering|CreateTrustFrameworkPolicy|
|Auktorisering|Skapar eller uppdaterar en ny AdminUserJourney|
|Auktorisering|Ta bort IDP|
|Auktorisering|Ta bort IdentityProvider|
|Auktorisering|Ta bort V1-program|
|Auktorisering|Ta bort V2-program|
|Auktorisering|Ta bort behörighetsbeviljande för V2-program|
|Auktorisering|Ta bort en B2C-katalogresurs|
|Auktorisering|Ta bort en CPIM-nyckelcontainer|
|Auktorisering|Ta bort trustFramework-princip|
|Auktorisering|Ta bort användarattribut|
|Auktorisering|Aktivera B2C-funktion|
|Auktorisering|Hämta B2C-katalogresurser i en prenumeration|
|Auktorisering|Hämta anpassad IDP|
|Auktorisering|Hämta IDP|
|Auktorisering|Hämta V1- och V2-program|
|Auktorisering|Hämta V1-program|
|Auktorisering|Hämta V1-program|
|Auktorisering|Hämta V2-program|
|Auktorisering|Hämta V2-program|
|Auktorisering|Hämta en B2C Directory-resurs|
|Auktorisering|Skapa en lista över anpassade domäner i klienten|
|Auktorisering|Hämta en användarresa|
|Auktorisering|Hämta tillåtna programanspråk för användarresa|
|Auktorisering|Hämta tillåtna självkontrollerade anspråk för användarresa|
|Auktorisering|Hämta tillåtna självkontrollerade anspråk för princip|
|Auktorisering|Hämta tillgänglig lista över utdataanspråk|
|Auktorisering|Hämta innehållsdefinitioner för användarresa|
|Auktorisering|Hämta idp:er för ett specifikt administratörsflöde|
|Auktorisering|Hämta nyckelcontainer för metadata för aktiv nyckel i JWK|
|Auktorisering|Hämta lista över alla administratörsflöden|
|Auktorisering|Hämta lista över taggar för alla administratörsflöden för alla användare|
|Auktorisering|Hämta lista över klienter för en användare|
|Auktorisering|Hämta lokala kontos självkontrollerade anspråk|
|Auktorisering|Hämta json för lokaliserad resurs|
|Auktorisering|Hämta åtgärder för resursprovidern Microsoft.AzureActiveDirectory|
|Auktorisering|Hämta principer|
|Auktorisering|Hämta princip|
|Auktorisering|Hämta resursegenskaper för en klient|
|Auktorisering|Hämta IDP-lista som stöds|
|Auktorisering|Hämta IDP-lista som stöds för användarresa|
|Auktorisering|Hämta klientinfo|
|Auktorisering|Hämta klienttillåtna funktioner|
|Auktorisering|Hämta klientdefinierad anpassad IDP-lista|
|Auktorisering|Hämta klientdefinierad IDP-lista|
|Auktorisering|Hämta klientdefinierad lokal IDP-lista|
|Auktorisering|Hämta klientinformation för en användare för att skapa en resurs|
|Auktorisering|Hämta klientlista|
|Auktorisering|Hämta tenantDomains|
|Auktorisering|Hämta standardkultur som stöds för CPIM|
|Auktorisering|Hämta information om ett administratörsflöde|
|Auktorisering|Hämta listan över UserJourneys för den här klienten|
|Auktorisering|Hämta uppsättningen med tillgängliga kulturer som stöds för CPIM|
|Auktorisering|Hämta trustFramework-princip|
|Auktorisering|Hämta trustFramework-princip som xml|
|Auktorisering|Hämta användarattribut|
|Auktorisering|Hämta användarattribut|
|Auktorisering|Hämta lista för användarresa|
|Auktorisering|GetIEFPolicies|
|Auktorisering|GetIdentityProviders|
|Auktorisering|GetTrustFrameworkPolicy|
|Auktorisering|Hämtar en CPIM-nyckelcontainer i jwk-format|
|Auktorisering|Hämtar lista över nyckelcontainrar i klienten|
|Auktorisering|Hämtar typ av klient|
|Auktorisering|MigrateTenantMetadata|
|Auktorisering|Korrigera IdentityProvider|
|Auktorisering|PutTrustFrameworkPolicy|
|Auktorisering|PutTrustFrameworkpolicy|
|Auktorisering|Ta bort en användarresa|
|Auktorisering|Återställa en säkerhetskopia av CPIM-nyckelcontainer|
|Auktorisering|Hämta beviljanden av V2-programbehörighet|
|Auktorisering|Hämta tjänstens huvudnamn för V2-program i aktuell klient|
|Auktorisering|Uppdatera anpassad IDP|
|Auktorisering|Uppdatera IDP|
|Auktorisering|Uppdatera den lokala IdP:n|
|Auktorisering|Uppdatera V1-program|
|Auktorisering|Uppdatera V2-program|
|Auktorisering|Uppdatera behörighetsbeviljande för V2-program|
|Auktorisering|Uppdatera princip|
|Auktorisering|Uppdatera användarattribut|
|Auktorisering|Ladda upp en CPIM-krypterad nyckel|
|Auktorisering|Användarautentisering: API är inaktiverat för klient-featureset|
|Auktorisering|Användarautentisering: Användaren beviljas åtkomst som ”innehavaradministratör”|
|Auktorisering|Användarautentisering: Användare beviljas behörighet för åtkomst som ”behöriga användare”|
|Auktorisering|Kontrollera om B2C-funktionen är aktiverad|
|Auktorisering|Verifiera om funktionen är aktive rad|
|Auktorisering|Skapa program|
|Auktorisering|Ta bort program|
|Auktorisering|Länka programkontroll|
|Auktorisering|Publicera till Azure AD-åtkomstgranskningar|
|Auktorisering|Ta bort länk till programkontroll|
|Auktorisering|Uppdatera program|
|Auktorisering|Inaktivera enkel inloggning för skrivbord|
|Auktorisering|Inaktivera skrivbords-Sso för en specifik domän|
|Auktorisering|Inaktivera programproxy|
|Auktorisering|Inaktivera direktautentisering|
|Auktorisering|Aktivera enkel inloggning för skrivbord|
|Kataloghantering|Aktivera skrivbords-Sso för en specifik domän|
|Kataloghantering|Aktivera Application Proxy|
|Kataloghantering|Aktivera direktautentisering|
|Kataloghantering|Skapa en anpassad domän i klienten|
|Kataloghantering|Aktivera B2C-funktion|
|Kataloghantering|Skapa en lista över anpassade domäner i klienten|
|Kataloghantering|Hämta resursegenskaper för en klient|
|Kataloghantering|Hämta klientinfo|
|Kataloghantering|Hämta klienttillåtna funktioner|
|Kataloghantering|Hämta tenantDomains|
|Nyckel|Hämtar typ av klient|
|Nyckel|Kontrollera om B2C-funktionen är aktiverad|
|Nyckel|Verifiera om funktionen är aktive rad|
|Nyckel|Lägg till partner till företag|
|Nyckel|Lägg till overifierad domän|
|Nyckel|Lägg till verifierad domän|
|Nyckel|Skapa företag|
|Nyckel|Skapa företagsinställningar|
|Nyckel|Ta bort företagsinställningar|
|Nyckel|Flytta ned partner|
|Nyckel|Katalog borttagen|
|Annat|Katalog borttagen permanent|
|Annat|Katalog schemalagd för borttagning|
|Resurs|Flytta upp företag till partner|
|Resurs|Rensa egenskaper för Rights Management|
|Resurs|Ta bort partner från företag|
|Resurs|Ta bort overifierad domän|
|Resurs|Ta bort verifierad domän|
|Resurs|Ange företagsinformation|
|Resurs|Ange Dirsync-funktion|
|Resurs|Ange flaggan Dirsyncenabled|
|Resurs|Ange partnerskap|
|Resurs|Ange tröskelvärde för oavsiktlig borttagning|
|Resurs|Ange företagstillåten dataplats|
|Resurs|Ange företagsfunktionen Multinationell som aktiverad|
|Resurs|Ange katalogfunktion för klientorganisation|
|Resurs|Ange domänautentisering|
|Resurs|Ange federationsinställningar för domän|
|Resurs|Ange lösenordsprincip|
|Resurs|Ange egenskaper för Rights Management|
|Resurs|Uppdatera företag|
|Resurs|Uppdatera företagsinställningar|
|Resurs|Uppdatera domän|
|Resurs|Verifiera domän|
|Resurs|Verifiera e-postverifierad domän|
|Resurs|Publicering|
|Resurs|Uppdatera aviseringsinställningar|
|Resurs|Uppdatera inställningar för sammandrag av veckan|
|Resurs|Inaktivera lösenordstillbakaskrivning för katalog|
|Resurs|Aktivera lösenordstillbakaskrivning för katalog|
|Resurs|Lägg till approlltilldelning till grupp|
|Resurs|Lägg till grupp|
|Resurs|Lägg till medlem i grupp|
|Resurs|Lägg till ägare till grupp|
|Resurs|Skapa gruppinställningar|
|Resurs|Ta bort grupp|
|Resurs|Ta bort gruppinställningar|
|Resurs|Slutför tillämpning av gruppbaserad licens för användare|
|Resurs|Ta bort grupp permanent|
|Resurs|Ta bort approlltilldelning från grupp|
|Resurs|Ta bort medlem från grupp|
|Resurs|Ta bort ägare från grupp|
|Resurs|Återställ grupp|
|Resurs|Ange grupplicens|
|Resurs|Ange grupp som ska hanteras av användare|
|Resurs|Börja tillämpa gruppbaserad licens för användare|
|Resurs|Utlös omberäkning för grupplicens|
|Resurs|Uppdatera grupp|
|Resurs|Uppdatera gruppinställningar|
|Resurs|Lägg till medlem|
|Resurs|Skapa grupp|
|Resurs|Ta bort grupp|
|Resurs|Ta bort medlem|
|Resurs|Uppdatera grupp|
|Resurs|Godkänn en väntande begäran om att gå med i en grupp|
|Resurs|Avbryt en väntande begäran om att gå med i en grupp|
|Resurs|Skapa princip för livscykelhantering|
|Resurs|Ta bort en väntande begäran om att gå med i en grupp|
|Resurs|Avvisa en väntande begäran om att gå med i en grupp|
|Resurs|Förnya grupp|
|Resurs|Begäran om att gå med i en grupp|
|Resurs|Ange dynamiska gruppegenskaper|
|Resurs|Uppdatera princip för livscykelhantering|
|Resurs|Lägga till en nyckel som baseras på en ASCII-hemlighet till en CPIM-nyckelcontainer|
|Resurs|Lägg till en nyckel till en CPIM-nyckelcontainer|
|Resurs|Ta bort en CPIM-nyckelcontainer|
|Resurs|Ta bort nyckelcontainer|
|Resurs|Hämta nyckelcontainer för metadata för aktiv nyckel i JWK|
|Resurs|Hämta metadata för nyckelcontainer|
|Resurs|Hämtar en CPIM-nyckelcontainer i jwk-format|
|Resurs|Hämtar lista över nyckelcontainrar i klienten|
|Resurs|Återställa en säkerhetskopia av CPIM-nyckelcontainer|
|Resurs|Spara nyckelcontainer|
|Resurs|Ladda upp en CPIM-krypterad nyckel|
|Resurs|Utfärda en auktoriseringskod till programmet|
|Resurs|Utfärda ett id_token för programmet|


## <a name="core-directory"></a>Grundläggande katalog

|Granskningskategori|Aktivitet|
|---|---|
|Hantering av administrativ enhet|Hämta en identifierings typ för enskild risk|
|Hantering av administrativ enhet|Ladda ned administratörer och status för sammandrag av veckan|
|Hantering av administrativ enhet|Hämta alla typer av risk identifiering|
|Hantering av administrativ enhet|Ladda ned kostnads fria identifieringar av användar risker|
|Hantering av administrativ enhet|Ladda ned användare som har flaggats för risk|
|Programhantering|Bearbetade batchinbjudningar|
|Programhantering|Överförda batchinbjudningar|
|Programhantering|Lägg till ägare till princip|
|Programhantering|Lägg till princip|
|Programhantering|Ta bort princip|
|Programhantering|Ta bort autentiseringsuppgifter för princip|
|Programhantering|Uppdatera princip|
|Programhantering|Ställ in MFA-registreingsprincip|
|Programhantering|Ange princip för inloggningsrisk|
|Programhantering|Ange princip för användarrisk|
|Programhantering|Godkänn användningsvillkoren|
|Programhantering|Skapa användningsvillkor|
|Programhantering|Ta bort användningsvillkor|
|Programhantering|Ta bort användningsvillkor|
|Programhantering|Redigera användningsvillkoren|
|Programhantering|Publicera användningsvillkoren|
|Programhantering|Avpublicera användningsvillkoren|
|Programhantering|Lägg till Application TLS/SSL-certifikat|
|Programhantering|Ta bort TLS-bindning|
|Programhantering|Registrera anslutningsapp|
|Programhantering|AdminPolicyDatas-RemoveResources|
|Programhantering|AdminPolicyDatas-SetResources|
|Programhantering|AdminUserJourneys-GetResources|
|Kataloghantering|AdminUserJourneys-RemoveResources|
|Kataloghantering|AdminUserJourneys-SetResources|
|Kataloghantering|Skapa IdentityProvider|
|Kataloghantering|Skapa ny AdminUserJourney|
|Kataloghantering|Skapa json för lokaliserad resurs|
|Kataloghantering|Skapa ny anpassad IDP|
|Kataloghantering|Skapa ny IDP|
|Kataloghantering|Skapa eller uppdatera en B2C-katalogresurs|
|Kataloghantering|Skapa princip|
|Kataloghantering|Skapa trustFramework-princip|
|Kataloghantering|Skapa trustFramework-princip med konfigurerbart prefix|
|Kataloghantering|Skapa användarattribut|
|Kataloghantering|CreateTrustFrameworkPolicy|
|Kataloghantering|Ta bort IDP|
|Kataloghantering|Ta bort IdentityProvider|
|Kataloghantering|Ta bort en B2C-katalogresurs|
|Kataloghantering|Ta bort trustFramework-princip|
|Kataloghantering|Ta bort användarattribut|
|Kataloghantering|Hämta B2C-katalogresurser i en resursgrupp|
|Kataloghantering|Hämta B2C-katalogresurser i en prenumeration|
|Kataloghantering|Hämta anpassad IDP|
|Kataloghantering|Hämta IDP|
|Kataloghantering|Hämta en B2C Directory-resurs|
|Kataloghantering|Hämta en användarresa|
|Kataloghantering|Hämta tillåtna programanspråk för användarresa|
|Kataloghantering|Hämta tillåtna självkontrollerade anspråk för användarresa|
|Kataloghantering|Hämta tillåtna självkontrollerade anspråk för princip|
|Kataloghantering|Hämta tillgänglig lista över utdataanspråk|
|Kataloghantering|Hämta innehållsdefinitioner för användarresa|
|Kataloghantering|Hämta idp:er för ett specifikt administratörsflöde|
|Kataloghantering|Hämta lista över alla administratörsflöden|
|Kataloghantering|Hämta lista över taggar för alla administratörsflöden för alla användare|
|Grupphantering|Grupp medlemmar för grupp hämtning – startade|
|Grupphantering|Grupp medlemmar för grupp hämtning – avslutad|
|Grupphantering|Grupp medlemmar för Mass import – startade|
|Grupphantering|Grupp medlemmar för Mass import – avslutad|
|Grupphantering|Mass borttagning av grupp medlemmar – startat|
|Grupphantering|Grupp medlemmar för Mass borttagning – avslutad|
|Grupphantering|Grupp hämtnings grupper – startat|
|Grupphantering|Grupp hämtnings grupper – klart|
|Grupphantering|Hämta lista över klienter för en användare|
|Grupphantering|Hämta lokala kontos självkontrollerade anspråk|
|Grupphantering|Hämta json för lokaliserad resurs|
|Grupphantering|Hämta åtgärder för resursprovidern Microsoft.AzureActiveDirectory|
|Grupphantering|Hämta principer|
|Grupphantering|Hämta princip|
|Grupphantering|Hämta IDP-lista som stöds|
|Grupphantering|Hämta IDP-lista som stöds för användarresa|
|Grupphantering|Hämta klientdefinierad anpassad IDP-lista|
|Grupphantering|Hämta klientdefinierad IDP-lista|
|Grupphantering|Hämta klientdefinierad lokal IDP-lista|
|Grupphantering|Hämta klientinformation för en användare för att skapa en resurs|
|Grupphantering|Hämta standardkultur som stöds för CPIM|
|Grupphantering|Hämta information om ett administratörsflöde|
|Grupphantering|Hämta listan över UserJourneys för den här klienten|
|Grupphantering|Hämta uppsättningen med tillgängliga kulturer som stöds för CPIM|
|Grupphantering|Hämta trustFramework-princip|
|Grupphantering|Hämta trustFramework-princip som xml|
|Grupphantering|Hämta användarattribut|
|Principhantering|Hämta användarattribut|
|Principhantering|Hämta lista för användarresa|
|Principhantering|GetIEFPolicies|
|Principhantering|GetIdentityProviders|
|Principhantering|GetTrustFrameworkPolicy|
|Resurs|MigrateTenantMetadata|
|Resurs|Flytta resurser|
|Resurs|Korrigera IdentityProvider|
|Resurs|PutTrustFrameworkPolicy|
|Resurs|PutTrustFrameworkpolicy|
|Resurs|Ta bort en användarresa|
|Resurs|Uppdatera anpassad IDP|
|Resurs|Uppdatera IDP|
|Resurs|Uppdatera den lokala IdP:n|
|Resurs|Uppdatera en B2C-katalogresurs|
|Resurs|Uppdatera princip|
|Resurs|Uppdatera prenumerationsstatus|
|Rollhantering|Uppdatera användarattribut|
|Rollhantering|Verifiera flytta resurser|
|Rollhantering|Lägg till enhet|
|Rollhantering|Lägg till enhetskonfiguration|
|Rollhantering|Lägg till registrerad ägare till enhet|
|Rollhantering|Lägg till registrerade användare till enhet|
|Rollhantering|Ta bort enhet|
|Rollhantering|Ta bort enhetskonfiguration|
|Rollhantering|Enheten är inte längre kompatibel|
|Rollhantering|Enheten hanteras inte längre|
|Användarhantering|AccessReview_Review|
|Användarhantering|AccessReview_Update|
|Användarhantering|ActivationAborted|
|Användarhantering|ActivationAborted|
|Användarhantering|ActivationAborted|
|Användarhantering|ActivationRequested|
|Användarhantering|Lägg till berättigad medlem i rollen|
|Användarhantering|Lägga till medlem till en roll|
|Användarhantering|Lägg till rolltilldelning till rolldefinition|
|Användarhantering|Lägg till roll från mall|
|Användarhantering|Lägg till begränsad medlem till roll|
|Användarhantering|Tillagd|
|Användarhantering|Tilldela|
|Användarhantering|Mass skapande av användare – startat|
|Användarhantering|Mass skapande av användare – klart|
|Användarhantering|Mass borttagning av användare – startat|
|Användarhantering|Mass borttagning av användare – färdig|
|Användarhantering|Mass nedladdnings användare – startat|
|Användarhantering|Mass nedladdnings användare – klart|
|Användarhantering|Mass återställning borttagna användare – startat|
|Användarhantering|Mass återställning borttagna användare – avslutad|
|Användarhantering|Massredigera användare – startade|
|Användarhantering|Grupp användare-avslutad|
|Användarhantering|Ta bort registrerad ägare från enhet|
|Användarhantering|Ta bort registrerade användare från enhet|
|Användarhantering|Ta bort behörig medlem från roll|
|Användarhantering|Ta bort medlem från roll|
|Användarhantering|Ta bort rolltilldelning från rolldefinition|
|Användarhantering|Ta bort begränsad medlem från roll|
|Användarhantering|Uppdatera enhet|
|Användarhantering|Uppdatera enhetskonfiguration|
|Användarhantering|Uppdatera roll|






## <a name="identity-protection"></a>Identity Protection

|Granskningskategori|Aktivitet|
|---|---|
|Kataloghantering|Upphöj|
|Kataloghantering|Borttaget|
|Kataloghantering|Ändringar av rollinställningar|
|Annat|ScanAlertsNow|
|Annat|Registrera|
|Annat|Ta bort behörighet|
|Annat|UpdateAlertSettings|
|Annat|UpdateCurrentState|
|Principhantering|Åtkomstgranskning avslutades|
|Principhantering|Lägg till godkännare för begäran|
|Principhantering|Lägg till granskare i åtkomstgranskning|
|Användarhantering|Tillämpa åtkomstgranskning|
|Användarhantering|Skapa åtkomstgranskning|


## <a name="invited-users"></a>Inbjudna användare

|Granskningskategori|Aktivitet|
|---|---|
|Annat|Skapa godkännande av begäran|
|Annat|Ta bort åtkomstgranskning|
|Användarhantering|Ta bort granskare från åtkomstgranskning|
|Användarhantering|Begär tillämpning av granskningsresultat|
|Användarhantering|Begär granskningsstopp|
|Användarhantering|Granska apptilldelning|
|Användarhantering|Granska gruppmedlemskap|
|Användarhantering|Granska medlemskap i Rbac-roller|


## <a name="microsoft-identity-manager-mim"></a>Microsoft Identity Manager (MIM)

|Granskningskategori|Aktivitet|
|---|---|
|Grupphantering|Granska begäran om godkännande|
|Grupphantering|Uppdatera åtkomstgranskning|
|Grupphantering|Uppdatera inställningar för e-postavisering om åtkomstgranskning|
|Grupphantering|Uppdatera inställningen för upprepnings antal för åtkomst granskning|
|Grupphantering|Uppdatera upprepnings varaktighet för åtkomst granskning i dagar|
|Användarhantering|Uppdatera inställning för upprepnings slut typ för åtkomst granskning|
|Användarhantering|Uppdatera inställningen för upprepnings typ för åtkomst granskning|



## <a name="privileged-identity-management"></a>Privileged Identity Management

|Granskningskategori|Aktivitet|
|---|---|
|PIM|ActivationAborted|
|PIM|ActivationAborted|
|PIM|ActivationAborted|
|PIM|ActivationDenied|
|PIM|ActivationRequested|
|PIM|Tillagd|
|PIM|AddedOutsidePIM|
|PIM|Tilldela|
|PIM|DismissAlert|
|PIM|Upphöj|
|PIM|ReactivateAlert|
|PIM|Borttaget|
|PIM|RemovedOutsidePIM|
|PIM|Begär granskningsstopp|
|PIM|Ändringar av rollinställningar|
|PIM|ScanAlertsNow|
|PIM|Registrera|
|PIM|Ta bort|
|PIM|Ta bort behörighet|
|PIM|UpdateAlertSettings|
|PIM|UpdateCurrentState|


## <a name="self-service-group-management"></a>Självbetjäning, grupphantering

|Granskningskategori|Aktivitet|
|---|---|
|Grupphantering|Återställ användarlösenord|
|Grupphantering|Återställ användare|
|Grupphantering|Genomför ändring av användarlösenord|
|Grupphantering|Ange användarhanterare|
|Grupphantering|Aktivera användares OATH-tokenmetadata|
|Grupphantering|Uppdatera StsRefreshTokenValidFrom-tidsstämpel|
|Grupphantering|Uppdatera externa hemligheter|
|Grupphantering|Uppdatera användare|
|Grupphantering|Administratören skapar ett tillfälligt lösenord|


## <a name="self-service-password-management"></a>Lösenordshantering via självbetjäning

|Granskningskategori|Aktivitet|
|---|---|
|Kataloghantering|Administratörer kräver att användaren återställer sitt lösenord|
|Kataloghantering|Tilldela extern användare till program|
|Användarhantering|E-postmeddelandet skickades inte, användaren har avbrutit prenumerationen|
|Användarhantering|Bjud in extern användare|
|Användarhantering|Lös in inbjudan för extern användare|
|Användarhantering|Generering av viral klientorganisation|
|Användarhantering|Generering av viral användare|
|Användarhantering|Användarlösenordsregistrering|
|Användarhantering|Återställning av användarlösenord|
|Användarhantering|Blockerad från lösenordsåterställning via självbetjäning|


## <a name="terms-of-use"></a>Villkor för användning

|Granskningskategori|Aktivitet|
|---|---|
|Användningsvillkor|Godkänn användningsvillkoren|
|Användningsvillkor|Skapa användningsvillkor|
|Användningsvillkor|Ta bort användningsvillkor|
|Användningsvillkor|Ta bort medgivande|
|Användningsvillkor|Ta bort användningsvillkor|
|Användningsvillkor|Redigera användningsvillkoren|
|Användningsvillkor|Användnings villkor upphör att gälla|
|Användningsvillkor|Användnings villkor för hård borttagning|
|Användningsvillkor|Publicera användningsvillkoren|
|Användningsvillkor|Avpublicera användningsvillkoren|


## <a name="next-steps"></a>Nästa steg

- [Översikt över Azure AD-rapporter](overview-reports.md).
- [Rapporten gransknings loggar](concept-audit-logs.md). 
- [Programmerings åtkomst till Azure AD-rapporter](concept-reporting-api.md)