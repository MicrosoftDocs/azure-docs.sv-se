---
title: Metod tips för säker utveckling på Microsoft Azure
description: Metod tips som hjälper dig att utveckla säkrare kod och distribuera ett säkrare program i molnet.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/11/2019
ms.topic: article
ms.service: security
ms.subservice: security-develop
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: baaa311620f5c10948aa3494002ce359cc5dab28
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94517180"
---
# <a name="secure-development-best-practices-on-azure"></a>Säkra bästa metoder för utveckling i Azure
I den här artikel serien presenteras säkerhets aktiviteter och kontroller som du bör tänka på när du utvecklar program för molnet. Faserna i Microsoft Security Development Lifecycle (SDL) och säkerhets frågor och koncept som du bör tänka på under varje fas i livs cykeln omfattas. Målet är att hjälpa dig att definiera aktiviteter och Azure-tjänster som du kan använda i varje fas i livs cykeln för att utforma, utveckla och distribuera ett säkrare program.

Rekommendationerna i artiklarna kommer från vår erfarenhet av Azure-säkerhet och från våra kunders upplevelser. Du kan använda de här artiklarna som en referens för vad du bör tänka på under en bestämd fas i utvecklings projektet, men vi rekommenderar att du läser igenom alla artiklar från början till slut minst en gång. Genom att läsa alla artiklar får du en introduktion till koncept som du kanske har missat i tidigare faser av projektet. Genom att implementera dessa begrepp innan du släpper din produkt kan du hjälpa dig att bygga säker program vara, tillgodose kraven för efterlevnad och minska utvecklings kostnaderna.

Dessa artiklar är avsedda att vara en resurs för programmerare, utvecklare och testare på alla nivåer som skapar och distribuerar säkra Azure-program.

## <a name="overview"></a>Översikt

Säkerhet är en av de viktigaste aspekterna av alla program och det är inte en enkel sak att komma åt dig. Lyckligt vis tillhandahåller Azure många tjänster som kan hjälpa dig att skydda ditt program i molnet. De här artiklarna löser aktiviteter och Azure-tjänster som du kan implementera i varje steg i livs cykeln för program utveckling för att hjälpa dig att utveckla säkrare kod och distribuera ett säkrare program i molnet.

## <a name="security-development-lifecycle"></a>Säkerhetsutvecklingslivscykel (SDL)

Följande metod tips för säker program varu utveckling kräver integrering av säkerheten i varje fas i program utvecklings livs cykeln, från krav analys till underhåll, oavsett projekt[metodik (](https://en.wikipedia.org/wiki/Waterfall_model)vattenfall, [Agile](https://en.wikipedia.org/wiki/Agile_software_development)eller [DevOps](https://en.wikipedia.org/wiki/DevOps)). Vid aktivering av data överträdelser med hög profil och användning av drifts säkerhets fel är fler utvecklare medveten om att säkerheten måste åtgärdas under hela utvecklings processen.

Senare kan du åtgärda ett problem i utvecklings livs cykeln, desto mer som korrigeringen kostar dig. Säkerhets frågor är inget undantag. Om du åsidosätter säkerhets problem i tidiga faser av program varu utvecklingen kan varje fas som följer ärva säkerhets riskerna i föregående fas. Den slutliga produkten kommer att ha samlat flera säkerhets problem och risken för intrång. Att skapa säkerhet i varje fas i utvecklings livs cykeln hjälper dig att fånga upp problem tidigt och det hjälper dig att minska dina utvecklings kostnader.

Vi följer faserna i Microsoft [Security Development Lifecycle (SDL)](/previous-versions/windows/desktop/cc307891(v=msdn.10)) för att presentera aktiviteter och Azure-tjänster som du kan använda för att uppfylla säkra program utvecklings metoder i varje fas av livs cykeln.

SDL-faserna är:

  - Utbildning
  - Krav
  - Design
  - Implementering
  - Verifiering
  - Frisläpp
  - Svarsåtgärder

![Säkerhetsutvecklingens livscykel](./media/secure-dev-overview/01-sdl-phase.png)

I de här artiklarna grupperar vi SDL-faserna till design, utveckling och distribution.

## <a name="engage-your-organizations-security-team"></a>Engagera organisationens säkerhets team

Din organisation kan ha ett formellt program säkerhets program som hjälper dig med säkerhets aktiviteter från början till slut under utvecklings livs cykeln. Om din organisation har säkerhets-och efterlevnadsprinciper måste du engagera dem innan du börjar utveckla ditt program. Fråga dem i varje fas i SDL om det finns några uppgifter som du har missat.

Vi förstår att många läsare kanske inte har någon säkerhets-eller Compliance team-grupp att delta. De här artiklarna kan hjälpa dig med säkerhets frågor och beslut som du behöver tänka på i varje fas av SDL.

## <a name="resources"></a>Resurser

Använd följande resurser för att lära dig mer om att utveckla säkra program och skydda dina program på Azure:

[Microsoft Security Development Lifecycle (SDL)](/previous-versions/windows/desktop/cc307891(v=msdn.10)) – sdl är en program utvecklings process från Microsoft som hjälper utvecklare att bygga säkrare program vara. Det hjälper dig att hantera krav för säkerhetskompatibilitet samtidigt som utvecklings kostnaderna sänks.

[Öppna webb program säkerhets projekt (OWASP)](https://www.owasp.org/index.php/Main_Page) – OWASP är en online-community som ger kostnads fritt tillgängliga artiklar, metoder, dokumentation, verktyg och tekniker i webb program säkerheten.

Genom att lägga till [vänster, t. ex. en chef](https://code.likeagirl.io/pushing-left-like-a-boss-part-1-80f1f007da95?WT.mc_id=docs-blog-tajanca) – en serie med online-artiklar som beskriver olika typer av program säkerhets aktiviteter som utvecklare bör utföra för att skapa säkrare kod.

[Microsoft Identity Platform](../../active-directory/develop/index.yml) – Microsoft Identity Platform är en utveckling av Azure AD Identity service och Developer Platform. Det är en komplett plattform som består av en autentiseringstjänst, bibliotek med öppen källkod, program registrering och konfiguration, fullständig dokumentation om utvecklare, kod exempel och annat innehåll i utvecklare. Microsoft Identity Platform stöder bransch standard protokoll som OAuth 2,0 och OpenID Connect.

[Rekommenderade säkerhets metoder för Azure-lösningar](https://azure.microsoft.com/resources/security-best-practices-for-azure-solutions/) – en samling säkerhets metoder som du kan använda när du utformar, distribuerar och hanterar moln lösningar med hjälp av Azure. Det här dokumentet är avsett att vara en resurs för IT-proffs. Detta kan vara designers, arkitekter, utvecklare och testare som skapar och distribuerar säkra Azure-lösningar.

[Ritningar för säkerhet och efterlevnad i Azure](https://servicetrust.microsoft.com/ViewPage/BlueprintOverview) – Azures säkerhets-och efterlevnads ritningar är resurser som kan hjälpa dig att bygga och starta molnbaserade program som uppfyller strängare bestämmelser och standarder.

## <a name="next-steps"></a>Nästa steg
I följande artiklar rekommenderar vi säkerhets kontroller och aktiviteter som kan hjälpa dig att utforma, utveckla och distribuera säkra program.

- [Utforma säkra program](secure-design.md)
- [Utveckla säkra program](secure-develop.md)
- [Distribuera säkra program](secure-deploy.md)