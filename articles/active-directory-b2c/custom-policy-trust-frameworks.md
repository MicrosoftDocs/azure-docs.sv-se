---
title: Översikt över Azure AD B2C anpassad princip | Microsoft Docs
description: Ett avsnitt om Azure Active Directory B2C anpassade principer och ramverket för identitets upplevelse.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: cb33e11af26d5f5a2676f5b236ac142179bdb550
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99592848"
---
# <a name="azure-ad-b2c-custom-policy-overview"></a>Översikt över Azure AD B2C anpassad princip

Anpassade principer är konfigurationsfiler som definierar beteendet för din Azure Active Directory B2C-klientorganisation (Azure AD B2C). Medan [användar flöden](user-flow-overview.md) är fördefinierade i Azure AD B2C Portal för de vanligaste identitets uppgifterna kan anpassade principer redige ras fullständigt av en identitets utvecklare för att utföra många olika uppgifter.

En anpassad princip är helt konfigurerbar och princip driven. En anpassad princip dirigerar förtroende mellan entiteter i standard protokoll format, till exempel OpenID Connect, OAuth, SAML och några icke-standardvärden, t. ex. REST API-baserade system-till-system-anspråk. Ramverket skapar användarvänliga upplevelser med vita etiketter.

En anpassad princip visas som en eller flera XML-formaterade filer som refererar till varandra i en hierarkisk kedja. XML-elementen definierar Bygg blocken, interaktionen med användaren och andra parter och affärs logiken. 

## <a name="custom-policy-starter-pack"></a>Start paket för anpassad princip

Azure AD B2C anpassad princip [Start paket](custom-policy-get-started.md#get-the-starter-pack) innehåller flera fördefinierade principer för att komma igång snabbt. Vart och ett av dessa start paket innehåller det minsta antalet tekniska profiler och användar resor som krävs för att uppnå de scenarier som beskrivs:

- **LocalAccounts** – tillåter endast lokala konton.
- **SocialAccounts** – tillåter endast användning av sociala (eller federerade) konton.
- **SocialAndLocalAccounts** – möjliggör användning av både lokala och sociala konton. De flesta av våra exempel hänvisar till den här principen.
- **SocialAndLocalAccountsWithMFA** – aktiverar alternativ för sociala, lokala och Multi-Factor Authentication.

## <a name="understanding-the-basics"></a>Förstå grunderna 

### <a name="claims"></a>Anspråk

Ett anspråk ger tillfällig lagring av data under en Azure AD B2C princip körning. Den kan lagra information om användaren, till exempel förnamn, efter namn eller andra anspråk som erhållits från användaren eller andra system (anspråks utbyten). [Anspråks schema](claimsschema.md) är den plats där du deklarerar dina anspråk. 

När principen körs skickar Azure AD B2C och tar emot anspråk till och från interna och externa parter och skickar sedan en delmängd av dessa anspråk till ditt förlitande part program som en del av token. Anspråk används på följande sätt: 

- Ett anspråk sparas, läses eller uppdateras mot katalogens användar objekt.
- Ett anspråk tas emot från en extern identitets leverantör.
- Anspråk skickas eller tas emot med en anpassad REST API-tjänst.
- Data samlas in som anspråk från användaren under registreringen eller redigera profil flöden.

### <a name="manipulating-your-claims"></a>Manipulera dina anspråk

[Anspråks Transformationerna](claimstransformations.md) är fördefinierade funktioner som kan användas för att konvertera ett angivet anspråk till ett annat, utvärdera ett anspråk eller ange ett anspråks värde. Du kan till exempel lägga till ett objekt i en sträng samling, ändra Skift läget för en sträng eller utvärdera ett datum-och tids anspråk. En anspråks omvandling anger en Transformations metod. 

### <a name="customize-and-localize-your-ui"></a>Anpassa och lokalisera ditt användar gränssnitt

När du vill samla in information från användarna genom att presentera en sida i webbläsaren använder du den [självkontrollerade tekniska profilen](self-asserted-technical-profile.md). Du kan redigera din självkontrollerade tekniska profil för att [lägga till anspråk och anpassa användarindata](./configure-user-input.md).

Om du vill [Anpassa användar gränssnittet](customize-ui-with-html.md) för din självkontrollerade tekniska profil anger du en URL i [innehålls definitions](contentdefinitions.md) elementet med anpassat HTML-innehåll. I den självkontrollerade tekniska profilen pekar du på det här innehålls Definitions-ID: t.

Om du vill anpassa språkspecifika strängar använder du [lokaliserings](localization.md) elementet. En innehålls definition kan innehålla en [lokaliserings](localization.md) referens som anger en lista över lokaliserade resurser som ska läsas in. Azure AD B2C sammanfogar användargränssnittets element med HTML-innehåll som läses in från din URL och visar sedan sidan för användaren. 

## <a name="relying-party-policy-overview"></a>Översikt över princip för förlitande part

Ett förlitande parts program, som i SAML-protokollet kallas en tjänst leverantör, anropar den [förlitande part principen](relyingparty.md) för att köra en specifik användar resa. Principen för förlitande part anger vilken användar resa som ska köras och lista över anspråk som token innehåller. 

![Diagram som visar körnings flödet för principen](./media/custom-policy-trust-frameworks/custom-policy-execution.png)

Alla förlitande part program som använder samma princip får samma token-anspråk och användaren går igenom samma användar resa.

### <a name="user-journeys"></a>Användarresor

Med [användar resan](userjourneys.md) kan du definiera affärs logiken med den sökväg som användaren ska följa för att få åtkomst till ditt program. Användaren tas genom användar resan för att hämta de anspråk som ska presenteras för ditt program. En användar resa skapas utifrån en sekvens av [Orchestration-steg](userjourneys.md#orchestrationsteps). En användare måste komma åt det sista steget för att hämta en token. 

I följande anvisningar beskrivs hur du kan lägga till Orchestration-steg i principen för [social och lokal konto start paket](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccounts) . Här är ett exempel på ett REST API-anrop som har lagts till.

![anpassad användar resa](media/custom-policy-trust-frameworks/user-journey-flow.png)


### <a name="orchestration-steps"></a>Orchestration-steg

Orchestration-steget hänvisar till en metod som implementerar dess avsedda syfte eller funktioner. Den här metoden kallas för en [teknisk profil](technicalprofiles.md). När din användar resa behöver brancha för att bättre representera affärs logiken refererar Orchestration-steget till [under resan](subjourneys.md). En under resa innehåller en egen uppsättning Dirigerings steg.

En användare måste komma till det sista Orchestration-steget i användar resan för att få en token. Men användarna kanske inte behöver gå igenom alla stegen i dirigeringen. Orchestration-steg kan utföras villkorligt, baserat på de [villkor](userjourneys.md#preconditions) som definieras i Orchestration-steget. 

När ett Orchestration-steg har slutförts lagrar Azure AD B2C de utgångna anspråken i **anspråks säcken**. Anspråken i anspråks säcken kan användas av eventuella ytterligare Orchestration-steg i användar resan.

Följande diagram visar hur användar resans Dirigerings steg kan komma åt anspråks säcken.

![Azure AD B2C användar resa](media/custom-policy-trust-frameworks/user-journey-diagram.png)

### <a name="technical-profile"></a>Teknisk profil

En teknisk profil tillhandahåller ett gränssnitt för att kommunicera med olika typer av parter. En användar resa kombinerar anrop av tekniska profiler via Orchestration-steg för att definiera din affärs logik.

Alla typer av tekniska profiler delar samma koncept. Du kan skicka indatamängds anspråk, köra anspråk och kommunicera med den konfigurerade parten. När processen har slutförts returnerar den tekniska profilen utgående anspråk till anspråks säck. Mer information finns i [Översikt över tekniska profiler](technicalprofiles.md).

### <a name="validation-technical-profile"></a>Verifiering av teknisk profil

När en användare interagerar med användar gränssnittet kanske du vill validera de data som samlas in. För att interagera med användaren, måste en [egen kontrollerad teknisk profil](self-asserted-technical-profile.md) användas.

För att verifiera användarindata anropas en [verifierings teknisk profil](validation-technical-profile.md) från den självkontrollerade tekniska profilen. En teknisk verifierings profil är en metod för att anropa alla icke-interaktiva tekniska profiler. I det här fallet kan den tekniska profilen returnera utgående anspråk eller ett fel meddelande. Fel meddelandet återges för användaren på skärmen, så att användaren kan försöka igen.

Följande diagram illustrerar hur Azure AD B2C använder en teknisk validerings profil för att verifiera användarautentiseringsuppgifter.

![Diagram över verifiering av teknisk profil](media/custom-policy-trust-frameworks/validation-technical-profile.png)

## <a name="inheritance-model"></a>Arvs modell

Varje start paket innehåller följande filer:

- En **bas** fil som innehåller de flesta av definitionerna. Försök att minimera antalet ändringar som du gör i den här filen om du vill ha hjälp med att felsöka och långsiktigt underhåll av dina principer.
- En **tilläggs** fil som innehåller de unika konfigurations ändringarna för din klient. Den här princip filen är härledd från bas filen. Använd den här filen för att lägga till nya funktioner eller åsidosätta befintliga funktioner. Använd till exempel den här filen för att federera med nya identitets leverantörer.
- En **förlitande parts fil (RP)** som är den enda aktivitets fokuserade filen som anropas direkt av den förlitande parten, t. ex. dina webb-, mobil-eller Skriv bords program. Varje unik aktivitet, till exempel registrering, inloggning, återställning av lösen ord eller profil redigering, kräver en egen princip fil för förlitande part. Den här princip filen är härledd från tilläggs filen.

Arvs modellen ser ut så här:

- Den underordnade principen på alla nivåer kan ärva från den överordnade principen och utöka den genom att lägga till nya element.
- För mer komplexa scenarier kan du lägga till fler arvs nivåer (upp till 10 totalt).
- Du kan lägga till fler principer för förlitande part. Ta till exempel bort mitt konto, ändra ett telefonnummer, en princip för SAML-förlitande part och mer.

I följande diagram visas relationen mellan principfiler och de förlitande part programmen.

![Diagram som visar förtroende ramverkets princip arvs modell](media/custom-policy-trust-frameworks/policies.png)


## <a name="guidance-and-best-practices"></a>Vägledning och metodtips

### <a name="best-practices"></a>Bästa praxis

I en Azure AD B2C anpassad princip kan du integrera din egen affärs logik för att bygga de användar upplevelser du behöver och utöka funktionerna i tjänsten. Vi har en uppsättning av bästa praxis och rekommendationer för att komma igång.

- Skapa din logik i **tilläggs principen** eller principen för **förlitande part**. Du kan lägga till nya element som åsidosätter bas principen genom att referera till samma ID. På så sätt kan du skala ut projektet samtidigt som du gör det lättare att uppgradera grund principen senare om Microsoft släpper nya start paket.
- I **bas principen** rekommenderar vi starkt att du gör ändringar. Vid behov kan du göra kommentarer där ändringarna görs.
- När du åsidosätter ett element, till exempel tekniska metadata för teknisk profil, bör du undvika att kopiera hela den tekniska profilen från bas principen. Kopiera i stället bara det obligatoriska avsnittet i-elementet. Se [inaktivera e-postverifiering](./disable-email-verification.md) för ett exempel på hur du gör ändringen.
- Om du vill minska dubbleringen av tekniska profiler, där kärn funktioner delas, använder du [teknisk profil inkludering](technicalprofiles.md#include-technical-profile).
- Undvik att skriva till Azure AD-katalogen under inloggningen, vilket kan leda till problem med begränsningen.
- Om principen har externa beroenden, t. ex. REST-API: er, ser du till att de är hög tillgängliga.
- För en bättre användar upplevelse ser du till att dina anpassade HTML-mallar är globalt distribuerade med [innehålls leverans online](../cdn/index.yml). Med Azure Content Delivery Network (CDN) kan du minska inläsnings tider, spara bandbredd och förbättra svars hastigheten.
- Om du vill göra en ändring i användar resan kopierar du hela användar resan från bas principen till tilläggs principen. Ange ett unikt användar resa-ID för användar resan som du har kopierat. I principen för [förlitande part](relyingparty.md)ändrar du sedan [standard användar transport](relyingparty.md#defaultuserjourney) elementet så att det pekar på den nya användar resan.

## <a name="troubleshooting"></a>Felsökning

När du utvecklar med Azure AD B2C-principer kan du stöta på fel eller undantag när du kör användar resan. Kan undersökas med hjälp av Application Insights.

- Integrera Application Insights med Azure AD B2C för att [diagnostisera undantag](troubleshoot-with-application-insights.md).
- [Azure AD B2C-tillägget för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c) kan hjälpa dig att komma åt och [visualisera loggarna](https://github.com/azure-ad-b2c/vscode-extension/blob/master/src/help/app-insights.md) baserat på ett princip namn och en tidpunkt.
- Det vanligaste felet vid inställning av anpassade principer är felaktigt formaterat XML. Använd [XML-schema verifiering](troubleshoot-custom-policies.md) för att identifiera fel innan du laddar upp XML-filen.

## <a name="continuous-integration"></a>Kontinuerlig integrering

Genom att använda en pipeline för kontinuerlig integrering och leverans (CI/CD) som du ställer in i Azure-pipeline kan du [inkludera dina Azure AD B2C anpassade principer i din program varu leverans](deploy-custom-policies-devops.md) och kod kontroll automatisering. När du distribuerar till olika Azure AD B2C miljöer, t. ex. utveckling, testning och produktion, rekommenderar vi att du tar bort manuella processer och utför automatiserad testning med hjälp av Azure-pipeliner.

## <a name="prepare-your-environment"></a>Förbered din miljö

Du kommer igång med Azure AD B2C anpassad princip:

1. [Skapa en Azure AD B2C-klient](tutorial-create-tenant.md)
1. [Registrera ett webb program](tutorial-register-applications.md) med hjälp av Azure Portal så att du kan testa principen.
1. Lägg till nödvändiga [princip nycklar](custom-policy-get-started.md#add-signing-and-encryption-keys) och [Registrera program för identitets upplevelse Framework](custom-policy-get-started.md#register-identity-experience-framework-applications).
1. [Hämta start paketet för Azure AD B2C policy](custom-policy-get-started.md#get-the-starter-pack) och ladda upp till din klient. 
1. När du har laddat upp start paketet [testar du din registrerings-eller inloggnings princip](custom-policy-get-started.md#test-the-custom-policy).
1. Vi rekommenderar att du hämtar och installerar [Visual Studio Code](https://code.visualstudio.com/) (vs Code). Visual Studio Code är en lätt men kraftfull käll kods redigerare som körs på Skriv bordet och är tillgänglig för Windows, macOS och Linux. Med VS Code kan du snabbt navigera i och redigera dina Azure AD B2C anpassade princip-XML-filer genom att installera [Azure AD B2C-tillägget för vs Code](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c)
 
## <a name="next-steps"></a>Nästa steg

När du har konfigurerat och testat din Azure AD B2C-princip kan du börja anpassa principen. Gå igenom följande artiklar om du vill lära dig att:

- [Lägg till anspråk och anpassa användarindata](./configure-user-input.md) med anpassade principer. Lär dig hur du definierar ett anspråk och lägger till ett anspråk i användar gränssnittet genom att anpassa några av de tekniska profilerna för start paket.
- Anpassa ditt programs [användar gränssnitt](customize-ui-with-html.md) med hjälp av en anpassad princip. Lär dig hur du skapar ett eget HTML-innehåll och anpassar innehålls definitionen.
- [Lokalisera användar gränssnittet](./language-customization.md) för ditt program med hjälp av en anpassad princip. Lär dig hur du konfigurerar en lista över språk som stöds och tillhandahåller språkspecifika etiketter genom att lägga till det lokaliserade resurs elementet.
- Under utvecklingen och testningen av principer kan du [inaktivera e-postverifiering](./disable-email-verification.md). Lär dig hur du skriver över en teknisk profils metadata.
- [Konfigurera inloggning med ett Google-konto](./identity-provider-google.md) med anpassade principer. Lär dig hur du skapar en ny anspråks leverantör med OAuth2 Technical Profile. Anpassa sedan användar resan till att inkludera alternativet Google-inloggning.
- För att diagnosticera problem med dina anpassade principer kan du [samla in Azure Active Directory B2C loggar med Application Insights](troubleshoot-with-application-insights.md). Lär dig hur du lägger till nya tekniska profiler och konfigurerar principen för förlitande part.
