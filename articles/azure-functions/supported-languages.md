---
title: Språk som stöds i Azure Functions
description: Lär dig vilka språk som stöds (GA) och som finns i förhands granskningen och hur du kan utöka funktions utvecklingen till andra språk.
ms.topic: conceptual
ms.date: 11/27/2019
ms.openlocfilehash: da3100521faae61e9803aaa82dbf91045a7ecbc9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98108559"
---
# <a name="supported-languages-in-azure-functions"></a>Språk som stöds i Azure Functions

I den här artikeln beskrivs de support nivåer som erbjuds för språk som du kan använda med Azure Functions. Den beskriver också strategier för att skapa funktioner med språk som inte stöds internt.

## <a name="levels-of-support"></a>Support nivåer

Det finns två support nivåer:

* **Allmänt tillgänglig (ga)** – fullständigt stöd och godkänd för produktions användning.
* För **hands version** – stöds ännu inte, men förväntas komma att uppnå GA-status i framtiden.

## <a name="languages-by-runtime-version"></a>Språk efter körnings version 

[Det finns tre versioner av Azure Functions runtime](functions-versions.md) . I följande tabell visas vilka språk som stöds i varje körnings version.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

## <a name="custom-handlers"></a>Anpassade hanterare

Anpassade hanterare är lätta webb servrar som tar emot händelser från den Azure Functions värden. Alla språk som stöder HTTP-primitiver kan implementera en anpassad hanterare. Det innebär att anpassade hanterare kan användas för att skapa funktioner i språk som inte stöds officiellt. Läs mer i [Azure Functions anpassade hanterare](functions-custom-handlers.md).

## <a name="language-extensibility"></a>Språk utökning

Från och med version 2. x är körnings miljön utformad för att erbjuda [språk utöknings barhet](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Language-Extensibility). Java Script-och Java-språken i 2. x-körningsmiljön har skapats med den här utökningen.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du utvecklar funktioner i de språk som stöds finns i följande resurser:

+ [C#-referens för klass bibliotek i C#](functions-dotnet-class-library.md)
+ [C#-referens för skript utvecklare](functions-reference-csharp.md)
+ [Referens för Java-utvecklare](functions-reference-java.md)
+ [JavaScript-referens för utvecklare](functions-reference-node.md)
+ [Referens för PowerShell-utvecklare](functions-reference-powershell.md)
+ [Python-referens för utvecklare](functions-reference-python.md)
+ [TypeScript Developer-referens](functions-reference-node.md#typescript)
