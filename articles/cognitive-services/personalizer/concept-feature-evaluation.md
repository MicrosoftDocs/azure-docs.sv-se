---
title: Funktions utvärdering – Personanpassare
titleSuffix: Azure Cognitive Services
description: När du kör en utvärdering i din personanpassa resurs från Azure Portal, ger Personanpassaren information om vilka funktioner i kontexten och åtgärder som påverkar modellen.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 07/29/2019
ms.openlocfilehash: c0e47a2943cf8c934d201f76aefc41868adf0b25
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87127731"
---
# <a name="feature-evaluation"></a>Funktionsutvärdering

När du kör en utvärdering i din personanpassa resurs från [Azure Portal](https://portal.azure.com), ger personanpassaren information om vilka funktioner i kontexten och åtgärder som påverkar modellen. 

Det här är användbart för att:

* Föreställ dig ytterligare funktioner som du kan använda och få inspiration från vilka funktioner som är viktiga i modellen.
* Se vilka funktioner som inte är viktiga och eventuellt ta bort dem eller analysera vad som kan påverka användningen.
* Ge vägledning till redaktionella eller undervisande team om nytt innehåll eller produkter som är värda att sätta i katalogen.
* Felsök vanliga problem och misstag som inträffar när du skickar funktioner till en Personligre.

De viktigaste funktionerna har starkare vikter i modellen. Eftersom de här funktionerna har starkare vikt brukar de vara närvarande när en Personanpassare erhåller högre förmåner.

## <a name="getting-feature-importance-evaluation"></a>Få utvärdering av funktions prioritet

Om du vill visa funktions resultat måste du köra en utvärdering. Utvärderingen skapar välläsbara funktions etiketter baserat på de funktions namn som observerats under utvärderings perioden.

Den resulterande informationen om funktions prioritet representerar den aktuella personanpassa online-modellen. I utvärderingen analyseras funktions vikten för modellen som sparades vid slutdatumet för utvärderings perioden, efter att du har genomgått all utbildning som gjorts under utvärderingen, med den aktuella utbildnings principen online. 

Resultatet av funktions prioriteten representerar inte andra principer och modeller som testas eller skapas under utvärderingen.  Utvärderingen innehåller inte funktioner som skickats till en egen användare efter utvärderings periodens slut.

## <a name="how-to-interpret-the-feature-importance-evaluation"></a>Tolka utvärderingen av funktions prioriteten

Personanpassaren utvärderar funktioner genom att skapa "grupper" av funktioner som har liknande prioritet. En grupp kan anses ha en övergripande starkare prioritet än andra, men i gruppen är det alfabetiskt att ordna funktionerna.

Information om varje funktion inkluderar:

* Om funktionen kommer från kontext eller åtgärder.
* Funktions nyckel och värde.

Till exempel kan en app för fabriks en Ice-beställning se "context. Väder: het" som en mycket viktig funktion.

I personanpassaren visas korrelationer av funktioner som, när de beaktas tillsammans, ger högre förmåner.

Du kan till exempel se "context. Väder: hett *med* action. MenuItem: icecream" och "context. Väder: kall *med* action. MenuItem: WarmTea:

## <a name="actions-you-can-take-based-on-feature-evaluation"></a>Åtgärder som du kan vidta utifrån funktions utvärdering

### <a name="imagine-additional-features-you-could-use"></a>Föreställ dig ytterligare funktioner som du kan använda

Få inspiration från viktigare funktioner i modellen. Om du till exempel ser "context. MobileBattery: low" i en mobilapp kan du tänka på att anslutnings typen även kan göra att kunderna väljer att visa ett videoklipp över ett annat och sedan lägga till funktioner för Anslutnings typ och bandbredd i din app.

### <a name="see-what-features-are-not-important"></a>Se vilka funktioner som inte är viktiga

Potentiellt ta bort viktiga funktioner eller analysera vad som kan påverka användningen. Funktionerna kan rangordnas lågt av många skäl. Det kan bero på att funktionen inte påverkar användar beteendet. Men det kan också betyda att funktionen inte är uppenbar för användaren. 

Till exempel kan en video webbplats se att "action. VideoResolution = 4K" är en funktion med låg prioritet, vilket strider mot användar forskningen. Orsaken kan vara att programmet inte ens nämner eller visar video upplösningen, så att användarna inte ändrar beteendet baserat på den.

### <a name="provide-guidance-to-editorial-or-curation-teams"></a>Ge vägledning för redaktionella eller undervisande team

Ge vägledning om nytt innehåll eller produkter som är värda att sätta i katalogen. Personanpassaren är utformad för att vara ett verktyg som förstärker mänsklig insikt och team. Ett sätt att göra detta är att ge information till redaktionella grupper om vad som är det om produkter, artiklar eller innehåll som är i funktion. Scenariot för video program kan till exempel visa att det finns en viktig funktion som kallas "action. VideoEntities. Cat: true", vilket gör att redaktionella teamet kan ta med mer Cat-videor.

### <a name="troubleshoot-common-problems-and-mistakes"></a>Felsök vanliga problem och misstag

Vanliga problem och misstag kan åtgärdas genom att ändra program koden så att den inte skickar olämpliga eller felaktigt formaterade funktioner till Personanpassaren. 

Vanliga misstag när du skickar funktioner är följande:

* Skicka personligt identifierbar information (PII). Personligt identifierbar information för en enskild person (t. ex. namn, telefonnummer, kreditkorts nummer, IP-adresser) bör inte användas med Personanpassare. Om ditt program behöver spåra användare använder du ett UUID som inte identifierar identifiering eller något annat UserID-nummer. I de flesta fall är detta också problematiskt.
* Med ett stort antal användare är det osannolikt att varje användares interaktion kommer att väga mer än populationens interaktion, så att användar-ID: n (även om icke-PII) ofta lägger till mer brus än värdet i modellen.
* Skicka datum-tid-fält som exakta tidsstämplar i stället för bearbetas tids värden. Ha funktioner som context. TimeStamp. Day = måndag eller "context. TimeStamp. Hour" = "13" är mer användbart. Det kommer att finnas högst 7 eller 24 funktions värden för var och en. Men "context. TimeStamp": "1985-04-12T23:20:50.52 Z" är så exakt att det inte går att lära sig från det eftersom det aldrig kommer att ske igen.

## <a name="next-steps"></a>Nästa steg

Förstå [skalbarhet och prestanda](concepts-scalability-performance.md) med personanpassare.

