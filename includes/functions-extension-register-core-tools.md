---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 08/07/2020
ms.author: glenga
ms.openlocfilehash: a4f03223b5067d18f5d6e747b3bb630a86031b8f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96020383"
---
Om du inte kan använda paket för tillägg kan du använda Azure Functions Core Tools lokalt för att installera de speciella tilläggs paket som krävs för ditt projekt.

> [!IMPORTANT]
> Du kan inte uttryckligen installera tillägg i en Function-app som använder tilläggs paket. Ta bort `extensionBundle` avsnittet i *host.jspå* innan du installerar tillägg explicit.

Följande objekt beskriver några orsaker till att du kan behöva installera tillägg manuellt:

* Du måste ha åtkomst till en speciell version av ett tillägg som inte är tillgängligt i ett paket.
* Du måste ha åtkomst till ett anpassat tillägg som inte är tillgängligt i ett paket.
* Du måste ha åtkomst till en viss kombination av tillägg som inte är tillgängliga i ett enda paket.

> [!NOTE]
> Om du vill installera tillägg manuellt med hjälp av kärn verktyg måste du ha installerat [.net Core 2. x SDK](https://dotnet.microsoft.com/download) . .NET Core SDK används av Azure Functions Core Tools för att installera tillägg från NuGet. Du behöver inte känna till .NET för att använda Azure Functions-tillägg.

När du uttryckligen installerar tillägg läggs en .NET-projekt fil med namnet Extensions. CSPROJ till i projektets rot. Den här filen definierar den uppsättning NuGet-paket som krävs av dina funktioner. Även om du kan arbeta med [NuGet-paketets referenser](/nuget/consume-packages/package-references-in-project-files) i den här filen kan du installera tillägg utan att behöva redigera filen manuellt.

Det finns flera sätt att använda kärn verktyg för att installera de nödvändiga tilläggen i det lokala projektet. 

#### <a name="install-all-extensions"></a>Installera alla tillägg 

Använd följande kommando för att automatiskt lägga till alla tilläggs paket som används av bindningarna i det lokala projektet:

```dotnetcli
func extensions install
```
Kommandot läser *function.jspå* fil för att se vilka paket du behöver, installerar dem och återkonstruerar tilläggs projektet (Extensions. CSPROJ). Den lägger till eventuella nya bindningar i den aktuella versionen men uppdaterar inte befintliga bindningar. Använd `--force` alternativet för att uppdatera befintliga bindningar till den senaste versionen när du installerar nya.

Om din Function-app använder bindningar som grund verktygen inte känner igen måste du installera det angivna tillägget manuellt.

#### <a name="install-a-specific-extension"></a>Installera ett angivet tillägg

Använd följande kommando för att installera ett särskilt tilläggs paket i en speciell version, i det här fallet lagrings tillägget:

```dotnetcli
func extensions install --package Microsoft.Azure.WebJobs.Extensions.Storage --version 4.0.2
```
