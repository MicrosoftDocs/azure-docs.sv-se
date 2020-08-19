---
title: Vad är Azure App Configuration?
description: Läs en översikt över Azure App konfigurations tjänsten. Ta reda på varför du vill använda app Configuration och lär dig hur du kan använda den.
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: overview
ms.date: 02/19/2020
ms.openlocfilehash: f38b8de37e84cd8a65c5a5caadf8d3b6b71ce455
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88587080"
---
# <a name="what-is-azure-app-configuration"></a>Vad är Azure App Configuration?

Azure App-konfigurationen tillhandahåller en tjänst för att centralt hantera program inställningar och funktions flaggor. Moderna program, särskilt program som körs i ett moln, har vanligt vis många komponenter som är fördelade i natur. Om konfigurationsinställningar sprids mellan dessa komponenter kan det ge upphov till fel som är svåra att felsöka under en programdistribution. Använd app-konfiguration för att lagra alla inställningar för ditt program och skydda deras åtkomst på en och samma plats.

## <a name="why-use-app-configuration"></a>Varför ska jag använda app-konfiguration?

Molnbaserade program körs ofta på flera virtuella datorer eller containrar i flera regioner och använder flera externa tjänster. Att skapa ett robust och skalbart program i en distribuerad miljö medför en betydande utmaning.

Olika programmerings metoder hjälper utvecklare att hantera den ökande komplexiteten med att skapa program. Till exempel beskriver den [12-Factor appen](https://12factor.net/) många välbeprövade arkitektur mönster och metod tips för användning med moln program. En viktig rekommendation från den här guiden är att avgränsa konfigurationen från koden. Konfigurations inställningarna för ett program bör lagras utanför den körbara filen och läsas in från körnings miljön eller en extern källa.

Även om ett program kan använda appens konfiguration, är följande exempel de typer av program som drar nytta av att använda det:

* Mikrotjänster som baseras på Azure Kubernetes service, Azure Service Fabric eller andra behållare som distribuerats i en eller flera geografiska områden
* Appar utan server, som innehåller Azure Functions eller andra händelse drivna tillstånds lösa Compute-appar
* Pipeline för kontinuerlig distribution

App Configuration ger följande fördelar:

* En fullständigt hanterad tjänst som kan konfigureras på några minuter
* Flexibla nyckel representationer och mappningar
* Tagga med etiketter
* Omuppspelning av inställningarna för tidpunkt
* Dedikerat gränssnitt för hantering av funktions flagga
* Jämförelse av två uppsättningar konfigurationer i anpassade dimensioner
* Förbättrad säkerhet via Azure-hanterade identiteter
* Kryptering av känslig information i vila och under överföring
* Inbyggd integrering med populära ramverk

Konfiguration av appar kompletterar [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), som används för att lagra program hemligheter. Med app-konfigurationen blir det enklare att implementera följande scenarier:

* Centralisera hantering och distribution av hierarkiska konfigurations data för olika miljöer och geografiska områden
* Ändra program inställningarna dynamiskt utan att behöva distribuera om eller starta om ett program
* Kontroll funktionens tillgänglighet i real tid

## <a name="use-app-configuration"></a>Använd app-konfiguration

Det enklaste sättet att lägga till ett program konfigurations lager i ditt program är via ett klient bibliotek från Microsoft. Följande metoder är tillgängliga för att ansluta till ditt program, beroende på vilket språk och ramverk du valt

| Programmeringsspråk och ramverk | Så här ansluter du |
|---|---|
| .NET Core och ASP.NET Core | App Configuration Provider för .NET Core |
| .NET Framework och ASP.NET | Konfigurations verktyg för app för .NET |
| Java Spring | App Configuration-klient för våren Cloud |
| Andra | App-konfiguration REST API |

## <a name="next-steps"></a>Nästa steg

* [ASP.NET Core snabb start](./quickstart-aspnet-core-app.md)
* [Snabb start för .NET Core](./quickstart-dotnet-core-app.md)
* [.NET Framework snabb start](./quickstart-dotnet-app.md)
* [Azure Functions snabb start](./quickstart-azure-functions-csharp.md)
* [Snabb start för Java våren](./quickstart-java-spring-app.md)
* [Snabb start för ASP.NET Core funktions flagga](./quickstart-feature-flag-aspnet-core.md)
* [Start funktions flagga snabb start](./quickstart-feature-flag-spring-boot.md)
