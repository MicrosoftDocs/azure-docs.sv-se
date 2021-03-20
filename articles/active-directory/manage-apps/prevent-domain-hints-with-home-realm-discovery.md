---
title: Förhindra inloggning automatiskt acceleration i Azure AD med hjälp av identifierings princip för start sfär
description: 'Lär dig hur du förhindrar domain_hint automatisk acceleration till federerade IDP: er.'
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/12/2021
ms.author: hirsin
ms.openlocfilehash: 53dfdfaf37695059d6d52428c2ba109970d9f7f7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104589386"
---
# <a name="disable-auto-acceleration-to-a-federated-idp-during-user-sign-in-with-home-realm-discovery-policy"></a>Inaktivera automatisk acceleration till en federerad IDP under användar inloggning med princip för identifiering av start sfär

HRD ( [Home sfär Discovery policy](/graph/api/resources/homeRealmDiscoveryPolicy) ) erbjuder administratörer flera sätt att styra hur och var deras användare autentiseras. `domainHintPolicy`Avsnittet i HRD-principen används för att hjälpa till att migrera federerade användare till moln hanterade autentiseringsuppgifter som [Fido](../authentication/howto-authentication-passwordless-security-key.md), genom att se till att de alltid besöker inloggnings sidan för Azure AD och inte accelererar automatiskt till en federerad IDP på grund av domän tips.

Den här principen behövs i situationer där program en administratör inte kan styra eller uppdatera Lägg till domän tips under inloggningen.  Till exempel `outlook.com/contoso.com` skickar användaren till en inloggnings sida med `&domain_hint=contoso.com` parametern append, för att automatiskt påskynda användaren direkt till den federerade IDP för `contoso.com` domänen. Användare med hanterade autentiseringsuppgifter som skickas till en federerad IDP kan inte logga in med sina hanterade autentiseringsuppgifter, vilket minskar säkerheten och frustrerande användare med slumpmässiga inloggnings upplevelser. Administratörer som hanterar hanterade autentiseringsuppgifter [bör också konfigurera den här principen](#suggested-use-within-a-tenant) för att säkerställa att användarna alltid kan använda sina hanterade autentiseringsuppgifter.

## <a name="domainhintpolicy-details"></a>DomainHintPolicy-information

Avsnittet DomainHintPolicy i HRD-principen är ett JSON-objekt som gör att en administratör kan välja mellan vissa domäner och program från användningen av domän tips.  Funktionen visar att inloggnings sidan för Azure AD fungerar som om det inte fanns någon `domain_hint` parameter i inloggningsbegäran.

### <a name="the-respect-and-ignore-policy-sections"></a>Avsnittet angående och ignorera principer

|Avsnitt | Innebörd | Värden |
|--------|---------|--------|
|`IgnoreDomainHintForDomains` |Om detta domän tips skickas i begäran, ignorera det. |Matris med domän adresser (till exempel `contoso.com` ). Stöder även `all_domains`|
|`RespectDomainHintForDomains`| Om det här domän tipset skickas i begäran bör du respektera det även om `IgnoreDomainHintForApps` anger att appen i begäran inte ska accelerera automatiskt. Detta används för att sakta ned distributionen av föråldrade domän tips i nätverket – du kan ange att vissa domäner fortfarande ska accelereras. | Matris med domän adresser (till exempel `contoso.com` ). Stöder även `all_domains`|
|`IgnoreDomainHintForApps`| Om en begäran från det här programmet levereras med ett domän tips ignorerar du det. | Matris med program-ID: n (GUID). Stöder även `all_apps`|
|`RespectDomainHintForApps` |Om en begäran från det här programmet levereras med ett domän tips bör du respektera den även om `IgnoreDomainHintForDomains` inkluderar den domänen. Används för att säkerställa att vissa appar fortsätter att fungera om du upptäcker att de bryter mot domän tips. | Matris med program-ID: n (GUID). Stöder även `all_apps`|

### <a name="policy-evaluation"></a>Princip utvärdering

DomainHintPolicy-logiken körs på varje inkommande begäran som innehåller ett domän tips och påskyndar baserat på två data mängder i begäran – domänen i domän tipset och klient-ID (appen). I kort-"respektera" för en domän eller app har företräde framför en instruktion till "Ignorera" ett domän tips för en specifik domän eller ett program.

1. Om någon domän tips princip saknas, eller om ingen av de 4 avsnitten refererar till appen eller domän tipset som nämns, [kommer resten av HRD-principen att utvärderas](configure-authentication-for-federated-users-portal.md#priority-and-evaluation-of-hrd-policies).
1. Om antingen ett (eller båda) av `RespectDomainHintForApps` eller- `RespectDomainHintForDomains` avsnittet innehåller appen eller domän tipset i begäran kommer användaren automatiskt att accelereras till den federerade IDP enligt begäran.
1. Om antingen en (eller båda) `IgnoreDomainHintsForApps` eller `IgnoreDomainHintsForDomains` refererar till appen eller domän tipset i begäran, och de inte refereras till i "respektera"-avsnitten, kommer begäran inte att accelerera automatiskt och användaren kommer att finnas kvar på inloggnings sidan för Azure AD för att ange ett användar namn.

När en användare har angett ett användar namn på inloggnings sidan kan de använda sina hanterade autentiseringsuppgifter, om de har registrerat sig.  Om de väljer att inte använda en hanterad autentiseringsuppgift, eller om de inte har registrerats, kommer de att tas till deras federerade IDP för Credential-poster som vanligt.

## <a name="suggested-use-within-a-tenant"></a>Föreslagen användning i en klient

Administratörer av federerade domäner bör konfigurera det här avsnittet av HRD-principen i en plan i fyra faser. Syftet med den här planen är att alla användare i en klient organisation har möjlighet att använda sina hanterade autentiseringsuppgifter oavsett domän eller program, spara de appar som har hårda beroenden `domain_hint` .  Den här planen hjälper administratörer att hitta dessa appar, undanta dem från den nya principen och fortsätta att distribuera ändringen till resten av klient organisationen.

1. Välj en domän för att börja om den här ändringen till.  Det här är din test domän, så välj en som kan vara mer mottaglig för ändringar i UX (t. ex. en annan inloggnings sida).  Detta kommer att ignorera alla domän tips från alla program som använder det här domän namnet. [Ange](#configuring-policy-through-graph-explorer) den här principen i din klient-standard princip för HRD:

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "testDomain.com" ], 
    "RespectDomainHintForDomains": [], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": [] 
} 
```

2. Samla in feedback från användare av test domän. Samla in information om program som är kopplade till följd av den här ändringen – de har ett beroende av användningen av domän tips och bör uppdateras. Nu ska du lägga till dem i `RespectDomainHintForApps` avsnittet:

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "testDomain.com" ], 
    "RespectDomainHintForDomains": [], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": ["app1-clientID-Guid", "app2-clientID-Guid] 
} 
```

3. Fortsätt att utöka distributionen av principen till nya domäner, samla in mer feedback.

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "testDomain.com", "otherDomain.com", "anotherDomain.com"], 
    "RespectDomainHintForDomains": [], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": ["app1-clientID-Guid", "app2-clientID-Guid] 
} 
```

4. Slutför distributionen – rikta alla domäner och undanta dem som ska fortsätta att accelerera:

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "*" ], 
    "RespectDomainHintForDomains": ["guestHandlingDomain.com"], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": ["app1-clientID-Guid", "app2-clientID-Guid] 
} 
```

När steg 4 har slutförts för alla användare, förutom de i `guestHandlingDomain.com` , kan de logga in på inloggnings sidan för Azure AD, även om domän tips annars skulle orsaka en automatisk acceleration av en FEDERERAD IDP.  Undantaget till detta är om appen som begär inloggning är en av de undantagna för dessa appar, kommer alla domän tips fortfarande att accepteras.

## <a name="configuring-policy-through-graph-explorer"></a>Konfigurera principer via Graph Explorer

Ange [HRD-principen](/graph/api/resources/homeRealmDiscoveryPolicy) som vanligt, med hjälp av Microsoft Graph.  

1. Bevilja behörigheten policy. ReadWrite. ApplicationConfiguration i [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer).  
1. Använd URL: en `https://graph.microsoft.com/v1.0/policies/homeRealmDiscoveryPolicies`
1. PUBLICERA den nya principen till den här URL: en eller korrigera `/policies/homerealmdiscoveryPolicies/{policyID}` den om du skriver över en befintlig.

POST-eller KORRIGERINGs innehåll:

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

`isOrganizationDefault` måste vara true, men displayName och definition kan ändras.

## <a name="next-steps"></a>Nästa steg

* [Aktivera inloggning med lösen ords skydd](../authentication/howto-authentication-passwordless-security-key.md)
* [Aktivera inloggning med lösen ord med Microsoft Authenticator-appen](../authentication/howto-authentication-passwordless-phone.md)