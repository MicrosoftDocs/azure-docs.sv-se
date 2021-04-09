---
title: Förstå funktions hantering med hjälp av Azure App konfiguration
description: Aktivera och inaktivera funktioner med Azure App konfiguration
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.custom: devx-track-dotnet
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: cfd676092bcaede58909a3ec1eefeabb4c80f86b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96930236"
---
# <a name="feature-management-overview"></a>Översikt över funktions hantering

För att leverera en ny program funktion krävs en fullständig omdistribution av själva programmet. Att testa en funktion kräver ofta flera distributioner av programmet.  Varje distribution kan ändra funktionen eller exponera funktionen för olika kunder för testning.  

Funktions hantering är en modern program utvecklings metod som frigör funktions lansering från kod distribution och gör det möjligt att snabbt ändra funktionsens tillgänglighet på begäran. Den använder en teknik som kallas *funktions flaggor* (kallas även för *funktions växlingar*, *funktions växlar* och så vidare) för att dynamiskt administrera en funktions livs cykel.

Med funktions hantering kan utvecklare lösa följande problem:

* **Kod förgrenings hantering**: Använd funktions flaggor för att figursätta nya program funktioner som för närvarande är under utveckling. Sådan funktionalitet är "dold" som standard. Du kan på ett säkert sätt leverera funktionen, även om den är oavslutad och den förblir vilande i produktionen. Med den här metoden, som kallas *mörk distribution*, kan du släppa all kod i slutet av varje utvecklings cykel. Du behöver inte längre ha kod grenar i flera utvecklings cykler eftersom en specifik funktion kräver mer än en cykel att slutföras.
* **Testa i produktion**: Använd funktions flaggor för att ge tidig åtkomst till nya funktioner i produktionen. Du kan till exempel begränsa åtkomsten till grupp medlemmar eller interna beta testare. Dessa användare kommer att uppleva hela produktions upplevelsen i stället för en simulerad eller delvis upplevelse i en test miljö.
* **Flygning**: Använd funktions flaggor för att stegvis distribuera nya funktioner till slutanvändarna. Du kan rikta in dig på en liten procent andel av din användar population först och öka procent andelen gradvis över tid.
* **Omedelbar Kill-växel**: funktions flaggor utgörs av ett säkerhets nät som gör det möjligt att lansera nya funktioner. Du kan aktivera och inaktivera program funktioner utan att behöva distribuera om någon kod. Om det behövs kan du snabbt inaktivera en funktion utan att behöva bygga om och omdistribuera ditt program.
* **Selektiv aktivering**: Använd funktions flaggor för att segmentera användarna och leverera en speciell uppsättning funktioner till varje grupp. Du kan ha en funktion som bara fungerar i en viss webbläsare. Du kan definiera en funktions flagga så att endast användare av webbläsaren kan se och använda funktionen. Med den här metoden kan du enkelt expandera listan över webbläsare som stöds senare utan att behöva göra några kod ändringar.

## <a name="basic-concepts"></a>Grundläggande begrepp

Här följer flera nya villkor som rör funktions hantering:

* **Funktions flagga**: en funktions flagga är en variabel med en binär status *på* eller *av*. Funktions flaggan har också ett associerat kodblock. Funktions flaggans tillstånd utlöser om kod blocket körs.
* **Funktions hanteraren**: en funktions hanterare är ett programpaket som hanterar livs cykeln för alla funktions flaggor i ett program. Funktions hanteraren innehåller också ytterligare funktioner, inklusive cachelagring av funktions flaggor och uppdatering av deras tillstånd.
* **Filter**: ett filter är en regel för att utvärdera status för en funktions flagga. Möjliga filter är användar grupper, enhets-eller webb läsar typer, geografiska platser och tidsfönster.

En effektiv implementering av funktions hantering består av minst två komponenter som arbetar i samförstånd:

* Ett program som använder funktions flaggor.
* En separat lagrings plats som lagrar funktions flaggorna och deras aktuella tillstånd.

## <a name="using-feature-flags-in-your-code"></a>Använda funktions flaggor i din kod

Det grundläggande mönstret för att implementera funktions flaggor i ett program är enkelt. En funktions flagga är en boolesk tillstånds variabel som styr en villkorlig instruktion i koden:

```csharp
if (featureFlag) {
    // Run the following code
}
```

Du kan ange värdet `featureFlag` statiskt.

```csharp
bool featureFlag = true;
```

Du kan utvärdera flaggans tillstånd baserat på vissa regler:

```csharp
bool featureFlag = isBetaUser();
```

Du kan utöka villkoret för att ange program beteende för endera status:

```csharp
if (featureFlag) {
    // This following code will run if the featureFlag value is true
} else {
    // This following code will run if the featureFlag value is false
}
```

## <a name="feature-flag-repository"></a>Databas för funktions flagga

Om du vill använda funktions flaggor effektivt måste du Externalize alla funktions flaggor som används i ett program. På så sätt kan du ändra funktions flagga tillstånd utan att ändra och omdistribuera själva programmet.

Azure App-konfigurationen tillhandahåller en central lagrings plats för funktions flaggor. Du kan använda den för att definiera olika typer av funktions flaggor och ändra deras tillstånd snabbt och säkert. Du kan sedan använda konfigurations biblioteken för appen för olika programmerings språk ramverk för att enkelt komma åt dessa funktions flaggor från ditt program.

[Använd funktions flaggor i en ASP.net Core app](./use-feature-flags-dotnet-core.md) som visar hur biblioteken för .net Core-appens konfiguration och funktions hanterings bibliotek används tillsammans för att implementera funktions flaggor för ASP.NET-webbprogrammet.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lägga till funktions flaggor i en ASP.NET Core-webbapp](./quickstart-feature-flag-aspnet-core.md)  
