---
title: Introduktion till Azure Active Directory verifierbara autentiseringsuppgifter (för hands version)
description: En översikt av autentiseringsuppgifter för Azure verifierbar.
services: active-directory
author: barclayn
manager: daveba
editor: ''
ms.service: identity
ms.subservice: verifiable-credentials
ms.topic: overview
ms.date: 04/01/2021
ms.author: barclayn
ms.reviewer: ''
ms.openlocfilehash: 4ae7ad186e2ed8868be6205d48e9cfbef525d810
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2021
ms.locfileid: "106172819"
---
# <a name="introduction-to-azure-active-directory-verifiable-credentials-preview"></a>Introduktion till Azure Active Directory verifierbara autentiseringsuppgifter (för hands version)

> [!IMPORTANT]
> Azure Active Directory verifierbara autentiseringsuppgifter finns för närvarande i en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Våra digitala och fysiska liv är alltmer länkade till appar, tjänster och enheter som vi använder för att få åtkomst till en omfattande uppsättning upplevelser. Den här digitala omvandlingen gör att vi kan interagera med hundratals företag och tusentals andra användare på sätt som tidigare inte kan ändras.

Men identitets data har för ofta exponerats i säkerhets överträdelser. Dessa överträdelser påverkas av personers liv som påverkar vår sociala, professionella och ekonomiska liv. Microsoft anser att det finns ett bättre sätt. Varje person har rätt till en identitet som de äger och styr, en som på ett säkert sätt lagrar element i sin digitala identitet och bevarar sekretess. I den här huvud gruppen förklaras hur vi ansluter sig till händerna med en stor community för att skapa en öppen, säker, interoperabl och standardbaserad decentraliserad identitets lösning (lösning) för enskilda användare och organisationer.

## <a name="why-we-need-decentralized-identity"></a>Varför vi behöver decentraliserad identitet 

Idag använder vi vår digitala identitet på arbetet, hemma och över alla appar, tjänster och enheter som vi använder. Det består av allt vi säger, gör och upplever våra livet – Köp biljetter för en händelse, att checka in till ett hotell eller till och med beställa lunch. För närvarande äger vår identitet och alla våra digitala interaktioner och styrs av andra parter, som vi inte ens känner till.

I allmänhet beviljar användare medgivande till flera appar och enheter. Den här metoden kräver en hög grad av vaksamhet på användarens sida för att spåra vem som har åtkomst till den information som krävs. På företagets fram kräver samarbete med konsumenter och partners hög touch genom att utbyta data på ett säkert sätt på ett sätt som upprätthåller sekretess och säkerhet för alla som ingår.

Vi tror att ett standardiserat decentraliserat identitets system kan låsa upp en ny uppsättning upplevelser som gör det möjligt för användare och organisationer att få bättre kontroll över sina data, och ge en högre grad av förtroende och säkerhet för appar, enheter och tjänst leverantörer

## <a name="lead-with-open-standards"></a>Lead med öppna standarder 

Vi strävar efter att samar beta med kunder, partners och communityn för att låsa upp nästa generations decentraliserade identitetsbaserade upplevelser och vi är glada över att vara partner med de personer och organisationer som gör fantastiska bidrag i detta utrymme. Om det ursprungliga eko systemet är att växa, standarder, tekniska komponenter och kod slut produkter måste vara öppen källkod och tillgänglig för alla.

Microsoft samarbetar aktivt med medlemmar i uncentraliserad Identity Foundation (DIF), gruppens autentiseringsuppgifter för W3C och den större identitets communityn. Vi arbetar med dessa grupper för att identifiera och utveckla kritiska standarder och följande standarder har implementerats i våra tjänster.

* [Förcentraliserade W3C-identifierare](https://www.w3.org/TR/did-core/)
* [Autentiseringsuppgifter för W3C verifierbar](https://www.w3.org/TR/vc-data-model/)
* [DIF-Sidetree](https://identity.foundation/sidetree/spec/)
* [Den välkända DIF-konfigurationen](https://identity.foundation/specs/did-configuration/)
* [DIF DID-SIOP](https://identity.foundation/did-siop/)
* [DIF presentations utbyte](https://identity.foundation/presentation-exchange/)


## <a name="what-are-dids"></a>Vad är DIDs 

Innan vi kan förstå DIDs hjälper det att jämföra dem med aktuella identitets system. E-postadresser och sociala nätverks-ID: n är human-vänliga alias för samarbete, men är nu överbelastade för att fungera som kontroll punkter för data åtkomst i många olika scenarier utanför samarbetet. Detta skapar ett potentiellt problem, eftersom åtkomst till dessa ID: n kan tas bort när som helst av externa parter.

Decentraliserade identifierare (DIDs) skiljer sig åt. DIDs är användardefinierade, egna, globalt unika identifierare som är rotade i decentraliserade system som Jon. De har unika egenskaper, t. ex. större garantier för oföränderlighets, censorship resistens och manipulering evasiveness. Dessa attribut är kritiska för alla ID-system som är avsedda att ge dig själv ägande rätt och användar kontroll. 

Microsofts lösning för verifierbara autentiseringsuppgifter använder decentraliserade autentiseringsuppgifter (DIDs) för att kryptografiskt signera som bevis på att en förlitande part (Verifier) bestyrkar information som visar att de är ägare till verifierbara autentiseringsuppgifter. Därför rekommenderas en grundläggande förståelse av decentraliserade identifierare för alla som skapar en verifierbar lösning för autentiseringsuppgifter baserat på Microsoft-erbjudandet.
## <a name="what-are-verifiable-credentials"></a>Vad är verifierbara autentiseringsuppgifter 

 Vi använder ID: n i vår dagliga livs längd. Vi har driv rutins licenser som vi använder som bevis på vår förmåga att driva en bil. Universitet utfärdar utbildnings bevis som visar att vi har uppnått en utbildnings nivå. Vi använder Passport för att bevisa vem vi är ansvariga för när vi kommer till andra länder. Data modellen beskriver hur vi kan hantera dessa typer av scenarier när de arbetar via Internet, men på ett säkert sätt som respekterar användarens integritet. Du kan hämta ytterligare information i [data modellen för verifierbara autentiseringsuppgifter 1,0](https://www.w3.org/TR/vc-data-model/)

I korthet är verifierbara autentiseringsuppgifter data objekt som består av anspråk som utfärdats av utfärdaren och som attesterar information om ett ämne. Dessa anspråk identifieras av schemat och inkluderar utfärdaren och ämnet. Utfärdaren skapade en digital signatur som bevis på att de attesterar den här informationen.


## <a name="how-does-decentralized-identity-work"></a>Hur fungerar decentraliserad identitet? 

Vi behöver en ny typ av identitet. Vi behöver en identitet som samlar teknik och standarder för att leverera nyckel identiteter som till exempel själv ägande rätt och censorship resistens. Dessa funktioner är svåra att uppnå med befintliga system.

För att leverera på detta löfte behöver vi en teknisk grund som består av sju viktiga innovationer. En nyckel innovation är identifierare som ägs av användaren, en användar agent för att hantera nycklar som är kopplade till dessa identifierare och krypterade, användar kontrollerade data lager.

![Översikt över Microsofts miljö för verifierbara autentiseringsuppgifter](media/decentralized-identifier-overview/microsoft-did-system.png)

**1. W3C decentraliserade identifierare (DIDs)** ID: er som användare skapar, äger och styr oberoende av organisationer eller myndigheter. DIDs är globalt unika identifierare som är länkade till decentraliserade DPKI-metadata (Public Key Infrastructure) som består av JSON-dokument som innehåller material för offentliga nycklar, autentiserings beskrivningar och tjänst slut punkter.

**2. decentraliserat system: Jon (identitets överlägg nätverk)** Jon är ett öppet, icke-auktoriserat nätverk på nivå 2, vilket inte kräver särskilda tokens, betrodda verifierare eller andra enighets metoder. det linjära förloppet för Bitcoin tids kedjan är allt som krävs för att utföra åtgärden. Vi har [öppnat ett NPM-paket](https://www.npmjs.com/package/@decentralized-identity/ion-tools) för att kunna arbeta med Jon nätverket lätt att integrera i dina appar och tjänster. Biblioteken omfattar att skapa en ny genererad, generera nycklar och fästa på Bitcoin-blockchain. 

**3. DID User Agent/plån boks program: Microsoft Authenticator app** gör att verkliga personer kan använda decentraliserade identiteter och verifierbara autentiseringsuppgifter. Autentiseraren skapar DIDs, underlättar utfärdande och presentations begär Anden om verifierbara autentiseringsuppgifter och hanterar säkerhets kopieringen av ditt Dirigerings dirigering via en krypterad plån boks fil.

**4. Microsoft löser** ett API som ansluter till vår Jon-nod för att leta upp och lösa DIDs med hjälp av ```did:ion``` metoden och returnera objektet DDO (dokument objekt). DDO innehåller DPKI metadata som är associerade med, t. ex. offentliga nycklar och tjänst slut punkter. 

**5. Azure Active Directory verifierade autentiseringsuppgifter** i ett API för utfärdande och verifiering och SDK för öppen källkod för [W3C verifierbara autentiseringsuppgifter](https://www.w3.org/TR/vc-data-model/) som är signerade med- ```did:ion``` metoden. De ger identitets ägare möjlighet att generera, presentera och verifiera anspråk. Detta utgör grunden för förtroende mellan användare av systemen.

## <a name="a-sample-scenario"></a>Ett exempel scenario

Scenariot som vi använder för att förklara hur VCs arbetar innefattar:

- Sparbank Inc. ett företag.
- Proseware, ett företag som erbjuder Sparbankens rabatter.
- Alice, en anställd på Sparbanken, Inc. som vill få rabatt från proseware



I dag innehåller Alice ett användar namn och lösen ord för att logga in på Sparbankens nätverks miljö. Sparbanken distribuerar en VC-lösning för att ge ett mer hanterbart sätt för Alice att bevisa att hon är anställd på Sparbanken. Proseware använder en VC-lösning som är kompatibel med Sparbankens VC-lösning och accepterar autentiseringsuppgifter som utfärdats av Sparbanken som bevis på anställning.

Utfärdaren av autentiseringsuppgiften, Sparbank Inc., skapar en offentlig nyckel och en privat nyckel. Den offentliga nyckeln lagras på Jon sidan. När nyckeln läggs till i infrastrukturen registreras posten i en blockchain-baserad decentraliserad redovisning. Utfärdaren ger Alice den privata nyckel som lagras i ett plån boks program. Varje gång Alice använder den privata nyckeln har transaktionen loggats i plån boks programmet.

![Microsoft-DID-översikt](media/decentralized-identifier-overview/did-overview.png)

## <a name="roles-in-a-verifiable-credential-solution"></a>Roller i en verifierbar lösning för autentiseringsuppgifter 

Det finns tre primära aktörer i lösningen för verifierbara autentiseringsuppgifter. I följande diagram:

- **Steg 1**, begär **användaren** en verifierbar autentiseringsuppgift från en utfärdare.
- **Steg 2**, **utfärdaren** anger att beviset som användaren har angett är korrekt och skapar en verifierbar autentiseringsuppgift signerad med deras upphovs man och användarens ämne.
- **I steg 3** loggar användaren en verifierbar presentation (VD) med sina utfärdade och skickar dem till **verifier.** Verifieringen verifierar sedan autentiseringsuppgifterna genom att matcha den offentliga nyckeln som placerats i DPKI.

Rollerna i det här scenariot är:

![roller i en verifierbar miljö för verifierbara autentiseringsuppgifter](media/decentralized-identifier-overview/issuer-user-verifier.png)

**utfärdare** – utfärdaren är en organisation som skapar en utfärdande lösning som efterfrågar information från en användare. Informationen används för att verifiera användarens identitet. Till exempel är Sparbanken Inc. en utfärdande lösning som gör det möjligt för dem att skapa och distribuera verifierbara autentiseringsuppgifter (VCs) till alla deras anställda. Den anställda använder Authenticator-appen för att logga in med sitt användar namn och lösen ord, vilket skickar en ID-token till den utfärdande tjänsten. När Sparbanken är inställt verifierar den ID-token som har skickats. utfärdande lösningen skapar en VC som innehåller anspråk om medarbetaren och är signerad med Sparbanken Inc. Medarbetaren har nu en verifierbar autentiseringsuppgift som är signerad av sin arbetsgivare, vilket innefattar de anställda gjorde som ämne.  

**användare** – användaren är den person eller enhet som begär en VC. Till exempel är Alice en ny medarbetare av Sparbanken, Inc. som tidigare har beviljats bevis för verifierbara autentiseringsuppgifter. När Alice behöver tillhandahålla bevis på sysselsättningen för att få rabatt på Proseware, kan hon bevilja åtkomst till autentiseringsuppgiften i sin Authenticator-app genom att signera en verifierbar presentation som bevisar Alice är ägaren till den gjorde. Proseware kan verifiera att autentiseringsuppgiften utfärdats av Sparbanken, Inc. och Alice är ägare till autentiseringsuppgiften. 

**Verifier** – kontrollanten är ett företag eller en enhet som behöver verifiera anspråk från en eller flera utfärdare som de litar på. Till exempel är proseware Trusts Sparbanke, Inc. ett adekvat jobb för att verifiera deras anställdas identitet och utfärda autentiska och giltiga VCs. När Alice försöker beställa den utrustning hon behöver för sitt jobb, kommer proseware att använda öppna standarder som SIOP och presentations utbyte för att begära autentiseringsuppgifter från användaren som bevisar att de är anställda på Sparbanken, Inc. Proseware kan till exempel förse Alice med en länk till en webbplats med en QR-kod som hon skannar med sin telefon kamera. Detta initierar begäran om en speciell VC, vilken autentiserare kommer att analysera och ge Alice möjlighet att godkänna begäran om att bevisa sin anställning till proseware. Proseware kan använda API: t eller SDK för verifierbara autentiseringsuppgifter för att verifiera att den verifierbara presentationen är äkta. Utifrån den information som angetts av Alice ger de Karin rabatten. Om andra företag och organisationer vet att Sparbanken, Inc. utfärdar till sina anställda, kan de också skapa en Verifier-lösning och använda Sparbanken, Inc. verifierbara autentiseringsuppgifter för att tillhandahålla Special erbjudanden som är reserverade för Sparbanken, Inc. anställda.

## <a name="next-steps"></a>Nästa steg

Nu när du vet om DIDs och verifierbara autentiseringsuppgifter kan du prova själv genom att följa vår artikel om att komma igång eller någon av våra artiklar som ger mer information om verifierbara autentiseringsuppgifter.

- [Kom igång med verifierbara autentiseringsuppgifter](get-started-verifiable-credentials.md)
- [Så här anpassar du dina autentiseringsuppgifter](credential-design.md)
- [Vanliga frågor och svar om verifierbara autentiseringsuppgifter](verifiable-credentials-faq.md)