---
title: Modell konvertering
description: Beskriver processen för att konvertera en modell för åter givning
author: jakrams
ms.author: jakras
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: e899b249261ea3238695a2e2be6001cb6a9bc763
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "91318066"
---
# <a name="convert-models"></a>Konvertera modeller

Med Azure Remote rendering kan du återge mycket komplexa modeller. För att uppnå högsta prestanda måste data förbehandlas för att vara i optimalt format. Beroende på mängden data kan det här steget ta en stund. Det är opraktiskt att om den här tiden användes vid inläsning av modellen. Det kan också vara onödig att upprepa den här processen för flera sessioner. Av dessa orsaker tillhandahåller ARR-tjänsten en dedikerad *konverterings tjänst*, som du kan köra i förväg.
När den har konverterats kan en modell läsas in från ett Azure Storage-konto.

## <a name="supported-source-formats"></a>Käll format som stöds

Konverterings tjänsten har stöd för följande format:

- **FBX**  (version 2011 till version 2020)
- **GLTF** / **GLB** (version 2. x)

Det finns mindre skillnader mellan formaten vad gäller omvandling av material egenskaper, enligt vad som anges i kapitel [material mappning för modell format](../../reference/material-mapping.md).

## <a name="the-conversion-process"></a>Konverterings processen

1. [Förbered två Azure Blob Storage-behållare](blob-storage.md): en för indata, en för utdata
1. Ladda upp din modell till Indataporten (valfritt under en under Sök väg)
1. Utlös konverterings processen genom [modell konverteringen REST API](conversion-rest-api.md)
1. Avsök tjänsten för konverterings förlopp
1. När du är färdig läser du in en modell
    - från ett länkat lagrings konto (se stegen "länka lagrings konton" på [skapa ett konto](../create-an-account.md#link-storage-accounts) för att länka ditt lagrings konto)
    - eller genom att tillhandahålla en *signatur för delad åtkomst (SAS)*.

Alla modell data (indata och utdata) lagras i användaren som tillhandahöll Azure Blob Storage. Med Azure Remote rendering får du full kontroll över din till gångs hantering.

## <a name="pricing"></a>Priser

Information om priser för konvertering finns på sidan med [priser för Fjärråter givning](https://azure.microsoft.com/pricing/details/remote-rendering) .


## <a name="conversion-parameters"></a>Konverterings parametrar

De olika konverterings alternativen finns i [det här kapitlet](configure-model-conversion.md).

## <a name="examples"></a>Exempel

- [Snabb start: konvertera en modell för åter givning](../../quickstarts/convert-model.md) är en steg-för-steg-introduktion om hur du konverterar en modell.
- [Exempel på PowerShell-skript](../../samples/powershell-example-scripts.md)som demonstrerar användningen av konverterings tjänsten finns i [lagrings plats för arr-exempel](https://github.com/Azure/azure-remote-rendering) i mappen *skript* .

## <a name="next-steps"></a>Nästa steg

- [Använda Azure-Blob Storage för modellkonvertering](blob-storage.md)
- [Modell konverterings REST API](conversion-rest-api.md)
- [Konfigurera modell konverteringen](configure-model-conversion.md)
- [Utforma filer för konvertering](layout-files-for-conversion.md)
- [Materialmappning för modellformat](../../reference/material-mapping.md)
