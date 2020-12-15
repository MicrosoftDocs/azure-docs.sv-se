---
title: Konfigurerbara livstider för token
titleSuffix: Microsoft identity platform
description: Lär dig hur du ställer in livs längder för token som utfärdats av Microsoft Identity Platform.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 12/14/2020
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40, content-perf, FY21Q1, contperf-fy21q1
ms.reviewer: hirsin, jlu, annaba
ms.openlocfilehash: f73186612fe79af88e84956bb4d0f0b374f4c986
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97507803"
---
# <a name="configurable-token-lifetimes-in-microsoft-identity-platform-preview"></a>Konfigurerbara livstider för token i Microsoft Identity Platform (för hands version)

> [!IMPORTANT]
> Efter den 2020 kommer klient organisationer inte längre att kunna konfigurera livstid för uppdatering och sessionstoken.  Azure Active Directory slutar att svara på befintlig konfiguration och sessionstoken i principer efter 30 januari 2021. Du kan fortfarande konfigurera livstid för åtkomsttoken efter utfasningen.
>
> Om du behöver fortsätta att definiera tids perioden innan en användare uppmanas att logga in igen, konfigurerar du inloggnings frekvensen i villkorlig åtkomst. Om du vill veta mer om villkorlig åtkomst kan du gå till [Konfigurera hantering av autentisering med villkorlig åtkomst](/azure/active-directory/conditional-access/howto-conditional-access-session-lifetime).
>
> För klienter som inte vill använda villkorlig åtkomst efter indragnings datumet kan de förväntas vara att Azure AD följer standard konfigurationen som beskrivs i nästa avsnitt.

## <a name="configurable-token-lifetime-properties-after-the-retirement"></a>Konfigurerbara egenskaper för token-livstid efter pensionering
Konfiguration av och sessionstoken påverkas av följande egenskaper och deras värden. Efter indragningen av konfigurationen och konfigurationen av sessionstoken kommer Azure AD endast att följa standardvärdet som beskrivs nedan, oavsett om principer har anpassade värden konfigurerade konfigurerade anpassade värden. Du kan fortfarande konfigurera livstid för åtkomsttoken efter indragningen. 

|Egenskap   |Princip egenskaps sträng    |Nätverk |Standardvärde |
|----------|-----------|------------|------------|
|Maximal inaktiv tid för uppdateringstoken |MaxInactiveTime  |Uppdatera token |90 dagar  |
|Högsta ålder för Single-Factor uppdaterad token  |MaxAgeSingleFactor  |Uppdatera tokens (för alla användare)  |Tills den har återkallats  |
|Högsta ålder för Multi-Factor Refresh-token  |MaxAgeMultiFactor  |Uppdatera tokens (för alla användare) |180 dagar  |
|Högsta ålder för Single-Factor sessionstoken  |MaxAgeSessionSingleFactor |Token för sessioner (beständiga och inte permanenta)  |Tills den har återkallats |
|Högsta ålder för Multi-Factor session  |MaxAgeSessionMultiFactor  |Token för sessioner (beständiga och inte permanenta)  |180 dagar |

## <a name="identify-configuration-in-scope-of-retirement"></a>Identifiera konfiguration i omfånget för pensionering

Gör så här för att komma igång:

1. Ladda ned den senaste [Azure AD PowerShell-modulens offentliga för hands version](https://www.powershellgallery.com/packages/AzureADPreview).
1. Kör `Connect` kommandot för att logga in på ditt Azure AD-administratörskonto. Kör det här kommandot varje gången du startar en ny session.

    ```powershell
    Connect-AzureAD -Confirm
    ```

1. Om du vill se alla principer som har skapats i din organisation kör du cmdleten [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) .  Eventuella resultat med definierade egenskaps värden som skiljer sig från de standardvärden som anges ovan är i omfånget för indragningen.

    ```powershell
    Get-AzureADPolicy -All
    ```

1. Om du vill se vilka appar och tjänst huvud namn som är länkade till en speciell princip som du har identifierat kör du följande [Get-AzureADPolicyAppliedObject](/powershell/module/azuread/get-azureadpolicyappliedobject?view=azureadps-2.0-preview&preserve-view=true) -cmdlet genom att ersätta **1a37dad8-5da7-4cc8-87c7-efbc0326cf20** med något av dina princip-ID: n. Sedan kan du bestämma om du vill konfigurera inloggnings frekvensen för villkorlig åtkomst eller behålla standardinställningarna för Azure AD.

    ```powershell
    Get-AzureADPolicyAppliedObject -id 1a37dad8-5da7-4cc8-87c7-efbc0326cf20
    ```

Om din klient har principer som definierar anpassade värden för konfigurations egenskaperna för uppdatering och sessionstoken, rekommenderar Microsoft att du uppdaterar principerna till värden som motsvarar de standardinställningar som beskrivs ovan. Om inga ändringar görs kommer Azure AD automatiskt att respektera standardvärdena.  

## <a name="overview"></a>Översikt

Du kan ange livs längden för en token som utfärdats av Microsoft Identity Platform. Du kan ange token-livslängd för alla program i din organisation, för ett program med flera klientorganisationer eller för en specifik huvudtjänst i organisationen. Men vi stöder för närvarande inte konfigurering av livs längderna för de [hanterade identitets tjänstens huvud namn](../managed-identities-azure-resources/overview.md).

I Azure AD representerar ett princip objekt en uppsättning regler som tillämpas på enskilda program eller på alla program i en organisation. Varje princip typ har en unik struktur med en uppsättning egenskaper som tillämpas på objekt som de är tilldelade till.

Du kan ange en princip som standard princip för din organisation. Principen tillämpas på alla program i organisationen, så länge den inte åsidosätts av en princip med högre prioritet. Du kan också tilldela en princip till vissa program. Prioritetsordningen varierar efter princip typ.

Exempel [på hur du konfigurerar livs längd för token](configure-token-lifetimes.md)finns i exempel.

> [!NOTE]
> Konfigurations bara livs längds princip för token gäller endast för mobila och Station ära klienter som har åtkomst till SharePoint Online-och OneDrive för företag-resurser och gäller inte för webbläsarfönster.
> Om du vill hantera livs längden för webb läsar sessioner för SharePoint Online och OneDrive för företag, använder du funktionen [livs längd för session för villkorlig åtkomst](../conditional-access/howto-conditional-access-session-lifetime.md) . Se [SharePoint Online-bloggen](https://techcommunity.microsoft.com/t5/SharePoint-Blog/Introducing-Idle-Session-Timeout-in-SharePoint-and-OneDrive/ba-p/119208) om du vill veta mer om hur du konfigurerar tids gränser för inaktiv session.

## <a name="token-types"></a>Token-typer

Du kan ange livs längds principer för token för uppdateringstoken, åtkomsttoken, SAML-token, tokens och ID-token.

### <a name="access-tokens"></a>Åtkomsttokens

Klienter använder åtkomsttoken för att få åtkomst till en skyddad resurs. En åtkomsttoken kan bara användas för en speciell kombination av användare, klient och resurs. Åtkomsttoken kan inte återkallas och är giltiga tills deras förfallo datum har passerats. En skadlig aktör som har fått en åtkomsttoken kan använda den för sin livs längd. Att justera livs längden för en åtkomsttoken är en kompromiss mellan att förbättra systemets prestanda och att öka den tid som klienten behåller åtkomsten efter att användarens konto har inaktiverats. Förbättrad system prestanda uppnås genom att minska antalet gånger som en klient behöver skaffa en ny åtkomsttoken.  Standardvärdet är 1 timme – efter 1 timme, klienten måste använda uppdateringstoken till (normalt tyst) Hämta en ny uppdateringstoken och åtkomsttoken. 

### <a name="saml-tokens"></a>SAML-token

SAML-token används av många webbaserade SAAS-program och hämtas med hjälp av Azure Active Directorys SAML2-protokollets slut punkt. De används också av program som använder WS-Federation. Standard livstiden för token är 1 timme. Från ett programs perspektiv anges giltighets perioden för token av NotOnOrAfter-värdet för `<conditions …>` elementet i token. När giltighets perioden för token har upphört måste klienten initiera en ny autentiseringsbegäran, som ofta uppfylls utan interaktiv inloggning som ett resultat av sessionstoken för enkel inloggning (SSO).

Värdet för NotOnOrAfter kan ändras med hjälp av `AccessTokenLifetime` parametern i en `TokenLifetimePolicy` . Den ställs in på den livstid som kon figurer ATS i principen om det finns någon, plus en klock skev på fem minuter.

Den angivna NotOnOrAfter i `<SubjectConfirmationData>` elementet påverkas inte av konfigurationen för token livs längd. 

### <a name="refresh-tokens"></a>Uppdatera token

När en klient får åtkomst-token för åtkomst till en skyddad resurs får klienten också en uppdateringstoken. Uppdateringstoken används för att hämta nya token för åtkomst/uppdatering när den aktuella åtkomsttoken upphör att gälla. En uppdateringstoken är kopplad till en kombination av användare och klient. En uppdateringstoken kan [återkallas när som helst](access-tokens.md#token-revocation), och giltighets tiden för token kontrol leras varje gång token används.  Uppdaterade token återkallas inte när de används för att hämta nya åtkomsttoken – det är bäst att ta bort den gamla token på ett säkert sätt när du får en ny.

Det är viktigt att skilja mellan konfidentiella klienter och offentliga klienter, eftersom detta påverkar hur länge uppdaterade token kan användas. Mer information om olika typer av klienter finns i [RFC 6749](https://tools.ietf.org/html/rfc6749#section-2.1).

#### <a name="token-lifetimes-with-confidential-client-refresh-tokens"></a>Livs längd för token med konfidentiella klient uppdaterings-token
Konfidentiella klienter är program som säkert kan lagra ett klient lösen ord (hemligt). De kan bevisa att förfrågningar kommer från det skyddade klient programmet och inte från en skadlig aktör. En webbapp är till exempel en konfidentiell klient eftersom den kan lagra en klient hemlighet på webb servern. Den exponeras inte. Eftersom de här flödena är säkrare, går det inte att ändra standard livstiden för uppdateringstoken som utfärdats till dessa flöden `until-revoked` genom att använda principen och kommer inte att återkallas för frivilliga lösen ords återställning.

#### <a name="token-lifetimes-with-public-client-refresh-tokens"></a>Livs längd för token med offentliga klient uppdaterings-token

Offentliga klienter kan inte lagra ett klient lösen ord på ett säkert sätt (hemligt). En iOS/Android-app kan till exempel inte obfuscate en hemlighet från resurs ägaren, så den betraktas som en offentlig klient. Du kan ange principer för resurser för att förhindra att uppdateringstoken från offentliga klienter som är äldre än en angiven period hämtar ett nytt nyckel par för åtkomst/uppdatering. Det gör du genom att använda [egenskapen Refresh token Max inaktive](#refresh-token-max-inactive-time) rad ( `MaxInactiveTime` ). Du kan också använda principer för att ange en period utanför vilken uppdateringstoken inte längre accepteras. Det gör du genom att använda en [token för maximal ålder för en enskild faktor](#single-factor-session-token-max-age) eller [token för Multi-Factor session-token](#multi-factor-refresh-token-max-age) . Du kan justera livs längden för en uppdateringstoken för att styra när och hur ofta användaren måste ange autentiseringsuppgifter igen, i stället för att tyst autentiseras igen när du använder ett offentligt klient program.

> [!NOTE]
> Egenskapen max ålder är den tid som en enskild token kan användas. 

### <a name="id-tokens"></a>ID-tokens
ID-token skickas till webbplatser och interna klienter. ID-tokens innehåller profil information om en användare. En ID-token är kopplad till en speciell kombination av användare och klient. ID-token anses giltiga tills de upphör att gälla. Vanligt vis matchar ett webb program en användares sessions livs längd i programmet till livs längden för den ID-token som utfärdats för användaren. Du kan justera livs längden för en ID-token för att kontrol lera hur ofta webb programmet upphör att gälla, och hur ofta den kräver att användaren autentiseras med Microsoft Identity Platform (antingen tyst eller interaktivt).

### <a name="single-sign-on-session-tokens"></a>Token för enkel inloggning
När en användare autentiserar med Microsoft Identity Platform upprättas en enkel inloggnings session (SSO) med användarens webbläsare och Microsoft Identity Platform. SSO-token i form av en cookie representerar den här sessionen. SSO-sessionstoken är inte kopplat till ett specifik resurs-/klient program. SSO-sessionstoken kan återkallas och deras giltighet kontrol leras varje gång de används.

Microsoft Identity Platform använder två typer av SSO-tokens: beständiga och inte beständiga. Beständig sessionstoken lagras som beständiga cookies av webbläsaren. Token för inte beständig session lagras som sessionscookies. (Sessionscookies förstörs när webbläsaren stängs.) Vanligt vis lagras en beständig sessionstoken. Men när användaren markerar kryss rutan **Behåll mig inloggad** under autentiseringen, lagras en beständig sessionstoken.

Token för inte beständig session har en livs längd på 24 timmar. Permanenta token har en livs längd på 90 dagar. När en SSO-token används i sin giltighets period, utökas giltighets perioden till ett till 24 timmar eller 90 dagar, beroende på tokentyp. Om en SSO-token inte används inom sin giltighets tid betraktas det som förfallet och godkänns inte längre.

Du kan använda en princip för att ställa in tiden efter att den första sessionstoken har utfärdats utanför vilken sessionstoken inte längre accepteras. (Om du vill göra det använder du egenskapen token för sessionstoken max ålder.) Du kan justera livs längden för en sessions-token för att styra när och hur ofta en användare krävs för att ange autentiseringsuppgifter igen, i stället för att bli tyst autentiserad, när du använder ett webb program.

### <a name="token-lifetime-policy-properties"></a>Princip egenskaper för token livstid
En livs längds princip för token är en typ av princip objekt som innehåller livs längds regler för token. Använd egenskaperna för principen för att kontrol lera angivna livstider för token. Om ingen princip har angetts tillämpar systemet standard livstid svärdet.

### <a name="configurable-token-lifetime-properties"></a>Egenskaper för konfigurerbar token-livstid
| Egenskap | Princip egenskaps sträng | Nätverk | Standardvärde | Minimum | Maximal |
| --- | --- | --- | --- | --- | --- |
| Livstid för åtkomsttoken |AccessTokenLifetime<sup>2</sup> |Åtkomsttoken, ID-token, SAML2-token |1 timme |10 minuter |1 dag |
| Maximal inaktiv tid för uppdateringstoken |MaxInactiveTime |Uppdatera token |90 dagar |10 minuter |90 dagar |
| Högsta ålder för Single-Factor uppdaterad token |MaxAgeSingleFactor |Uppdatera tokens (för alla användare) |Tills den har återkallats |10 minuter |Till och med återkalla<sup>1</sup> |
| Högsta ålder för Multi-Factor Refresh-token |MaxAgeMultiFactor |Uppdatera tokens (för alla användare) | 180 dagar |10 minuter |180 dagar<sup>1</sup> |
| Högsta ålder för Single-Factor sessionstoken |MaxAgeSessionSingleFactor |Token för sessioner (beständiga och inte permanenta) |Tills den har återkallats |10 minuter |Till och med återkalla<sup>1</sup> |
| Högsta ålder för Multi-Factor session |MaxAgeSessionMultiFactor |Token för sessioner (beständiga och inte permanenta) | 180 dagar |10 minuter | 180 dagar<sup>1</sup> |

* <sup>1</sup>365 dagar är den maximala explicita längden som kan anges för dessa attribut.
* <sup>2</sup> För att säkerställa att Microsoft Teams webb klienten fungerar rekommenderar vi att du håller AccessTokenLifetime till mer än 15 minuter för Microsoft Teams.

### <a name="exceptions"></a>Undantag
| Egenskap | Nätverk | Standardvärde |
| --- | --- | --- |
| Uppdatera token max ålder (utfärdat för federerade användare som har otillräcklig åter kallelse information<sup>1</sup>) |Uppdatera tokens (utfärdat för federerade användare som har otillräcklig återkallnings information<sup>1</sup>) |12 timmar |
| Maximal inaktiv tid för uppdateringstoken (utfärdat för konfidentiella klienter) |Uppdatera tokens (utfärdat för konfidentiella klienter) |90 dagar |
| Maximal ålder för uppdateringstoken (utfärdat för konfidentiella klienter) |Uppdatera tokens (utfärdat för konfidentiella klienter) |Tills den har återkallats |

* <sup>1</sup> federerade användare som har otillräcklig information om återkallade certifikat inkluderar alla användare som inte har attributet "LastPasswordChangeTimestamp" synkroniserat. Dessa användare ges denna kortaste ålder eftersom Azure Active Directory inte kan verifiera när token som är knutna till en gammal autentiseringsuppgift (till exempel ett lösen ord som har ändrats) kan återkallas, och de måste komma tillbaka oftare för att säkerställa att användaren och tillhör ande tokens fortfarande är i ett lyckat position. För att förbättra den här upplevelsen måste klient administratörerna se till att de synkroniserar attributet "LastPasswordChangeTimestamp" (detta kan anges för användarobjektet med PowerShell eller via AADSync).

### <a name="policy-evaluation-and-prioritization"></a>Princip utvärdering och prioritering
Du kan skapa och tilldela en livs längd princip för token till ett särskilt program, till din organisation och till tjänstens huvud namn. Flera principer kan gälla för ett enskilt program. Den token-princip för token som börjar gälla följer dessa regler:

* Om en princip uttryckligen tilldelas till tjänstens huvud namn, tillämpas den.
* Om ingen princip tilldelas explicit till tjänstens huvud namn tillämpas en princip som uttryckligen tilldelas till den överordnade organisationen för tjänstens huvud namn.
* Om ingen princip har tilldelats till tjänstens huvud namn eller till organisationen tillämpas principen som tilldelats programmet.
* Om ingen princip har tilldelats till tjänstens huvud namn, organisation eller program objekt, tillämpas standardvärdena. (Mer information finns i tabellen i [konfigurations bara livs längds egenskaper för token](#configurable-token-lifetime-properties).)

Mer information om relationen mellan program objekt och tjänst huvud objekt finns i [program-och tjänst huvud objekt i Azure Active Directory](app-objects-and-service-principals.md).

Giltighet för token utvärderas vid den tidpunkt då token används. Principen med den högsta prioriteten för det program som används börjar gälla.

Alla tidsintervallen som används här är formaterade enligt C# [TimeSpan](/dotnet/api/system.timespan) -objekt-D. hh: mm: SS.  Så 80 dagar och 30 minuter skulle vara det `80.00:30:00` .  Den inledande D-funktionen kan tas bort om den är noll, så 90 minuter `00:90:00` .  

> [!NOTE]
> Här är ett exempel scenario.
>
> En användare vill ha åtkomst till två webb program: webb program A och webb program B.
> 
> Förhållanden
> * Båda webb programmen finns i samma överordnade organisation.
> * Token för token 1 med en sessionstoken max ålder på åtta timmar har angetts som överordnad organisations standard.
> * Webb program A är ett webb program som används ofta och inte är länkat till några principer.
> * Webb program B används för mycket känsliga processer. Tjänstens huvud namn är länkat till livs längd policy 2, som har en högsta ålder på en token på 30 minuter.
>
> Vid 12:00 PM startar användaren en ny webbläsarsession och försöker komma åt webb program A. Användaren omdirigeras till Microsoft Identity Platform och uppmanas att logga in. Detta skapar en cookie som har en sessionstoken i webbläsaren. Användaren omdirigeras tillbaka till webb program A med en ID-token som ger användaren åtkomst till programmet.
>
> Vid 12:15 PM försöker användaren komma åt webb program B. Webbläsaren omdirigeras till Microsoft Identity Platform, som identifierar sessionens cookie. Webb program B:s tjänstens huvud namn är länkat till livs längd policy 2, men är också en del av den överordnade organisationen, med standard livstid för token 1. Livs längd för token-princip 2 börjar gälla eftersom principer som är kopplade till tjänstens huvud namn har högre prioritet än organisationens standard principer. Sessionstoken utfärdades ursprungligen under de senaste 30 minuterna, så det anses vara giltigt. Användaren omdirigeras tillbaka till webb program B med en ID-token som ger åtkomst till dem.
>
> Vid 1:00 PM försöker användaren komma åt webb program A. Användaren omdirigeras till Microsoft Identity Platform. Webb program A är inte länkad till några principer, men eftersom den finns i en organisation med token för standard-token 1 börjar den tillämpas. Sessions-cookien som ursprungligen utfärdades under de senaste åtta timmarna har upptäckts. Användaren omdirigeras tyst tillbaka till webb program A med en ny ID-token. Användaren behöver inte autentiseras.
>
> Omedelbart efteråt försöker användaren komma åt webb program B. Användaren omdirigeras till Microsoft Identity Platform. Som tidigare börjar token för token-princip 2 gälla. Eftersom token utfärdades mer än 30 minuter sedan uppmanas användaren att ange sina inloggnings uppgifter igen. En ny session-token och ID-token utfärdas. Användaren kan sedan komma åt webb program B.
>
>

## <a name="configurable-policy-property-details"></a>Konfigurerbar princip egenskaps information
### <a name="access-token-lifetime"></a>Livstid för åtkomsttoken
**Sträng:** AccessTokenLifetime

**Påverkar:** Åtkomsttoken, ID-token, SAML-token

**Sammanfattning:** Den här principen styr hur länge åtkomst-och ID-token för den här resursen betraktas som giltiga. Att minska livs längden för åtkomsttoken minskar risken för att en åtkomsttoken eller ID-token används av en skadlig aktör under en längre tid. (De här token kan inte återkallas.) Kompromissen är att prestanda påverkas negativt, eftersom token måste ersättas oftare.

Ett exempel finns i [skapa en princip för webb inloggning](configure-token-lifetimes.md#create-a-policy-for-web-sign-in).

### <a name="refresh-token-max-inactive-time"></a>Maximal inaktiv tid för uppdateringstoken
**Sträng:** MaxInactiveTime

**Påverkar:** Uppdatera token

**Sammanfattning:** Den här principen styr hur gammal en uppdateringstoken kan vara innan en klient inte längre kan använda den för att hämta ett nytt nyckel par för åtkomst/uppdatering vid försök att få åtkomst till den här resursen. Eftersom en ny uppdateringstoken vanligt vis returneras när en uppdateringstoken används, förhindrar den här principen åtkomst om klienten försöker få åtkomst till en resurs med hjälp av den aktuella uppdateringstoken under den angivna tids perioden.

Den här principen tvingar användare som inte har varit aktiva på sin klient att autentiseras igen för att hämta en ny uppdateringstoken.

Egenskapen för maximal inaktiv tid för uppdateringstoken måste anges till ett lägre värde än max ålder för Single-Factor token och Multi-Factor Refresh-token.

Ett exempel finns i [skapa en princip för en intern app som anropar ett webb-API](configure-token-lifetimes.md#create-a-policy-for-a-native-app-that-calls-a-web-api).

### <a name="single-factor-refresh-token-max-age"></a>Högsta ålder för Single-Factor uppdaterad token
**Sträng:** MaxAgeSingleFactor

**Påverkar:** Uppdatera token

**Sammanfattning:** Den här principen styr hur länge en användare kan använda en uppdateringstoken för att hämta ett nytt nyckel par för åtkomst/uppdatering efter att de senast autentiserades genom att använda en enda faktor. När en användare autentiserar och tar emot en ny uppdateringstoken kan användaren använda det för att uppdatera token-flödet under den angivna tids perioden. (Detta är sant så länge den aktuella uppdateringstoken inte har återkallats och är inte kvar längre än den inaktiva tiden.) Då tvingas användaren att autentisera igen för att ta emot en ny uppdateringstoken.

Att minska den högsta åldern tvingar användare att autentisera sig oftare. Eftersom autentisering med en faktor anses vara mindre säker än Multi-Factor Authentication, rekommenderar vi att du ställer in den här egenskapen till ett värde som är lika med eller lägre än den högsta ålders egenskapen för Multi-Factor Refresh-token.

Ett exempel finns i [skapa en princip för en intern app som anropar ett webb-API](configure-token-lifetimes.md#create-a-policy-for-a-native-app-that-calls-a-web-api).

### <a name="multi-factor-refresh-token-max-age"></a>Högsta ålder för Multi-Factor Refresh-token
**Sträng:** MaxAgeMultiFactor

**Påverkar:** Uppdatera token

**Sammanfattning:** Den här principen styr hur länge en användare kan använda en uppdateringstoken för att hämta ett nytt nyckel par för åtkomst/uppdatering efter att de senast autentiserades med hjälp av flera faktorer. När en användare autentiserar och tar emot en ny uppdateringstoken kan användaren använda det för att uppdatera token-flödet under den angivna tids perioden. (Detta är sant så länge den aktuella uppdateringstoken inte återkallas och inte används längre än den inaktiva tiden.) Då tvingas användarna att autentisera igen för att få en ny uppdateringstoken.

Att minska den högsta åldern tvingar användare att autentisera sig oftare. Eftersom autentisering med en faktor anses vara mindre säker än Multi-Factor Authentication, rekommenderar vi att du ställer in den här egenskapen till ett värde som är lika med eller större än egenskapen Single-Factor Refresh token max ålder.

Ett exempel finns i [skapa en princip för en intern app som anropar ett webb-API](configure-token-lifetimes.md#create-a-policy-for-a-native-app-that-calls-a-web-api).

### <a name="single-factor-session-token-max-age"></a>Högsta ålder för Single-Factor sessionstoken
**Sträng:** MaxAgeSessionSingleFactor

**Påverkar:** Token för sessioner (beständiga och inte permanenta)

**Sammanfattning:** Den här principen styr hur länge en användare kan använda en sessionstoken för att hämta ett nytt ID och sessionstoken efter att de senast autentiserades genom att använda en enda faktor. När en användare autentiserar och tar emot en ny sessionstoken, kan användaren använda sessionens token-flöde under den angivna tids perioden. (Detta är sant så länge den aktuella sessionstoken inte har återkallats och har inte gått ut.) Efter den angivna tids perioden tvingas användaren att autentisera igen för att ta emot en ny sessionstoken.

Att minska den högsta åldern tvingar användare att autentisera sig oftare. Eftersom autentisering med en faktor anses vara mindre säker än Multi-Factor Authentication, rekommenderar vi att du ställer in den här egenskapen till ett värde som är lika med eller mindre än max ålders egenskapen för Multi-Factor session-token.

Ett exempel finns i [skapa en princip för webb inloggning](configure-token-lifetimes.md#create-a-policy-for-web-sign-in).

### <a name="multi-factor-session-token-max-age"></a>Högsta ålder för Multi-Factor session
**Sträng:** MaxAgeSessionMultiFactor

**Påverkar:** Token för sessioner (beständiga och inte permanenta)

**Sammanfattning:** Den här principen styr hur länge en användare kan använda en sessionstoken för att hämta ett nytt ID och sessionstoken efter den senaste gången de autentiserades genom att använda flera faktorer. När en användare autentiserar och tar emot en ny sessionstoken, kan användaren använda sessionens token-flöde under den angivna tids perioden. (Detta är sant så länge den aktuella sessionstoken inte har återkallats och har inte gått ut.) Efter den angivna tids perioden tvingas användaren att autentisera igen för att ta emot en ny sessionstoken.

Att minska den högsta åldern tvingar användare att autentisera sig oftare. Eftersom autentisering med en faktor anses vara mindre säker än Multi-Factor Authentication, rekommenderar vi att du ställer in den här egenskapen till ett värde som är lika med eller större Single-Factor än max ålders egenskapen för sessionstoken.

## <a name="cmdlet-reference"></a>Cmdlet-referens

Dessa är cmdletarna i [Azure Active Directory PowerShell för för hands versions modulen för diagram](/powershell/module/azuread/?view=azureadps-2.0-preview#service-principals&preserve-view=true&preserve-view=true).

### <a name="manage-policies"></a>Hantera principer

Du kan använda följande cmdletar för att hantera principer.

| Cmdlet | Beskrivning | 
| --- | --- |
| [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | Skapar en ny princip. |
| [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | Hämtar alla Azure AD-principer eller en angiven princip. |
| [Get-AzureADPolicyAppliedObject](/powershell/module/azuread/get-azureadpolicyappliedobject?view=azureadps-2.0-preview&preserve-view=true) | Hämtar alla appar och tjänst huvud namn som är länkade till en princip. |
| [Set-AzureADPolicy](/powershell/module/azuread/set-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | Uppdaterar en befintlig princip. |
| [Remove-AzureADPolicy](/powershell/module/azuread/remove-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | Tar bort den angivna principen. |

### <a name="application-policies"></a>Användningsprinciper
Du kan använda följande cmdletar för användnings principer.</br></br>

| Cmdlet | Beskrivning | 
| --- | --- |
| [Add-AzureADApplicationPolicy](/powershell/module/azuread/add-azureadapplicationpolicy?view=azureadps-2.0-preview&preserve-view=true) | Länkar den angivna principen till ett program. |
| [Get-AzureADApplicationPolicy](/powershell/module/azuread/get-azureadapplicationpolicy?view=azureadps-2.0-preview&preserve-view=true) | Hämtar principen som är tilldelad ett program. |
| [Remove-AzureADApplicationPolicy](/powershell/module/azuread/remove-azureadapplicationpolicy?view=azureadps-2.0-preview&preserve-view=true) | Tar bort en princip från ett program. |

### <a name="service-principal-policies"></a>Principer för tjänstens huvud namn
Du kan använda följande cmdletar för principer för tjänstens huvud namn.

| Cmdlet | Beskrivning | 
| --- | --- |
| [Add-AzureADServicePrincipalPolicy](/powershell/module/azuread/add-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) | Länkar den angivna principen till ett huvud namn för tjänsten. |
| [Get-AzureADServicePrincipalPolicy](/powershell/module/azuread/get-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) | Hämtar en princip som är länkad till det angivna huvud namnet för tjänsten.|
| [Remove-AzureADServicePrincipalPolicy](/powershell/module/azuread/remove-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) | Tar bort principen från det angivna huvud namnet för tjänsten.|

## <a name="license-requirements"></a>Licenskrav

Om du använder den här funktionen krävs en licens för Azure AD Premium P1. Information om rätt licens för dina krav finns i [jämföra allmänt tillgängliga funktioner i de kostnads fria versionerna och Premium-versionerna](https://azure.microsoft.com/pricing/details/active-directory/).

Kunder med [Microsoft 365 Business licenser](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description) har också till gång till funktioner för villkorlig åtkomst.

## <a name="next-steps"></a>Nästa steg

Läs mer i [exempel på hur du konfigurerar livs längd för token](configure-token-lifetimes.md).