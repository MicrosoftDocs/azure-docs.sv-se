---
title: Vanliga frågor och Azure verifierbara autentiseringsuppgifter (för hands version)
description: Få svar på vanliga frågor om verifierbara autentiseringsuppgifter
author: barclayn
manager: davba
ms.service: identity
ms.subservice: verifiable-credentials
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: 3c43cfb537c84fb25a6bf879d32a8034342ff605
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2021
ms.locfileid: "106280725"
---
# <a name="frequently-asked-questions-faq"></a>Vanliga frågor och svar

Den här sidan innehåller vanliga frågor om verifierbara autentiseringsuppgifter och decentraliserad identitet. Frågorna är ordnade i följande avsnitt.

- [Vokabulär och grundläggande information](#the-basics)
- [Konceptuella frågor om decentraliserad identitet](#conceptual-questions)
- [Frågor om att använda för hands versionen av verifierbara autentiseringsuppgifter](#using-the-preview)

> [!IMPORTANT]
> Azure Active Directory verifierbara autentiseringsuppgifter finns för närvarande i en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="the-basics"></a>Grunderna

### <a name="what-is-a-did"></a>Vad är en? 

Decentraliserade Identifers (DIDs) är identifierare som kan användas för att skydda åtkomsten till resurser, signera och verifiera autentiseringsuppgifter och under lätta program data utbyte. Till skillnad från traditionella användar namn och e-postadresser ägs DIDs och styrs av själva entiteten (vara en person, enhet eller företag). DIDs finns oberoende av externa organisationer eller betrodda mellanliggande. I [förcentraliserade W3C-specifikationen](https://www.w3.org/TR/did-core/) beskrivs detta i detalj.

### <a name="why-do-we-need-a-did"></a>Varför behöver vi en?

Digitalt förtroende kräver att deltagare äger och kontrollerar sina identiteter och identiteten börjar på identifieraren.
I en ålder av dagliga, storskaliga system överträdelser och attacker på centraliserad identifierare honeypots, blir decentraliserad identitet ett kritiskt säkerhets behov för konsumenter och företag.
Personer som äger och kontrollerar sina identiteter kan utbyta verifierbara data och bevis. En distribuerad Credential-miljö möjliggör automatisering av många affärs processer som för närvarande är manuella och arbets krävande.

### <a name="what-is-a-verifiable-credential"></a>Vad är en verifierbar autentiseringsuppgift? 

Autentiseringsuppgifterna är en del av vår dagliga livs längd; driv rutins licenser används för att fastställa att vi kan driva ett motor fordon, universitets grader kan användas för att tala om vår utbildnings nivå och utfärdande av utfärdande av myndigheter för att vi ska kunna resa mellan länder. Autentiseringsuppgifter för verifierbara är en mekanism för att uttrycka dessa typer av autentiseringsuppgifter på webben på ett sätt som är kryptografiskt säkert, sekretess och maskin medveten. I [specifikationen W3C verifierbara autentiseringsuppgifter](https://www.w3.org/TR/vc-data-model//) förklaras detta i detalj.


## <a name="conceptual-questions"></a>Konceptuella frågor

### <a name="what-happens-when-a-user-loses-their-phone-can-they-recover-their-identity"></a>Vad händer när en användare förlorar sin telefon? Kan de återställa sin identitet?

Det finns flera sätt att erbjuda en återställnings metod för användarna, var och en med sina egna kompromisser. Vi utvärderar för närvarande alternativ och utformar metoder för återställning som ger bekvämlighet och säkerhet samtidigt som det gäller en användares sekretess och själv suveränitet.

### <a name="how-can-a-user-trust-a-request-from-an-issuer-or-verifier-how-do-they-know-a-did-is-the-real-did-for-an-organization"></a>Hur kan en användare lita på en begäran från en utfärdare eller Verifier? Hur vet man att en har varit i verkligheten för en organisation?

Vi har implementerat [den decentraliserade konfigurations specifikationen för identitets stiftelsen](https://identity.foundation/.well-known/resources/did-configuration/) för att ansluta en till ett starkt känt befintligt system, domän namn. Varje skapades med Azure Active Directory verifierbara autentiseringsuppgifter kan du välja att inkludera ett rot domän namn som ska kodas i dokumentet. Följ artikeln med rubriken [Länka din domän till din distribuerade ID](how-to-dnsbind.md) om du vill veta mer.  

### <a name="why-does-the-verifiable-credential-preview-use-ion-as-its-did-method-and-therefore-bitcoin-to-provide-decentralized-public-key-infrastructure"></a>Varför använder för hands versionen av verifierbara autentiseringsuppgifter Jon som sin DID-metod och därför Bitcoin att tillhandahålla decentraliserad infrastruktur för offentliga nycklar?

Jon är ett decentraliserat, bevarat, skalbart och decentraliserat ID Layer 2-nätverk som kör ovanpå Bitcoin. Den uppnår skalbarhet utan att inkludera en speciell cryptoasset-token, betrodda verifierare eller centralt vedertagna metoder. Vi använder Bitcoin för bas lagret 1 substrat på grund av styrkan i det decentraliserade nätverket att tillhandahålla en hög grad av oföränderlighets för ett kronologiskt händelse post system.

## <a name="using-the-preview"></a>Använda för hands versionen

### <a name="why-must-i-use-nodejs-for-the-verifiable-credentials-preview-any-plans-for-other-programming-languages"></a>Varför måste jag använda NodeJS för för hands versionen av verifierbara autentiseringsuppgifter? Eventuella planer för andra programmeringsspråk? 

Vi valde NodeJS eftersom det är en mycket populär plattform för programutvecklare. Vi kommer att släppa ett REST-API som gör det möjligt för utvecklarna att utfärda och verifiera autentiseringsuppgifter. 

### <a name="is-any-of-the-code-used-in-the-preview-open-source"></a>Är någon av de koder som används i förhands granskningen öppen källkod?

Ja! Följande databaser är de komponenter som har öppen källkod i våra tjänster.

1. [SideTree, på GitHub](https://github.com/decentralized-identity/sidetree)
2. [VC SDK för Node, på GitHub](https://github.com/microsoft/VerifiableCredentials-Verification-SDK-Typescript)
3. Ett [Android SDK för att bygga decentraliserade plån boks bok på GitHub](https://github.com/microsoft/VerifiableCredential-SDK-Android)
4. Ett [iOS SDK för att bygga decentraliserade plån boks boks-och GitHub](https://github.com/microsoft/VerifiableCredential-SDK-iOS)


## <a name="what-are-the-licensing-requirements"></a>Vilka är licensieringskraven?

En Azure AD P2-licens krävs för att kunna använda förhands granskningen av verifierbara autentiseringsuppgifter. Detta är ett tillfälligt krav som vi förväntar oss att den här tjänsten faktureras baserat på användning. 


## <a name="next-steps"></a>Nästa steg

- [Anpassa dina Azure Active Directory verifierbara autentiseringsuppgifter](credential-design.md)
