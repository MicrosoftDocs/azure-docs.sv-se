---
title: Till gångs konverterings REST API
description: Beskriver hur du konverterar en till gång via REST API
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: 4f8ac72e2b598a6c7631d691cc1bfb82cdba7599
ms.sourcegitcommit: 7ec45b7325e36debadb960bae4cf33164176bc24
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100530271"
---
# <a name="use-the-model-conversion-rest-api"></a>Använda REST API:et för modellkonvertering

[Modell konverterings](model-conversion.md) tjänsten styrs via en [REST API](https://en.wikipedia.org/wiki/Representational_state_transfer). Detta API kan användas för att skapa konverteringar, Hämta konverterings egenskaper och lista befintliga konverteringar.

## <a name="rest-api-reference"></a>Referens för REST-API

Referens dokumentationen för fjärrrendering REST API finns [här](https://docs.microsoft.com/rest/api/mixedreality/2021-01-01preview/remoterendering)och Swagger-definitionerna [här](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mixedreality/data-plane/Microsoft.MixedReality).

Vi tillhandahåller ett PowerShell-skript i [databasen arr samples](https://github.com/Azure/azure-remote-rendering) i mappen *scripts* , som kallas *Conversion.ps1*, som visar användningen av vår tjänst. Skriptet och dess konfiguration beskrivs här: exempel på [PowerShell-skript](../../samples/powershell-example-scripts.md). Vi tillhandahåller även SDK: er för [.net](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/mixedreality/Azure.MixedReality.RemoteRendering), Java och python.

## <a name="next-steps"></a>Nästa steg

- [Använda Azure-Blob Storage för modellkonvertering](blob-storage.md)
- [Modell konvertering](model-conversion.md)
