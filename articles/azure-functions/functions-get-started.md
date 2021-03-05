---
title: Komma igång med Azure Functions
description: Ta de första stegen mot att arbeta med Azure Functions.
author: craigshoemaker
ms.topic: overview
ms.date: 11/19/2020
ms.author: cshoe
zone_pivot_groups: programming-languages-set-functions-lang-workers
ms.openlocfilehash: 77d370b895c777278d3136c7d2c511e7f9e23b36
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102179263"
---
# <a name="getting-started-with-azure-functions"></a>Komma igång med Azure Functions

## <a name="introduction"></a>Introduktion

Med [Azure Functions](./functions-overview.md) kan du implementera systemets logik i lätt tillgängliga kodblock. Kodblock kallas för "Functions".

Använd följande resurser för att komma igång.

::: zone pivot="programming-language-csharp"

| Action | Resurser |
| --- | --- |
| **Skapa din första funktion** | Använd något av följande verktyg:<br><br><li>[Visual Studio](./functions-create-your-first-function-visual-studio.md)<li>[Visual Studio Code](./create-first-function-vs-code-csharp.md)<li>[Kommandorad](./create-first-function-cli-csharp.md) |
| **Se en funktion som kör** | <li>[Azure samples-webbläsare](/samples/browse/?expanded=azure&languages=csharp&products=azure-functions)<li>[Azure community-bibliotek](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=C%23) |
| **Utforska en interaktiv självstudie**| <li>[Välj den bästa serverlösa teknikerna i Azure för ditt företagsscenario](/learn/modules/serverless-fundamentals/)<li>[Väl utformat ramverk – prestanda effektivitet](/learn/modules/azure-well-architected-performance-efficiency/)<li>[Kör en Azure-funktion med utlösare](/learn/modules/execute-azure-function-with-triggers/) <br><br>Se Microsoft Learn för en [fullständig lista över interaktiva självstudier](/learn/browse/?expanded=azure&products=azure-functions).|
| **Granska metodtips** |<li>[Prestanda och tillförlitlighet](./functions-best-practices.md)<li>[Hantera anslutningar](./manage-connections.md)<li>[Fel hantering och funktions försök](./functions-bindings-error-pages.md?tabs=csharp)<li>[Säkerhet](./security-concepts.md)|
| **Läs mer i djupet** | <li>Lär dig hur funktioner [automatiskt ökar eller minskar](./functions-scale.md) instanser för att matcha efter frågan<li>Utforska de olika [distributions metoder](./functions-deployment-technologies.md) som är tillgängliga<li>Använd inbyggda [övervaknings verktyg](./functions-monitoring.md) för att analysera dina funktioner<li>Läs [språk referens för C#](./functions-dotnet-class-library.md)|

::: zone-end

::: zone pivot="programming-language-java"
| Action | Resurser |
| --- | --- |
| **Skapa din första funktion** | Använd något av följande verktyg:<br><br><li>[Visual Studio Code](./create-first-function-vs-code-java.md)<li>[Java/maven-funktion med Terminal/kommando-prompt](./create-first-function-cli-java.md)<li>[Gradle](./functions-create-first-java-gradle.md)<li>[Eclipse](./functions-create-maven-eclipse.md)<li>[IntelliJ IDEA](./functions-create-maven-intellij.md) |
| **Se en funktion som kör** | <li>[Azure samples-webbläsare](/samples/browse/?expanded=azure&languages=java&products=azure-functions)<li>[Azure community-bibliotek](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=Java) |
| **Utforska en interaktiv självstudie**| <li>[Välj den bästa serverlösa teknikerna i Azure för ditt företagsscenario](/learn/modules/serverless-fundamentals/)<li>[Väl utformat ramverk – prestanda effektivitet](/learn/modules/azure-well-architected-performance-efficiency/)<li>[Utveckla en app med hjälp av Maven-plugin-programmet för Azure Functions](/learn/modules/develop-azure-functions-app-with-maven-plugin/) <br><br>Se Microsoft Learn för en [fullständig lista över interaktiva självstudier](/learn/browse/?expanded=azure&products=azure-functions).|
| **Granska metodtips** |<li>[Prestanda och tillförlitlighet](./functions-best-practices.md)<li>[Hantera anslutningar](./manage-connections.md)<li>[Fel hantering och funktions försök](./functions-bindings-error-pages.md?tabs=java)<li>[Säkerhet](./security-concepts.md)|
| **Läs mer i djupet** | <li>Lär dig hur funktioner [automatiskt ökar eller minskar](./functions-scale.md) instanser för att matcha efter frågan<li>Utforska de olika [distributions metoder](./functions-deployment-technologies.md) som är tillgängliga<li>Använd inbyggda [övervaknings verktyg](./functions-monitoring.md) för att analysera dina funktioner<li>Läs [språk referens för Java](./functions-reference-java.md)|
::: zone-end

::: zone pivot="programming-language-javascript"
| Action | Resurser |
| --- | --- |
| **Skapa din första funktion** | Använd något av följande verktyg:<br><br><li>[Visual Studio Code](./create-first-function-vs-code-node.md)<li>[Node.js Terminal/kommando-prompt](./create-first-function-cli-node.md) |
| **Se en funktion som kör** | <li>[Azure samples-webbläsare](/samples/browse/?expanded=azure&languages=javascript%2ctypescript&products=azure-functions)<li>[Azure community-bibliotek](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=JavaScript%2CTypeScript) |
| **Utforska en interaktiv självstudie** | <li>[Välj den bästa serverlösa teknikerna i Azure för ditt företagsscenario](/learn/modules/serverless-fundamentals/)<li>[Väl utformat ramverk – prestanda effektivitet](/learn/modules/azure-well-architected-performance-efficiency/)<li>[Skapa serverlösa API:er med Azure Functions](/learn/modules/build-api-azure-functions/)<li>[Skapa serverfri logik med Azure Functions](/learn/modules/create-serverless-logic-with-azure-functions/)<li>[Omstrukturera Node.js Express-API:er till serverlösa API:er med Azure Functions](/learn/modules/shift-nodejs-express-apis-serverless/) <br><br>Se Microsoft Learn för en [fullständig lista över interaktiva självstudier](/learn/browse/?expanded=azure&products=azure-functions).|
| **Granska metodtips** |<li>[Prestanda och tillförlitlighet](./functions-best-practices.md)<li>[Hantera anslutningar](./manage-connections.md)<li>[Fel hantering och funktions försök](./functions-bindings-error-pages.md?tabs=javascript)<li>[Säkerhet](./security-concepts.md)|
| **Läs mer i djupet** | <li>Lär dig hur funktioner [automatiskt ökar eller minskar](./functions-scale.md) instanser för att matcha efter frågan<li>Utforska de olika [distributions metoder](./functions-deployment-technologies.md) som är tillgängliga<li>Använd inbyggda [övervaknings verktyg](./functions-monitoring.md) för att analysera dina funktioner<li>Läs språk referens för [Java Script](./functions-reference-node.md) eller [typescript](./functions-reference-node.md#typescript)|
::: zone-end

::: zone pivot="programming-language-powershell"
| Action | Resurser |
| --- | --- |
| **Skapa din första funktion** | <li>Använda [Visual Studio Code](./create-first-function-vs-code-powershell.md) |
| **Se en funktion som kör** | <li>[Azure samples-webbläsare](/samples/browse/?expanded=azure&languages=powershell&products=azure-functions)<li>[Azure community-bibliotek](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=PowerShell) |
| **Utforska en interaktiv självstudie** | <li>[Välj den bästa serverlösa teknikerna i Azure för ditt företagsscenario](/learn/modules/serverless-fundamentals/)<li>[Väl utformat ramverk – prestanda effektivitet](/learn/modules/azure-well-architected-performance-efficiency/)<li>[Skapa serverlösa API:er med Azure Functions](/learn/modules/build-api-azure-functions/)<li>[Skapa serverfri logik med Azure Functions](/learn/modules/create-serverless-logic-with-azure-functions/)<li>[Kör en Azure-funktion med utlösare](/learn/modules/execute-azure-function-with-triggers/) <br><br>Se Microsoft Learn för en [fullständig lista över interaktiva självstudier](/learn/browse/?expanded=azure&products=azure-functions).|
| **Granska metodtips** |<li>[Prestanda och tillförlitlighet](./functions-best-practices.md)<li>[Hantera anslutningar](./manage-connections.md)<li>[Fel hantering och funktions försök](./functions-bindings-error-pages.md?tabs=powershell)<li>[Säkerhet](./security-concepts.md)|
| **Läs mer i djupet** | <li>Lär dig hur funktioner [automatiskt ökar eller minskar](./functions-scale.md) instanser för att matcha efter frågan<li>Utforska de olika [distributions metoder](./functions-deployment-technologies.md) som är tillgängliga<li>Använd inbyggda [övervaknings verktyg](./functions-monitoring.md) för att analysera dina funktioner<li>Läs [PowerShell-språkreferensen](./functions-reference-powershell.md))|
::: zone-end

::: zone pivot="programming-language-python"
| Action | Resurser |
| --- | --- |
| **Skapa din första funktion** | Använd något av följande verktyg:<br><br><li>[Visual Studio Code](./create-first-function-vs-code-csharp.md?pivots=programming-language-python)<li>[Terminal/kommando-prompt](./create-first-function-cli-csharp.md?pivots=programming-language-python) |
| **Se en funktion som kör** | <li>[Azure samples-webbläsare](/samples/browse/?expanded=azure&languages=python&products=azure-functions)<li>[Azure community-bibliotek](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=Python) |
| **Utforska en interaktiv självstudie** | <li>[Välj den bästa serverlösa teknikerna i Azure för ditt företagsscenario](/learn/modules/serverless-fundamentals/)<li>[Väl utformat ramverk – prestanda effektivitet](/learn/modules/azure-well-architected-performance-efficiency/)<li>[Skapa serverlösa API:er med Azure Functions](/learn/modules/build-api-azure-functions/)<li>[Skapa serverfri logik med Azure Functions](/learn/modules/create-serverless-logic-with-azure-functions/) <br><br>Se Microsoft Learn för en [fullständig lista över interaktiva självstudier](/learn/browse/?expanded=azure&products=azure-functions).|
| **Granska metodtips** |<li>[Prestanda och tillförlitlighet](./functions-best-practices.md)<li>[Hantera anslutningar](./manage-connections.md)<li>[Fel hantering och funktions försök](./functions-bindings-error-pages.md?tabs=python)<li>[Säkerhet](./security-concepts.md)<li>[Förbättra data flödes prestanda](./python-scale-performance-reference.md)|
| **Läs mer i djupet** | <li>Lär dig hur funktioner [automatiskt ökar eller minskar](./functions-scale.md) instanser för att matcha efter frågan<li>Utforska de olika [distributions metoder](./functions-deployment-technologies.md) som är tillgängliga<li>Använd inbyggda [övervaknings verktyg](./functions-monitoring.md) för att analysera dina funktioner<li>Läs [språk referens för python](./functions-reference-python.md)|
::: zone-end

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig mer om att utveckla ett Azure Functions program](./functions-reference.md)
