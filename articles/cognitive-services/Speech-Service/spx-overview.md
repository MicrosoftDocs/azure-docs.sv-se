---
title: Azure Speech CLI
titleSuffix: Azure Cognitive Services
description: Tal-CLI är ett kommando rads verktyg för att använda tal tjänsten utan att skriva någon kod. Tal CLI kräver minimal installation, och det är enkelt att börja experimentera med viktiga funktioner i tal tjänsten för att se om dina användnings fall kan uppfyllas.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/13/2021
ms.author: trbye
ms.custom: devx-track-azurecli
ms.openlocfilehash: 8f1e5f38e97a1b51a2d919deebbdc452e9daf993
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/17/2021
ms.locfileid: "98539773"
---
# <a name="what-is-the-speech-cli"></a>Vad är Speech CLI?

Tal-CLI är ett kommando rads verktyg för att använda tal tjänsten utan att skriva någon kod. Tal CLI kräver minimal installation, och det är enkelt att börja experimentera med viktiga funktioner i tal tjänsten för att se om dina användnings fall kan uppfyllas. Inom några minuter kan du köra enkla test arbets flöden som batch tal igenkänning från en katalog med filer eller text till tal i en samling med strängar från en fil. I många enkla arbets flöden är tal CLI produktions klart och kan skalas upp för att köra större processer med hjälp av automatiserade `.bat` eller Shell-skript.

De flesta funktioner i talet SDK är tillgängliga i tal-CLI och vissa avancerade funktioner och anpassningar är förenklade i tal-CLI. Överväg följande rikt linjer för att bestämma när du ska använda tal-CLI eller tal-SDK.

Använd tal-CLI när:
* Du vill experimentera med tal tjänst funktioner med minimal konfiguration och ingen kod
* Du har relativt enkla krav för ett produktions program med hjälp av tal tjänsten

Använd tal-SDK: n när:
* Du vill integrera funktionen för tal tjänster på ett speciellt språk eller en plattform (till exempel C#, python, C++)
* Du har komplexa krav som kan kräva avancerade tjänst begär Anden eller utveckla anpassade beteenden inklusive svars strömning

## <a name="core-features"></a>Kärn funktioner

* Tal igenkänning – konvertera tal till text från ljudfiler eller direkt från en mikrofon eller genom att skriva över en inspelad konversation.

* Tal syntes – konvertera text till tal med inmatade text från textfiler eller mata in direkt från kommando raden. Anpassa tal utmatnings egenskaper med [SSML-konfigurationer](speech-synthesis-markup.md)och antingen [standard-eller neurala-röster](speech-synthesis-markup.md#standard-neural-and-custom-voices).

* Tal översättning – Översätt ljud på ett käll språk till text eller ljud på ett mål språk.

* Kör på Azure Compute Resources – skicka tal CLI-kommandon som ska köras på en Azure Remote Compute-resurs med hjälp av `spx webjob` .

## <a name="get-started"></a>Kom igång

För att komma igång med tal-CLI, se [snabb](spx-basics.md)starten. Den här artikeln visar hur du kör några grundläggande kommandon och visar även något mer avancerade kommandon för att köra batch-åtgärder för tal-till-text och text till tal. När du har läst grunderna i artikeln bör du ha tillräckligt med en förståelse för syntaxen för att börja skriva vissa anpassade kommandon eller automatisera enkla tal tjänst åtgärder.

## <a name="next-steps"></a>Nästa steg

- Kom igång med [tal CLI-snabb](spx-basics.md) starten
- [Konfigurera ditt data lager](./spx-data-store-configuration.md)
- Lär dig hur du [kör batch-åtgärder med tal-CLI](./spx-batch-operations.md)
