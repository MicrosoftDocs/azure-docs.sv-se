---
title: Översikt över Azure API Management-relaterade viktiga begrepp | Microsoft Docs
description: Läs mer om API:er, produkter, roller, grupper och andra viktiga API Management-relaterade begrepp.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: overview
ms.date: 11/15/2017
ms.author: apimpm
ms.custom: mvc
ms.openlocfilehash: 85fa79cdfc7036be5b0ab20e49986a1d075152c5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "86254664"
---
# <a name="about-api-management"></a>Om API Management

API Management (APIM) är ett snabbt sätt att skapa enhetliga och moderna API-gatewayer för befintliga serverdelstjänster.

API Management hjälper organisationer att publicera API: er till externa, partner och interna utvecklare för att låsa upp potentiella data och tjänster. Företag överallt vill utöka sin närvaro som digital plattform, skapa nya kanaler, hitta nya kunder och fördjupa relationerna med befintliga kunder. API Management lägger grunden till ett effektivt API-program genom engagerade utvecklare, affärsinsikter, analyser, hög säkerhet och skydd. Med Azure API Management kan du välja valfri serverdel och köra ett fullvärdigt API-program baserat på den.

Den här artikeln innehåller en översikt över vanliga scenarier som innefattar APIM.  Det ger också en kort översikt över APIM-systemets huvudkomponenter. Artikeln ger sedan en detaljerad översikt över varje komponent.

## <a name="overview"></a>Översikt

Administratörer skapar API:er för att använda API Management. Varje API består av en eller flera åtgärder och varje API kan läggas till i en eller flera produkter. För att kunna använda ett API prenumererar utvecklare på en produkt som innehåller API:et och kan sedan anropa API:ets åtgärder, baserat på eventuella användningsprinciper. Här följer exempel på några vanliga scenarier:

* **Skydda den mobila infrastrukturen** genom att hantera åtkomsten med API-nycklar, förhindra DOS-attacker med hjälp av begränsningar och använda avancerade säkerhetsprinciper som JWT-tokenvalidering.
* **Aktivera ISV-partnerekosystem** genom att erbjuda snabb partnerintegrering via utvecklarportalen och genom att bygga en API-fasad som är åtskild från interna implementeringar som inte är redo för partneranvändning än.
* **Kör ett internt API-program** genom att erbjuda en central plats för organisationen där man kan kommunicera kring tillgänglighet och de senaste ändringarna i API:er samt hantera åtkomsten baserat på organisationskonton – allt via en säker kanal mellan API-gatewayen och serverdelen.

Systemet består av följande komponenter:

* **API-gatewayen** är den slut punkt som:
  
  * Accepterar API-anrop och dirigerar dem till serverdelen.
  * Verifierar API-nycklar, JWT-token, certifikat och andra autentiseringsuppgifter.
  * Framtvingar användningskvoter och hastighetsbegränsningar.
  * Transformerar ditt API i farten utan kodändringar.
  * Cachelagrar serverdelssvar där de konfigureras.
  * Loggar metadata i anrop för analysändamål.
* **Azure Portal** är det administrativa gränssnittet där du konfigurerar ditt API-program. Använd portalen om du vill:
  
  * Definiera eller importera API-schemat.
  * Paketera API:er till produkter.
  * Konfigurera principer som kvoter eller transformationer i API:erna.
  * Få insikter från analyser.
  * Hantera användare.
* **Developer-portalen** fungerar som den huvudsakliga webb förekomsten för utvecklare, där de kan:
  
  * Läsa API-dokumentation.
  * Testa ett API via den interaktiva konsolen.
  * Skapa ett konto och prenumerera för att få API-nycklar.
  * Komma åt analyser om deras egen användning.

Mer information finns i PDF-dokumentet [Cloud-based API Management: Harnessing the Power of APIs](https://j.mp/ms-apim-whitepaper). Det här introduktionsdokumentet om API Management av CITO Research innehåller bland annat följande avsnitt: 
 
 * Vanliga API-relaterade krav och utmaningar
 * Frikoppla API:er och presentera fasader
 * Få snabbt igång utvecklarna
 * Skydda åtkomst
 * Analyser och mått
 * Få kontroll och insyn med en API Management-plattform
 * Använda molnet eller lokala lösningar
 * Azure API Management
 
## <a name="apis-and-operations"></a><a name="apis"> </a>API:er och åtgärder
API:er är grunden för en API Management-tjänstinstans. Varje API representerar en uppsättning åtgärder som är tillgängliga för utvecklare. Varje API innehåller en referens till backend-tjänsten som implementerar API:et, och dess åtgärder mappar till åtgärderna som implementeras av backend-tjänsten. Åtgärder i API Management är ytterst konfigurerbara, med kontroll över URL-mappning, fråge- och sökvägsparametrar, förfrågnings- och svarsinnehåll och cachelagring av åtgärdssvar. Principer för frekvensgränser, kvoter och IP-begränsning kan också implementeras på API- eller åtgärdsnivå.

Mer information finns i [Skapa API:er][How to create APIs] och [Lägga till åtgärder till ett API][How to add operations to an API].

## <a name="products"></a><a name="products"></a> Produkter
Produkter syftar på hur API:erna exponeras för utvecklare. Produkter i API Management har ett eller flera API:er och konfigureras med en rubrik, en beskrivning och användningsvillkor. Produkter kan vara **öppna** eller **skyddade**. Utvecklare måste prenumerera på skyddade produkter innan de kan användas. Öppna produkter kan användas utan en prenumeration. När en produkt är redo att användas av utvecklare kan den publiceras. När produkten har publicerats kan utvecklare se den (och prenumerera på den om det är en skyddad produkt). Prenumerationsgodkännande konfigureras på produktnivå och kan antingen kräva administratörsgodkännande eller godkännas automatiskt.

Grupper används för att hantera hur produkterna visas för utvecklare. Produkter beviljar synlighet till grupper, och utvecklare kan visa och prenumerera på de produkter som är synliga för grupper som de är medlemmar i. 

## <a name="groups"></a><a name="groups"></a> Grupper
Grupper används för att hantera hur produkterna visas för utvecklare. API Management har följande systemgrupper som inte kan ändras:

* **Administratörer** – Administratörer av Azure-prenumerationer är medlemmar i den här gruppen. Administratörer hanterar API Management-tjänstinstanser genom att skapa API:er, åtgärder och produkter som används av utvecklare.
* **Utvecklare** – Autentiserade användare av utvecklarportalen hör till den här gruppen. Utvecklare är de kunder som utvecklar program med hjälp av dina API:er. Utvecklare beviljas åtkomst till utvecklarportalen och bygger program som anropar åtgärderna i ett API.
* **Gäster** – Oautentiserade användare av utvecklarportalen. Potentiella kunder som besöker utvecklarportalen för en API Management-instans hör t.ex. till den här gruppen. De kan beviljas viss skrivskyddad åtkomst, t.ex. möjligheten att visa API:er men inte anropa dem.

Utöver dessa systemgrupper kan administratörer skapa anpassade grupper eller [använda externa grupper i tillhörande Azure Active Directory-klienter](api-management-howto-aad.md). Anpassade och externa grupper kan användas tillsammans med systemgrupper för att välja vilka utvecklare som kan se och komma åt API-produkter. Du kan till exempel skapa en anpassad grupp för utvecklare som hör till en specifik partnerorganisation och ge dem åtkomst till API:erna från en produkt som endast innehåller relevanta API:er. En användare kan tillhöra mer än en grupp.

Mer information finns i [Skapa och använda grupper][How to create and use groups].

## <a name="developers"></a><a name="developers"></a> Utvecklare
Utvecklare representerar användarkontona i en API Management-tjänstinstans. Utvecklare kan skapas eller bjudas in av administratörer eller registrera sig på [utvecklarportalen][Developer portal]. Varje utvecklare är medlem i en eller flera grupper och kan prenumerera på de produkter som visas för dessa grupper.

När utvecklare prenumererar på en produkt får de tillgång till den primära och sekundära nyckeln för produkten. Den här nyckeln används för att göra anrop till produktens API:er.

Mer information finns i [Skapa eller bjuda in utvecklare][How to create or invite developers] och [Associera grupper med utvecklare][How to associate groups with developers].

## <a name="policies"></a><a name="policies"></a> Principer
Principer är en kraftfull funktion i API Management som gör att Azure-portalen kan konfigurera om API:ets beteende. Principer är en samling instruktioner som körs sekventiellt på begäran av eller efter ett svar från ett API. Exempel på populära instruktioner är formatkonvertering från XML till JSON och begränsning av anropsfrekvensen för att begränsa antalet inkommande anrop från en utvecklare. Många andra principer är också tillgängliga.

Principuttryck kan användas som attributvärden eller textvärden i API Management-principer, under förutsättning att principen tillåter det. Vissa principer som [Kontrollflöde](./api-management-advanced-policies.md#choose) och [Ange variabel](./api-management-advanced-policies.md#set-variable) baseras på principuttryck. Mer information finns i [Avancerade principer](./api-management-advanced-policies.md#AdvancedPolicies) och [Principuttryck](./api-management-policy-expressions.md).


En fullständig lista över API Management-principer finns i [Principreferens][Policy reference]. Mer information om hur du använder och konfigurerar principer finns i [API Management-principer][API Management policies]. En självstudiekurs om hur du skapar en produkt med principer för frekvensbegränsning och kvoter finns i [Skapa och konfigurera avancerade produktinställningar][How create and configure advanced product settings].


## <a name="developer-portal"></a><a name="developer-portal"></a> Utvecklings Portal
På utvecklarportalen kan utvecklare lära sig mer om dina API:er, visa och anropa åtgärder och prenumerera på produkter. Potentiella kunder kan besöka utvecklarportalen, visa API:er och åtgärder och registrera sig. URL:en för din utvecklarportal finns på instrumentpanelen i Azure Portal för din API Management-tjänstinstans.

Du kan anpassa utvecklingsportalen genom att lägga till eget innehåll, anpassa format eller lägga till varumärkesanpassad design.

## <a name="api-management-and-the-api-economy"></a>Ekonomin bakom API Management och API:er

Om du vill veta mer om API Management kan du titta på följande presentation från Microsoft Ignite 2017-konferensen.

> [!VIDEO https://channel9.msdn.com/Events/Ignite/Microsoft-Ignite-Orlando-2017/BRK2186/player]
> 
> 

## <a name="next-steps"></a>Nästa steg

Slutför följande snabbstart och börja använda Azure API Management:

> [!div class="nextstepaction"]
> [Skapa en Azure API Management-instans](get-started-create-service-instance.md)

[APIs and operations]: #apis
[Products]: #products
[Groups]: #groups
[Developers]: #developers
[Policies]: #policies
[Developer portal]: #developer-portal

[How to create APIs]: ./import-and-publish.md
[How to add operations to an API]: ./mock-api-responses.md
[How to create and publish a product]: api-management-howto-add-products.md
[How to create and use groups]: api-management-howto-create-groups.md
[How to associate groups with developers]: api-management-howto-create-groups.md#associate-group-developer
[How create and configure advanced product settings]: transform-api.md
[How to create or invite developers]: api-management-howto-create-or-invite-developers.md
[Policy reference]: ./api-management-policies.md
[API Management policies]: api-management-howto-policies.md
[Create an API Management service instance]: get-started-create-service-instance.md
