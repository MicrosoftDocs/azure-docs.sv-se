---
title: Till gångs konverterings REST API
description: Beskriver hur du konverterar en till gång via REST API
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: e3db4b9c9b4a05142f1327f681b067748cb1a2f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104951648"
---
# <a name="use-the-model-conversion-rest-api"></a>Använda REST API:et för modellkonvertering

[Modell konverterings](model-conversion.md) tjänsten styrs via en [REST API](https://en.wikipedia.org/wiki/Representational_state_transfer). Detta API kan användas för att skapa konverteringar, Hämta konverterings egenskaper och lista befintliga konverteringar.

## <a name="rest-api-reference"></a>Referens för REST-API

Referens dokumentationen för fjärrrendering REST API finns [här](/rest/api/mixedreality/2021-01-01/remoterendering)och Swagger-definitionerna [här](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mixedreality/data-plane/Microsoft.MixedReality).

Vi tillhandahåller ett PowerShell-skript i [databasen arr samples](https://github.com/Azure/azure-remote-rendering) i mappen *scripts* , som kallas *Conversion.ps1*, som visar användningen av vår tjänst. Skriptet och dess konfiguration beskrivs här: exempel på [PowerShell-skript](../../samples/powershell-example-scripts.md). Vi tillhandahåller även SDK: er för [.net](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/remoterendering/Azure.MixedReality.RemoteRendering/README.md) och [Java]( https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/remoterendering/azure-mixedreality-remoterendering/README.md).

## <a name="next-steps"></a>Nästa steg

- [Använda Azure-Blob Storage för modellkonvertering](blob-storage.md)
- [Modell konvertering](model-conversion.md)