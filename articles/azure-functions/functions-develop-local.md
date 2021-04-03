---
title: Utveckla och kör Azure Functions lokalt
description: Lär dig att koda och testa Azure Functions på den lokala datorn innan du kör dem på Azure Functions.
ms.topic: conceptual
ms.date: 09/04/2018
ms.openlocfilehash: 9c37d51abcc8d612b777b845515cf07666369d4f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96168142"
---
# <a name="code-and-test-azure-functions-locally"></a>Koda och testa Azure Functions lokalt

När du har möjlighet att utveckla och testa Azure Functions i [Azure Portal], föredrar många utvecklare en lokal utvecklings miljö. Funktionerna gör det enkelt att använda dina favorit kod redigerare och utvecklingsverktyg för att skapa och testa funktioner på den lokala datorn. Dina lokala funktioner kan ansluta till Live Azure-tjänster och du kan felsöka dem på din lokala dator med hjälp av körnings funktionen för fullständiga funktioner.

## <a name="local-development-environments"></a>Lokala utvecklings miljöer

Hur du utvecklar funktioner på din lokala dator beror på [språk](supported-languages.md) och verktygs inställningar. Miljöerna i följande tabell stöder lokal utveckling:

|Miljö                              |Språk         |Beskrivning|
|-----------------------------------------|------------|---|
|[Visual Studio Code](functions-develop-vs-code.md)| [C# (klass bibliotek)](functions-dotnet-class-library.md), [c#-skript (. CSX)](functions-reference-csharp.md), [Java Script](functions-reference-node.md), [PowerShell](./create-first-function-vs-code-powershell.md), [python](functions-reference-python.md) | [Azure Functions-tillägget för vs Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) lägger till funktioner som stöder vs Code. Kräver kärn verktyg. Stöder utveckling av Linux, MacOS och Windows när du använder version 2. x av kärn verktygen. Mer information finns i [skapa din första funktion med Visual Studio Code](./create-first-function-vs-code-csharp.md). |
| [Kommando tolk eller Terminal](functions-run-local.md) | [C# (klass bibliotek)](functions-dotnet-class-library.md), [c#-skript (. CSX)](functions-reference-csharp.md), [Java Script](functions-reference-node.md), [PowerShell](functions-reference-powershell.md), [python](functions-reference-python.md) | [Azure Functions Core tools] tillhandahåller kärn körning och mallar för att skapa funktioner som möjliggör lokal utveckling. Version 2. x stöder utveckling på Linux, MacOS och Windows. Alla miljöer förlitar sig på kärn verktyg för den lokala Functions-körningen. |
| [Visual Studio 2019](functions-develop-vs.md) | [C# (klassbibliotek)](functions-dotnet-class-library.md) | Azure Functions verktyg ingår i **Azure Development** -arbetsbelastningen för [Visual Studio 2019](https://www.visualstudio.com/vs/) och senare versioner. Gör att du kan kompilera funktioner i ett klass bibliotek och publicera. dll-filen på Azure. Innehåller kärn verktygen för lokal testning. Läs mer i [utveckla Azure Functions med Visual Studio](functions-develop-vs.md). |
| [Maven](./create-first-function-cli-java.md) (olika) | [Java](functions-reference-java.md) | Integrerar med Core-verktyg för att möjliggöra utveckling av Java-funktioner. Version 2. x stöder utveckling på Linux, MacOS och Windows. Mer information finns i [skapa din första funktion med Java och maven](./create-first-function-cli-java.md). Har även stöd för utveckling med [Sol förmörkelse](functions-create-maven-eclipse.md) och [IntelliJ idé](functions-create-maven-intellij.md) |

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

Var och en av de här lokala utvecklings miljöerna gör att du kan skapa Function app-projekt och använda fördefinierade funktions mallar för att skapa nya funktioner. Varje använder kärn verktygen så att du kan testa och felsöka dina funktioner mot Real tids körningen på din egen dator precis som med andra appar. Du kan också publicera ditt Function app-projekt från någon av dessa miljöer till Azure.

## <a name="next-steps"></a>Nästa steg

+ Läs mer om lokal utveckling av kompilerade C#-funktioner med Visual Studio 2019 i [utveckla Azure Functions med Visual Studio](functions-develop-vs.md).
+ Mer information om lokal utveckling av funktioner med VS Code på en Mac-, Linux-eller Windows-dator finns i [distribuera Azure Functions från vs Code](/azure/developer/javascript/tutorial-vscode-serverless-node-01).
+ Mer information om hur du utvecklar funktioner från kommando tolken eller terminalen finns i [arbeta med Azure Functions Core tools](functions-run-local.md).

<!-- LINKS -->

[Azure Functions Core Tools]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure-portalen]: https://portal.azure.com
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows