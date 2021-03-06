---
title: Metodtips för att lägga till användare i en ansiktstjänst
titleSuffix: Azure Cognitive Services
description: Lär dig mer om processen för registrering av ansiktsigenkänning för att registrera användare i en tjänst för ansiktsigenkänning.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: overview
ms.date: 04/19/2021
ms.author: pafarley
ms.openlocfilehash: 183983779c09658d8d6f609319a127b1f406452d
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728436"
---
# <a name="best-practices-for-adding-users-to-a-face-service"></a>Metodtips för att lägga till användare i en ansiktstjänst

För att kunna använda ansikts Cognitive Services-API:et för ansiktsverifiering eller -identifiering måste du registrera ansikten i **en LargePersonGroup** eller en liknande datastruktur. Den här djupdykningen demonstrerar metodtips för att samla in meningsfullt medgivande från användare och exempellogik för att skapa registreringar av hög kvalitet som optimerar igenkänningsprecisionen. 

## <a name="meaningful-consent"></a>Meningsfullt medgivande 

Ett av de viktigaste syftena med ett registreringsprogram för ansiktsigenkänning är att ge användarna möjlighet att godkänna användningen av bilder av deras ansikte för specifika ändamål, till exempel åtkomst till en arbetsplats. Eftersom tekniker för ansiktsigenkänning kan uppfattas som insamling av känsliga personuppgifter är det särskilt viktigt att be om medgivande på ett sätt som är både transparent och respekterat. Medgivande är meningsfullt för användarna när det ger dem möjlighet att fatta det beslut som de anser är bäst för dem.   

Baserat på Microsofts användarforskning, Microsofts principer för ansvarsfull AI och extern forskning [har](ftp://ftp.cs.washington.edu/tr/2000/12/UW-CSE-00-12-02.pdf)vi upptäckt att medgivande är meningsfullt när det erbjuder följande till användare som registrerar sig inom tekniken:

* Medvetenhet: Användarna bör utan tvekan känna till när de uppmanas att tillhandahålla ansiktsmallen eller registreringsfoton. 
* Förståelse: Användare bör kunna beskriva vad de har blivit tillfrågade om med egna ord, av vem, i vilket syfte och med vilka garantier. 
* Valfrihet: Användarna ska inte känna sig skrivna eller manipulerade när de väljer att godkänna och registrera sig för ansiktsigenkänning. 
* Kontroll: Användarna bör när som helst kunna återkalla sitt medgivande och ta bort sina data. 

Det här avsnittet innehåller vägledning för att utveckla ett registreringsprogram för ansiktsigenkänning. Den här vägledningen har utvecklats baserat på Microsofts användarforskning när det gäller registrering av individer i ansiktsigenkänning för att skapa en post. Därför kanske dessa rekommendationer inte gäller för alla lösningar för ansiktsigenkänning. Ansvarig användning för Ansikts-API beror starkt på den specifika kontext där det är integrerat, så prioriteringen och tillämpningen av dessa rekommendationer bör anpassas till ditt scenario. 

> [!NOTE]
> Det är ditt ansvar att anpassa ditt registreringsprogram till tillämpliga juridiska krav i din jurisdiktion och korrekt återspegla alla dina datainsamlings- och bearbetningsmetoder.

## <a name="application-development"></a>Programutveckling 

Innan du utformar ett registreringsflöde bör du tänka på hur programmet du skapar kan upprätthålla de löften du ger användarna om hur deras data skyddas. Följande rekommendationer kan hjälpa dig att skapa en registreringsupplevelse som omfattar ansvarsfulla metoder för att skydda personliga data, hantera användarnas integritet och säkerställa att programmet är tillgängligt för alla användare.  

|Kategori | Rekommendationer |
|---|---|
|Maskinvara | Överväg registreringsenhetens kamerakvalitet. |
|Rekommenderade registreringsfunktioner | Inkludera ett inloggningssteg med multifaktorautentisering. </br></br>Länka användarinformation som ett alias eller ett id-nummer med deras ansiktsmalls-ID från ansikts-API:et (kallas person-ID). Den här mappningen är nödvändig för att hämta och hantera en användares registrering. Obs! Person-ID ska behandlas som en hemlighet i programmet.</br></br>Konfigurera en automatiserad process för att ta bort alla registreringsdata, inklusive ansiktsmallar och registreringsfoton för personer som inte längre använder ansiktsigenkänningsteknik, till exempel tidigare anställda. </br></br>Undvik automatisk registrering eftersom det inte ger användaren den medvetenhet, förståelse, valfrihet eller kontroll som rekommenderas för att få medgivande. </br></br>Be användarna om behörighet att spara de avbildningar som används för registrering. Detta är användbart när det finns en modelluppdatering eftersom nya registreringsfoton måste registreras på nytt i den nya modellen ungefär var 10:e månad. Om de ursprungliga avbildningarna inte sparas måste användarna gå igenom registreringsprocessen från början.</br></br>Tillåt att användare avanmäler sig från att lagra foton i systemet. För att göra valet tydligare kan du lägga till en andra skärm för begäran om medgivande för att spara registreringsfotona. </br></br>Om fotona sparas skapar du en automatiserad process för att omregistrera alla användare när det finns en modelluppdatering. Användare som sparade sina registreringsfoton behöver inte registrera sig själva igen. </br></br>Skapa en appfunktion som gör att utsedda administratörer kan åsidosätta vissa kvalitetsfilter om en användare har problem med att registrera sig. |
|Säkerhet | Cognitive Services [metodtips för](../cognitive-services-virtual-networks.md?tabs=portal) kryptering av användardata i vila och under överföring. Följande är andra metoder som kan hjälpa till att upprätthålla de säkerhetsrekommendationer som du ger användarna under registreringen. </br></br>Vidta säkerhetsåtgärder för att säkerställa att ingen har åtkomst till person-ID:t vid något tillfälle under registreringen. Obs! PersonID ska behandlas som en hemlighet i registreringssystemet. </br></br>Använd [rollbaserad åtkomstkontroll med](../../role-based-access-control/overview.md) Cognitive Services. </br></br>Använd tokenbaserad autentisering och/eller signaturer för delad åtkomst (SAS) över nycklar och hemligheter för att få åtkomst till resurser som databaser. Genom att använda begärande- eller SAS-token kan du bevilja begränsad åtkomst till data utan att kompromissa med dina kontonycklar, och du kan ange en förfallotid för token. </br></br>Lagra aldrig hemligheter, nycklar eller lösenord i din app. |
|Användarsekretess |Ange en rad registreringsalternativ för att hantera olika sekretessnivåer. Be inte att personer ska använda sina personliga enheter för att registrera sig i ett system för ansiktsigenkänning. </br></br>Tillåt att användarna registrerar om, återkallar medgivande och tar bort data från registreringsprogrammet när som helst och av valfri anledning. |
|Tillgänglighet |Följ hjälpmedelsstandarderna (till exempel [ADA](https://www.ada.gov/regs2010/2010ADAStandards/2010ADAstandards.htm) eller [W3C)](https://www.w3.org/TR/WCAG21/)för att säkerställa att programmet kan användas av personer med nedsatt rörlighet eller syn. |

## <a name="next-steps"></a>Nästa steg  

Följ guiden [Skapa en registreringsapp för](build-enrollment-app.md) att komma igång med en exempelregistreringsapp. Anpassa den sedan eller skriv din egen app så att den passar din produkts behov.