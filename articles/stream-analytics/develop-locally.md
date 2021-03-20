---
title: Utveckla och felsöka Azure Stream Analytics jobb lokalt
description: Lär dig hur du utvecklar och testar Azure Stream Analytics jobb på den lokala datorn innan du kör dem i Azure Portal.
ms.author: sujie
author: su-jie
ms.topic: conceptual
ms.date: 03/31/2020
ms.service: stream-analytics
ms.openlocfilehash: 18df480dab90d9ab127bb96971fc19cdc5a361ce
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98016481"
---
# <a name="develop-and-debug-azure-stream-analytics-jobs-locally"></a>Utveckla och felsöka Azure Stream Analytics jobb lokalt

När du har möjlighet att skapa och testa Azure Stream Analytics jobb i Azure Portal, föredrar många utvecklare en lokal utvecklings miljö. Stream Analytics gör det enkelt att använda dina favorit kod redigerare och utvecklingsverktyg för att skapa och testa jobb med Live Events-strömmar från Azure Event Hub, IoT Hub och Blob Storage med en helt fungerande lokal körning av en nod. Du kan också skicka jobb till Azure direkt från din lokala utvecklings miljö.

## <a name="local-development-environments"></a>Lokala utvecklings miljöer

Hur du utvecklar Stream Analytics jobb på din lokala dator beror på dina verktygs inställningar och funktions tillgänglighet. Se [Azure Stream Analytics funktions jämförelse](feature-comparison.md) för att se vilka funktioner som stöds för varje utvecklings miljö.

Miljöerna i följande tabell stöder lokal utveckling:

|Miljö                              |Beskrivning    |
|-----------------------------------------|------------|
|[Visual Studio Code](visual-studio-code-explore-jobs.md)| Med [tillägget Azure Stream Analytics-verktyg](https://marketplace.visualstudio.com/items?itemName=ms-bigdatatools.vscode-asa) för Visual Studio Code kan du skapa, hantera, testa Stream Analytics-jobbet både lokalt och i molnet med omfattande IntelliSense och ursprunglig käll kontroll. Stöd för utveckling på Linux, MacOS och Windows. Mer information finns i [skapa ett Azure Stream Analytics jobb i Visual Studio Code](quick-create-visual-studio-code.md). Tillägget har även stöd för [Visual Studio-Codespaces](https://visualstudio.microsoft.com/services/visual-studio-codespaces/) som är en utvecklings miljö med en moln värd.|
|[Visual Studio 2019](stream-analytics-tools-for-visual-studio-install.md) |Stream Analytics verktyg är en del av Azure-utveckling och data lagring och bearbetning av arbets belastningar i Visual Studio. Du kan använda Visual Studio för att skriva anpassade C#-användardefinierade funktioner och deserialiserare. Mer information finns i [skapa ett Azure Stream Analytics jobb med hjälp av Visual Studio](stream-analytics-quick-create-vs.md).|
|[Kommando tolk eller Terminal](stream-analytics-tools-for-visual-studio-cicd.md)|Azure Stream Analytics CI/CD NuGet-paketet innehåller verktyg för Visual Studio Project build, lokal testning på en godtycklig dator. Azure Stream Analytics CI/CD NPM-paketet innehåller verktyg för Visual Studio Code Project-versioner (som genererar en Azure Resource Manager mall) på en godtycklig dator.|

## <a name="next-steps"></a>Nästa steg

* [Testa Stream Analytics frågor lokalt med exempel data med Visual Studio Code](visual-studio-code-local-run.md)
* [Testa Stream Analytics frågor lokalt mot Real tids indata med hjälp av Visual Studio Code](visual-studio-code-local-run-live-input.md)
* [Testa Stream Analytics frågor lokalt med Visual Studio](stream-analytics-vs-tools-local-run.md)
* [Testa Live data lokalt med Azure Stream Analytics verktyg för Visual Studio](stream-analytics-live-data-local-testing.md)
