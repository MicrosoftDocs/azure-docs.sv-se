---
title: Azure Storage Explorer kommando rads alternativ | Microsoft Docs
description: Dokumentation för Azure Storage Explorer start kommando rads alternativ
services: storage
author: chuye
ms.service: storage
ms.topic: article
ms.date: 02/24/2021
ms.author: chuye
ms.openlocfilehash: 0d588d85852f798542c5d52658e8dae3b9e57949
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101746406"
---
# <a name="azure-storage-explorer-command-line-options"></a>Azure Storage Explorer kommando rads alternativ

Microsoft Azure Storage Explorer innehåller en uppsättning kommando rads alternativ som kan läggas till när programmet startas. De flesta kommando rads alternativ är för fel sökning eller fel söknings syfte.

## <a name="command-line-options"></a>Kommandoradsalternativ
Alternativ  | Beskrivning
:------- | :-----------
`--debug`/`--prod`  | Starta programmet i fel söknings läge eller produktions läge. I fel söknings läge lagras de lokala bilage data i programmets lokala lagring och den kommer inte att krypteras. Dolda egenskaper visas i panelen Egenskaper för markerade resursfiler. Loggens detalj nivå anges till Skriv ut fel söknings meddelanden som visar Storage Explorer interna installations logiken. Standardvärdet är `--prod`.
`--lang`  | Starta programmet med ett specifikt språk. Till exempel `--lang="zh-Hans"`.
`--disable-gpu` | Starta programmet utan GPU-acceleration.
`--auto-open-dev-tools` | Låt programmet öppna fönstret utvecklarverktyg så snart webbläsarfönstret visas. Det här alternativet är användbart när du vill nå en Bryt punkt på en rad i Start koden för webbläsarfönstret.
`--verbosity` | Ange den detaljerade nivån för Storage Explorer loggning. De utförliga nivåer som stöds är `debug` ,,,, `verbose` `info` `warn` `error` och `silent` . Till exempel `--verbosity=verbose`. Vid körning i produktions läge är standard nivån för utförlighet `info` . När du kör i fel söknings läge är loggens detalj nivå alltid `debug` .
`--log-dir` | Ange katalog för att spara loggfiler. Till exempel `--log-dir=path_to_a_directory`.

Ett exempel på att starta Storage Explorer med anpassade kommando rads alternativ

```shell
./MicrosoftAzureStorageExplorer --lang=en --auto-open-dev-tools
```

> [!NOTE]
> Dessa kommando rads alternativ kan ändras i nya Storage Explorer-versioner.

## <a name="when-to-use-command-line-options"></a>När du ska använda kommando rads alternativ

Vissa kommando rads alternativ kan användas för att anpassa Storage Explorer. För de alternativ som har motsvarande användar inställningar, till exempel `--lang` . Vi rekommenderar att du använder användar inställningarna i stället för att använda kommando rads alternativet. 

De andra kommando rads alternativen kan vara användbara för fel sökning och fel sökning. Om du stöter på ett problem i Storage Explorer kan det hjälpa oss att få mer detaljerad information om hur du kan undersöka problemet i fel söknings läge.