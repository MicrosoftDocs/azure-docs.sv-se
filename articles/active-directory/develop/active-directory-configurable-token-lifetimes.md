---
title: Konfigurerbar livslängd för token
titleSuffix: Microsoft identity platform
description: Lär dig hur du anger livslängder för åtkomst, SAML och ID-token som utfärdats av Microsoft Identity Platform.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 04/08/2021
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40, content-perf, FY21Q1, contperf-fy21q1
ms.reviewer: hirsin, jlu, annaba
ms.openlocfilehash: e1753391c7b61b6e9bd9e6ac0d142b4ee94502d8
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363979"
---
# <a name="configurable-token-lifetimes-in-the-microsoft-identity-platform-preview"></a>Konfigurerbara livslängder för token på Microsoft Identity Platform (förhandsversion)

Du kan ange livslängden för en åtkomst-, ID- eller SAML-token som utfärdats av Microsoft Identity Platform. Du kan ange token-livslängd för alla program i din organisation, för ett program med flera klientorganisationer eller för en specifik huvudtjänst i organisationen. För närvarande stöder vi dock inte konfigurering av tokenlivslängder för [tjänsthuvudnamn för hanterad identitet.](../managed-identities-azure-resources/overview.md)

I Azure AD representerar ett principobjekt en uppsättning regler som tillämpas på enskilda program eller på alla program i en organisation. Varje principtyp har en unik struktur med en uppsättning egenskaper som tillämpas på objekt som de har tilldelats.

Du kan ange en princip som standardprincip för din organisation. Principen tillämpas på alla program i organisationen, så länge den inte åsidosätts av en princip med högre prioritet. Du kan också tilldela en princip till specifika program. Prioritetsordningen varierar beroende på principtyp.

Exempel finns i Läs [exempel på hur du konfigurerar livslängd för token.](configure-token-lifetimes.md)

> [!NOTE]
> Konfigurerbar livslängdsprincip för token gäller endast för mobila och stationära klienter som har åtkomst till SharePoint Online och OneDrive för företag-resurser och som inte gäller för webbläsarsessioner.
> Om du vill hantera livslängden för webbläsarsessioner för SharePoint Online och OneDrive för företag kan du använda funktionen för villkorlig [åtkomst för sessionslivslängd.](../conditional-access/howto-conditional-access-session-lifetime.md) Mer information om hur [du konfigurerar](https://techcommunity.microsoft.com/t5/SharePoint-Blog/Introducing-Idle-Session-Timeout-in-SharePoint-and-OneDrive/ba-p/119208) tidsgränser för inaktivitetssessioner finns i SharePoint Online-bloggen.

## <a name="license-requirements"></a>Licenskrav

Den här funktionen kräver en Azure AD Premium P1-licens. Information om hur du hittar rätt licens för dina krav finns [i Jämföra allmänt tillgängliga funktioner i free- och Premium-versionerna.](https://azure.microsoft.com/pricing/details/active-directory/)

Kunder med [Microsoft 365 Business-licenser](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description) har också åtkomst till funktioner för villkorlig åtkomst.

## <a name="token-lifetime-policies-for-access-saml-and-id-tokens"></a>Livslängdsprinciper för token för åtkomst, SAML och ID-token

Du kan ange livslängdsprinciper för token för åtkomsttoken, SAML-token och ID-token.

### <a name="access-tokens"></a>Åtkomsttokens

Klienter använder åtkomsttoken för att få åtkomst till en skyddad resurs. En åtkomsttoken kan bara användas för en specifik kombination av användare, klient och resurs. Åtkomsttoken kan inte återkallas och är giltiga förrän de upphör att gälla. En illvillig aktör som har fått en åtkomsttoken kan använda den under hela livslängden. Att justera livslängden för en åtkomsttoken är en avvägning mellan att förbättra systemets prestanda och att öka den tid som klienten behåller åtkomst efter att användarkontot har inaktiverats. Förbättrad systemprestanda uppnås genom att minska antalet gånger som en klient behöver hämta en ny åtkomsttoken.  Standardvärdet varierar beroende på vilket klientprogram som begär token. Till exempel kommer cae-kompatibla klienter (continuous access evaluation) som förhandlar CAE-medvetna sessioner att se en livslängd för långlivade token (upp till 28 timmar). När token upphör att gälla måste klienten använda uppdateringstoken för att (vanligtvis tyst) hämta en ny uppdateringstoken och åtkomsttoken.

### <a name="saml-tokens"></a>SAML-token

SAML-token används av många webbaserade SaaS-program och hämtas med Azure Active Directory SAML2-protokollslutpunkt. De används också av program som använder WS-Federation. Standardlivslängden för token är 1 timme. Från ett program perspektiv anges giltighetsperioden för token av NotOnOrAfter-värdet för `<conditions …>` elementet i token. När giltighetsperioden för token har upphört måste klienten initiera en ny autentiseringsbegäran, som ofta uppfylls utan interaktiv inloggning som ett resultat av sessionstoken för enkel inloggning (SSO).

Värdet för NotOnOrAfter kan ändras med hjälp av `AccessTokenLifetime` parametern i en `TokenLifetimePolicy` . Den ställs in på den livslängd som konfigurerats i principen om sådan finns, plus en klockskev faktor på fem minuter.

Ämnesbekräftelsen NotOnOrAfter som anges `<SubjectConfirmationData>` i elementet påverkas inte av konfigurationen för tokenlivslängd. 

### <a name="id-tokens"></a>ID-tokens

ID-token skickas till webbplatser och interna klienter. ID-token innehåller profilinformation om en användare. En ID-token är bunden till en specifik kombination av användare och klient. ID-token anses vara giltiga tills de upphör att gälla. Ett webbprogram matchar vanligtvis en användares sessionslivslängd i programmet till livslängden för den ID-token som utfärdats för användaren. Du kan justera livslängden för en ID-token för att styra hur ofta webbappen upphör att gälla programsessionen och hur ofta det kräver att användaren autentiseras igen med Microsoft Identity Platform (tyst eller interaktivt).

## <a name="token-lifetime-policies-for-refresh-tokens-and-session-tokens"></a>Livslängdsprinciper för token för uppdateringstoken och sessionstoken

Du kan inte ange livslängdsprinciper för token för uppdateringstoken och sessionstoken.

> [!IMPORTANT]
> Från och med 30 januari 2021 kan du inte konfigurera livslängd för uppdaterings- och sessionstoken. Azure Active Directory inte längre uppdaterings- och sessionstokenkonfiguration i befintliga principer.  Nya token som utfärdas när befintliga token har upphört att gälla är nu inställda på [standardkonfigurationen](#configurable-token-lifetime-properties). Du kan fortfarande konfigurera livslängd för åtkomst, SAML och ID-token efter att uppdaterings- och sessionstokenkonfigurationen har pensioneras.
>
> Den befintliga tokens livslängd ändras inte. När de upphör att gälla utfärdas en ny token baserat på standardvärdet.
>
> Om du behöver fortsätta att definiera tidsperioden innan en användare uppmanas att logga in igen konfigurerar du inloggningsfrekvensen i Villkorlig åtkomst. Mer information om villkorlig åtkomst finns i Konfigurera [hantering av autentiseringssessioner med villkorlig åtkomst.](../conditional-access/howto-conditional-access-session-lifetime.md)

## <a name="configurable-token-lifetime-properties"></a>Konfigurerbara livslängdsegenskaper för token
En livslängdsprincip för token är en typ av principobjekt som innehåller livslängdsregler för token. Den här principen styr hur länge åtkomst, SAML och ID-token för den här resursen anses vara giltiga. Livslängdsprinciper för token kan inte anges för uppdaterings- och sessionstoken. Om ingen princip har angetts tillämpar systemet standardvärdet för livslängd.

### <a name="access-id-and-saml2-token-lifetime-policy-properties"></a>Principegenskaper för åtkomst, ID och SAML2-tokenlivslängd

Genom att minska livslängdsegenskapen för åtkomsttoken minskar du risken för att en åtkomsttoken eller ID-token används av en illvillig aktör under en längre tid. (Dessa token kan inte återkallas.) Avvägningen är att prestandan påverkas negativt, eftersom token måste ersättas oftare.

Ett exempel finns i [Skapa en princip för webb inloggning.](configure-token-lifetimes.md#create-a-policy-for-web-sign-in)

Konfiguration av åtkomst, ID och SAML2-token påverkas av följande egenskaper och deras respektive inställda värden:

- **Egenskap:** Livslängd för åtkomsttoken
- **Principegenskapssträng:** AccessTokenLifetime
- **Påverkar**: Åtkomsttoken, ID-token, SAML2-token
- **Standard:**
    - Åtkomsttoken: varierar beroende på vilket klientprogram som begär token. Till exempel kommer CAE-kompatibla klienter (Continuous Access Evaluation) som förhandlar CAE-medvetna sessioner att se en livslängd för lång livslängd för token (upp till 28 timmar).
    - ID-token, SAML2-token: 1 timme
- **Minst**: 10 minuter
- **Max:** 1 dag

### <a name="refresh-and-session-token-lifetime-policy-properties"></a>Principegenskaper för uppdaterings- och sessionstokens livslängd

Konfigurationen av uppdaterings- och sessionstoken påverkas av följande egenskaper och deras respektive inställda värden. Efter tillbakagången av konfigurationen av uppdaterings- och sessionstoken den 30 januari 2021 respekterar Azure AD endast standardvärdena som beskrivs nedan. Om du bestämmer [](../conditional-access/howto-conditional-access-session-lifetime.md) dig för att inte använda villkorsstyrd åtkomst för att hantera inloggningsfrekvensen ställs dina uppdaterings- och sessionstoken in på standardkonfigurationen på det datumet och du kommer inte längre att kunna ändra deras livslängd.  

|Egenskap   |Principegenskapssträng    |Påverkar |Standardvärde |
|----------|-----------|------------|------------|
|Maximal inaktiv tid för uppdateringstoken |MaxInactiveTime  |Uppdatera token |90 dagar  |
|Single-Factor högsta ålder för uppdateringstoken  |MaxAgeSingleFactor  |Uppdatera token (för alla användare)  |Tills den återkallas  |
|Maximal ålder för multifaktoruppdateringstoken  |MaxAgeMultiFactor  |Uppdatera token (för alla användare) |Tills den återkallas  |
|Single-Factor högsta ålder för sessionstoken  |MaxAgeSessionSingleFactor |Sessionstoken (beständiga och icke-inaktsfria)  |Tills den återkallas |
|Maximal ålder för multifaktorsessionstoken  |MaxAgeSessionMultiFactor  |Sessionstoken (beständiga och icke-inaktsfria)  |Tills den återkallas |

Du kan använda PowerShell för att hitta de principer som påverkas av tillbakagången.  Använd [PowerShell-cmdletarna för](configure-token-lifetimes.md#get-started) att se alla principer som skapats i din organisation eller för att hitta vilka appar och tjänstens huvudnamn som är länkade till en specifik princip.

## <a name="policy-evaluation-and-prioritization"></a>Principutvärdering och prioritering
Du kan skapa och sedan tilldela en livslängdsprincip för token till ett specifikt program, till din organisation och till tjänstens huvudnamn. Flera principer kan gälla för ett visst program. Principen för livslängd för token som tillämpas följer dessa regler:

* Om en princip uttryckligen tilldelas till tjänstens huvudnamn tillämpas den.
* Om ingen princip uttryckligen tilldelas till tjänstens huvudnamn tillämpas en princip som uttryckligen tilldelats till den överordnade organisationen för tjänstens huvudnamn.
* Om ingen princip uttryckligen tilldelas till tjänstens huvudnamn eller till organisationen tillämpas principen som tilldelats programmet.
* Om ingen princip har tilldelats till tjänstens huvudnamn, organisation eller programobjektet tillämpas standardvärdena. (Se tabellen i [Configurable token lifetime properties](#configurable-token-lifetime-properties).)

Mer information om relationen mellan programobjekt och objekt för tjänstens huvudnamn finns i [Program- och tjänsthuvudnamnsobjekt i Azure Active Directory](app-objects-and-service-principals.md).

En tokens giltighet utvärderas när token används. Principen med högst prioritet för det program som används tillämpas.

Alla tidsspann som används här formateras enligt objektet C# [TimeSpan](/dotnet/api/system.timespan) – D.HH:MM:SS.  80 dagar och 30 minuter skulle alltså vara `80.00:30:00` .  Inledande D kan tas bort om noll, så 90 minuter skulle vara `00:90:00` .  

## <a name="cmdlet-reference"></a>Cmdlet-referens

Det här är cmdletarna i [Azure Active Directory PowerShell för Graph Preview-modulen](/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true#service-principals).

### <a name="manage-policies"></a>Hantera principer

Du kan använda följande cmdlets för att hantera principer.

| Cmdlet | Beskrivning | 
| --- | --- |
| [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | Skapar en ny princip. |
| [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | Hämtar alla Azure AD-principer eller en angiven princip. |
| [Get-AzureADPolicyAppliedObject](/powershell/module/azuread/get-azureadpolicyappliedobject?view=azureadps-2.0-preview&preserve-view=true) | Hämtar alla appar och tjänstens huvudnamn som är länkade till en princip. |
| [Set-AzureADPolicy](/powershell/module/azuread/set-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | Uppdaterar en befintlig princip. |
| [Remove-AzureADPolicy](/powershell/module/azuread/remove-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | Tar bort den angivna principen. |

### <a name="application-policies"></a>Användningsprinciper
Du kan använda följande cmdlets för programprinciper.</br></br>

| Cmdlet | Beskrivning | 
| --- | --- |
| [Add-AzureADApplicationPolicy](/powershell/module/azuread/add-azureadapplicationpolicy?view=azureadps-2.0-preview&preserve-view=true) | Länkar den angivna principen till ett program. |
| [Get-AzureADApplicationPolicy](/powershell/module/azuread/get-azureadapplicationpolicy?view=azureadps-2.0-preview&preserve-view=true) | Hämtar principen som är tilldelad till ett program. |
| [Remove-AzureADApplicationPolicy](/powershell/module/azuread/remove-azureadapplicationpolicy?view=azureadps-2.0-preview&preserve-view=true) | Tar bort en princip från ett program. |

### <a name="service-principal-policies"></a>Principer för tjänstens huvudnamn
Du kan använda följande cmdlets för principer för tjänstens huvudnamn.

| Cmdlet | Beskrivning | 
| --- | --- |
| [Add-AzureADServicePrincipalPolicy](/powershell/module/azuread/add-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) | Länkar den angivna principen till ett huvudnamn för tjänsten. |
| [Get-AzureADServicePrincipalPolicy](/powershell/module/azuread/get-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) | Hämtar alla policyer som är länkade till det angivna tjänstens huvudnamn.|
| [Remove-AzureADServicePrincipalPolicy](/powershell/module/azuread/remove-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) | Tar bort principen från det angivna tjänstens huvudnamn.|

## <a name="next-steps"></a>Nästa steg

Mer information finns i exempel [på hur du konfigurerar livslängd för token.](configure-token-lifetimes.md)
