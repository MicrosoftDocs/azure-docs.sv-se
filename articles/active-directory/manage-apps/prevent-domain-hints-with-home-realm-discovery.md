---
title: Förhindra automatisk acceleration av inloggning i Azure AD med identifieringsprincip för hemsfär
description: Lär dig hur du domain_hint automatisk acceleration till federerade IDE:er.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/12/2021
ms.author: iangithinji
ms.reviewer: hirsin
ms.openlocfilehash: b89e0e1c8bd8109fac8b4b7c05a845a3e234b617
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375558"
---
# <a name="disable-auto-acceleration-to-a-federated-idp-during-user-sign-in-with-home-realm-discovery-policy"></a>Inaktivera automatisk acceleration till en federerad IDP under användar inloggning med identifieringsprincip för hemsfär

[Identifieringsprincip för hemsfär](/graph/api/resources/homeRealmDiscoveryPolicy) (HRD) erbjuder administratörer flera sätt att styra hur och var deras användare autentiseras. Avsnittet i HRD-principen används för att migrera federerade användare till moln hanterade autentiseringsuppgifter som FIDO genom att se till att de alltid besöker Azure AD-inloggningssidan och inte automatiskt accelereras till `domainHintPolicy` en federerad [](../authentication/howto-authentication-passwordless-security-key.md)IDP på grund av domäntips.

Den här principen behövs i situationer där program som en administratör inte kan styra eller uppdatera lägga till domäntips under inloggningen.  Skickar till exempel användaren till en inloggningssida med parametern tillagd för att automatiskt påskynda användaren direkt till `outlook.com/contoso.com` `&domain_hint=contoso.com` den federerade IDP:n för `contoso.com` domänen. Användare med hanterade autentiseringsuppgifter som skickas till en federerad IDP kan inte logga in med sina hanterade autentiseringsuppgifter, vilket minskar säkerheten och frustrerande användare med slumpmässig inloggning. Administratörer som distribuerar hanterade [autentiseringsuppgifter bör också konfigurera den här principen för](#suggested-use-within-a-tenant) att säkerställa att användarna alltid kan använda sina hanterade autentiseringsuppgifter.

## <a name="domainhintpolicy-details"></a>DomainHintPolicy-information

Avsnittet DomainHintPolicy i HRD-principen är ett JSON-objekt som gör att en administratör kan välja bort vissa domäner och program från domäntipsanvändningen.  Funktionellt talar detta om för Azure AD-inloggningssidan att den ska bete sig som om en parameter i `domain_hint` inloggningsbegäran inte fanns.

### <a name="the-respect-and-ignore-policy-sections"></a>Avsnitten Respektera och Ignorera princip

|Avsnitt | Innebörd | Värden |
|--------|---------|--------|
|`IgnoreDomainHintForDomains` |Om det här domäntipset skickas i begäran ignorerar du det. |Matris med domänadresser (till exempel `contoso.com` ). Har även stöd för `all_domains`|
|`RespectDomainHintForDomains`| Om det här domäntipset skickas i begäran ska du respektera det även om anger att appen i begäran `IgnoreDomainHintForApps` inte ska accelerera automatiskt. Detta används för att sakta ned utfasningen av domäntips i nätverket – du kan ange att vissa domäner fortfarande ska påskyndas. | Matris med domänadresser (till exempel `contoso.com` ). Har även stöd för `all_domains`|
|`IgnoreDomainHintForApps`| Om en begäran från det här programmet kommer med en domäntips ignorerar du den. | Matris med program-ID:n (GUID). Har även stöd för `all_apps`|
|`RespectDomainHintForApps` |Om en begäran från det här programmet kommer med en domäntips ska du respektera den även om `IgnoreDomainHintForDomains` den domänen ingår. Används för att säkerställa att vissa appar fortsätter att fungera om du upptäcker att de bryts utan domäntips. | Matris med program-ID:n (GUID). Har även stöd för `all_apps`|

### <a name="policy-evaluation"></a>Principutvärdering

DomainHintPolicy-logiken körs på varje inkommande begäran som innehåller en domäntips och accelererar baserat på två datadelar i begäran – domänen i domäntipset och klient-ID:t (appen). I korthet – "Respektera" för en domän eller app har företräde framför en instruktion att "ignorera" en domäntips för en viss domän eller ett visst program.

1. Om det inte finns någon domäntipsprincip, eller om inget av de fyra avsnitten refererar till appen eller domäntipset, utvärderas resten av [HRD-principen](configure-authentication-for-federated-users-portal.md#priority-and-evaluation-of-hrd-policies).
1. Om antingen en (eller båda) av eller -avsnittet innehåller appen eller domäntipset i begäran, så accelereras användaren automatiskt till `RespectDomainHintForApps` `RespectDomainHintForDomains` den federerade IDP:n på begäran.
1. Om antingen en (eller båda) av eller refererar till appen eller domäntipset i begäran och de inte refereras till av avsnitten "Respektera" påskyndas inte begäran automatiskt och användaren finns kvar på `IgnoreDomainHintsForApps` Azure AD-inloggningssidan för att ange ett `IgnoreDomainHintsForDomains` användarnamn.

När en användare har angett ett användarnamn på inloggningssidan kan de använda sina hanterade autentiseringsuppgifter, om de har registrerat sig.  Om de väljer att inte använda hanterade autentiseringsuppgifter, eller om de inte har någon registrerad, kommer de att tas till sin federerade IDP för att ange autentiseringsuppgifter som vanligt.

## <a name="suggested-use-within-a-tenant"></a>Föreslagen användning i en klientorganisation

Administratörer av federerade domäner bör konfigurera det här avsnittet av HRD-principen i en plan med fyra faser. Målet med den här planen är att alla användare i en klientorganisation så småningom ska ha möjlighet att använda sina hanterade autentiseringsuppgifter oavsett domän eller program, och spara de appar som är beroende av `domain_hint` användning.  Den här planen hjälper administratörer att hitta dessa appar, undanta dem från den nya principen och fortsätta att distribuera ändringen till resten av klientorganisationen.

1. Välj en domän som den här ändringen ska distribueras till.  Det här är din testdomän, så välj en som kan vara mer öppen för ändringar i UX (dvs. se en annan inloggningssida).  Detta ignorerar alla domäntips från alla program som använder det här domännamnet. [Ange](#configuring-policy-through-graph-explorer) den här principen i din hrd-standardprincip för klientorganisationen:

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "testDomain.com" ], 
    "RespectDomainHintForDomains": [], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": [] 
} 
```

2. Samla in feedback från testdomänanvändarna. Samla in information för program som brutits på grund av den här ändringen – de är beroende av domäntipsanvändning och bör uppdateras. För tillfället lägger du till dem i `RespectDomainHintForApps` avsnittet:

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "testDomain.com" ], 
    "RespectDomainHintForDomains": [], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": ["app1-clientID-Guid", "app2-clientID-Guid] 
} 
```

3. Fortsätt att expandera utökningen av principen till nya domäner och samla in mer feedback.

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "testDomain.com", "otherDomain.com", "anotherDomain.com"], 
    "RespectDomainHintForDomains": [], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": ["app1-clientID-Guid", "app2-clientID-Guid] 
} 
```

4. Slutför din utrullning – rikta in dig på alla domäner och undanta dem som ska fortsätta att accelereras:

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "*" ], 
    "RespectDomainHintForDomains": ["guestHandlingDomain.com"], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": ["app1-clientID-Guid", "app2-clientID-Guid] 
} 
```

När steg 4 har slutförts kan alla användare, förutom de i , logga in på Azure AD-inloggningssidan även om domäntips annars skulle orsaka en automatisk acceleration till `guestHandlingDomain.com` en federerad IDP.  Undantaget till detta är om appen som begär inloggning är en av de undantagna – för dessa appar kommer alla domäntips fortfarande att godkännas.

## <a name="configuring-policy-through-graph-explorer"></a>Konfigurera princip via Graph Explorer

Ange [HRD-principen som](/graph/api/resources/homeRealmDiscoveryPolicy) vanligt med hjälp av Microsoft Graph.  

1. Ge behörigheten Policy.ReadWrite.ApplicationConfiguration i [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer).  
1. Använd URL:en `https://graph.microsoft.com/v1.0/policies/homeRealmDiscoveryPolicies`
1. PUBLICERA den nya principen till den här URL:en eller PATCH `/policies/homerealmdiscoveryPolicies/{policyID}` till den om du skriver över en befintlig.

POST- eller PATCH-innehåll:

```json
{
    "displayName":"Home Realm Discovery Domain Hint Exclusion Policy",
    "definition":[
        "{\"HomeRealmDiscoveryPolicy\" : {\"DomainHintPolicy\": { \"IgnoreDomainHintForDomains\": [ \"Contoso.com\" ], \"RespectDomainHintForDomains\": [], \"IgnoreDomainHintForApps\": [\"sample-guid-483c-9dea-7de4b5d0a54a\"], \"RespectDomainHintForApps\": [] } } }"
    ],
    "isOrganizationDefault":true
}
```

Se till att använda snedstreck för att undvika `Definition` JSON-avsnittet när du använder Graph.  

`isOrganizationDefault` måste vara sant, men displayName och definitionen kan ändras.

## <a name="next-steps"></a>Nästa steg

* [Aktivera inloggning med lösenordsfri säkerhetsnyckel](../authentication/howto-authentication-passwordless-security-key.md)
* [Aktivera lösenordsfri inloggning med Microsoft Authenticator appen](../authentication/howto-authentication-passwordless-phone.md)