---
title: Metod tips för Azure App konfiguration | Microsoft Docs
description: Lär dig metod tips när du använder Azure App-konfiguration. Ämnen som omfattas är nyckel grupperingar, nyckel värdes kompositioner, start för app-konfiguration med mera.
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: lcozzens
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: d532b8aab87840f4b6ad90daedba743597f4fe43
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88588066"
---
# <a name="azure-app-configuration-best-practices"></a>Metod tips för Azure App konfiguration

I den här artikeln beskrivs vanliga mönster och bästa metoder när du använder Azure App konfiguration.

## <a name="key-groupings"></a>Nyckel grupper

Konfiguration av appar innehåller två alternativ för att ordna nycklar:

* Viktiga prefix
* Etiketter

Du kan använda antingen ett eller båda alternativen för att gruppera dina nycklar.

*Viktiga prefix* är start delarna av nycklar. Du kan logiskt gruppera en uppsättning nycklar genom att använda samma prefix i deras namn. Prefix kan innehålla flera komponenter som är anslutna med en avgränsare, till exempel `/` liknande en URL-sökväg, för att bilda ett namn område. Sådana hierarkier är användbara när du lagrar nycklar för många program, komponent tjänster och miljöer i ett konfigurations lager för appar.

Ett viktigt att tänka på är att nycklarna är vad din program kod refererar till för att hämta värdena för motsvarande inställningar. Nycklar bör inte ändras eller så måste du ändra koden varje stund.

*Etiketter* är ett attribut för nycklar. De används för att skapa varianter av en nyckel. Du kan till exempel tilldela etiketter till flera versioner av en nyckel. En version kan vara en iteration, en miljö eller någon annan sammanhangsbaserad information. Ditt program kan begära en helt annan uppsättning nyckel värden genom att ange en annan etikett. Därför förblir alla viktiga referenser oförändrade i koden.

## <a name="key-value-compositions"></a>Nyckel värdes kompositioner

App Configuration behandlar alla nycklar som lagras med den som oberoende entiteter. App-konfigurationen försöker inte härleda någon relation mellan nycklar eller ärva nyckel värden baserat på deras hierarki. Du kan använda flera uppsättningar med nycklar, men med hjälp av etiketter tillsammans med korrekt konfigurations stack i program koden.

Vi tittar på ett exempel. Anta att du har en inställning med namnet **Asset1**, vars värde kan variera beroende på utvecklings miljön. Du skapar en nyckel med namnet "Asset1" med en tom etikett och en etikett med namnet "Development". I den första etiketten sätter du standardvärdet för **Asset1**och du anger ett särskilt värde för "utveckling" i den senare.

I din kod hämtar du först nyckel värden utan några etiketter, och sedan hämtar du samma uppsättning nyckel värden en andra gång med etiketten "utveckling". När du hämtar värdena den andra gången skrivs de tidigare värdena i nycklarna över. Med konfigurations systemet för .NET Core kan du "stacka" flera uppsättningar konfigurations data ovanpå varandra. Om det finns en nyckel i fler än en uppsättning används den sista uppsättningen som innehåller den. Med ett modernt programmerings ramverk, till exempel .NET Core, får du den här stack funktionen kostnads fritt om du använder en inbyggd Konfigurationsprovider för att komma åt appens konfiguration. Följande kodfragment visar hur du kan implementera stackning i ett .NET Core-program:

```csharp
// Augment the ConfigurationBuilder with Azure App Configuration
// Pull the connection string from an environment variable
configBuilder.AddAzureAppConfiguration(options => {
    options.Connect(configuration["connection_string"])
           .Select(KeyFilter.Any, LabelFilter.Null)
           .Select(KeyFilter.Any, "Development");
});
```

[Använd etiketter för att aktivera olika konfigurationer för olika miljöer](./howto-labels-aspnet-core.md) , vilket är ett fullständigt exempel.

## <a name="app-configuration-bootstrap"></a>Start av app-konfiguration

Om du vill komma åt ett konfigurations lager för appar kan du använda dess anslutnings sträng, som finns i Azure Portal. Eftersom anslutnings strängar innehåller autentiseringsinformation anses de vara hemligheter. Dessa hemligheter måste lagras i Azure Key Vault och din kod måste autentiseras för att Key Vault ska kunna hämta dem.

Ett bättre alternativ är att använda funktionen hanterade identiteter i Azure Active Directory. Med hanterade identiteter behöver du bara slut punkts-URL: en för programmets konfiguration för att starta åtkomst till appens konfigurations lager. Du kan bädda in webb adressen i din program kod (till exempel i *appsettings.jspå* fil). Mer information finns i [integrera med Azure Managed Identities](howto-integrate-azure-managed-service-identity.md) .

## <a name="app-or-function-access-to-app-configuration"></a>App-eller funktions åtkomst till app-konfiguration

Du kan ge åtkomst till app-konfiguration för webbappar eller funktioner med någon av följande metoder:

* Ange anslutnings strängen till appens konfigurations Arkiv i program inställningarna för App Service Azure Portal.
* Lagra anslutnings strängen i appens konfigurations Arkiv i Key Vault och [referera den från App Service](https://docs.microsoft.com/azure/app-service/app-service-key-vault-references).
* Använd Azure Managed Identities för att få åtkomst till appens konfigurations arkiv. Mer information finns i [integrera med Azure Managed Identities](howto-integrate-azure-managed-service-identity.md).
* Push-konfiguration från App Configuration till App Service. App-konfigurationen tillhandahåller en export funktion (i Azure Portal och Azure CLI) som skickar data direkt till App Service. Med den här metoden behöver du inte ändra program koden alls.

## <a name="reduce-requests-made-to-app-configuration"></a>Minska förfrågningar till app-konfiguration

För många begär anden till app-konfigurationen kan leda till begränsning eller överbelastnings kostnader. Så här minskar du antalet begär Anden som har gjorts:

* Öka tids gränsen för uppdatering, särskilt om dina konfigurations värden inte ändras ofta. Ange en ny tids gräns för uppdatering med hjälp av [ `SetCacheExpiration` metoden](/dotnet/api/microsoft.extensions.configuration.azureappconfiguration.azureappconfigurationrefreshoptions.setcacheexpiration).

* Titta på en enda *kontroll nyckel*i stället för att titta på enskilda nycklar. Uppdatera all konfiguration endast om kontroll nyckeln ändras. Se [Använd dynamisk konfiguration i en ASP.net Core-app](enable-dynamic-configuration-aspnet-core.md) för ett exempel.

* Använd Azure Event Grid för att ta emot meddelanden när konfigurations ändringar görs, i stället för att hela tiden söka efter ändringar. Mer information finns i [dirigera Azure App konfigurations händelser till en webb slut punkt](./howto-app-configuration-event.md)

## <a name="importing-configuration-data-into-app-configuration"></a>Importera konfigurations data till konfigurations program

Med konfiguration av appar kan du välja Mass [import](https://aka.ms/azconfig-importexport1) av konfigurations inställningar från dina aktuella konfigurationsfiler med hjälp av antingen Azure Portal eller cli. Du kan också använda samma alternativ för att exportera värden från App-konfigurationen, till exempel mellan relaterade butiker. Om du vill konfigurera en pågående synkronisering med din GitHub-lagrings platsen kan du använda vår [GitHub-åtgärd](https://aka.ms/azconfig-gha2) så att du kan fortsätta att använda dina befintliga käll kontroll metoder samtidigt som du får fördelarna med app-konfigurationen.

## <a name="multi-region-deployment-in-app-configuration"></a>Distribution i flera regioner i appens konfiguration

Konfiguration av appar är regional tjänst. För program med olika konfigurationer per region kan du skapa en enskild felpunkt genom att lagra dessa konfigurationer i en instans. Att distribuera en instans av konfigurations instanser per region i flera regioner kan vara ett bättre alternativ. Det kan hjälpa till med regional haveri beredskap, prestanda och säkerhets silo. Att konfigurera efter region förbättrar också svars tiden och använder separerade begränsnings kvoter, eftersom begränsningen är per instans. Om du vill tillämpa minskning av haveri beredskap kan du använda [flera konfigurations lager](./concept-disaster-recovery.md). 

## <a name="next-steps"></a>Nästa steg

* [Nycklar och värden](./concept-key-value.md)
