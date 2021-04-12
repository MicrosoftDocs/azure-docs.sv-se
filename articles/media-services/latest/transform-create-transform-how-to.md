---
title: Exempel på Azure CLI-skript – skapa en transformering
description: Transformeringar beskriver ett enkelt arbetsflöde av uppgifter för att bearbeta video- eller ljudfiler (kallas ofta ”recept”). Azure CLI-skriptet i den här artikeln visar hur du skapar en transformering.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: multiple
ms.topic: how-to
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/18/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 75b45067be49475ecd2e07aed9c4479b147fdd29
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106490275"
---
# <a name="create-a-transform"></a>Skapa en transformering

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure CLI-skriptet i den här artikeln visar hur du skapar en transformering. Transformeringar beskriver ett enkelt arbetsflöde av uppgifter för att bearbeta video- eller ljudfiler (kallas ofta ”recept”). Du bör alltid kontrollera om det redan finns en transformering med önskat namn och ”recept”. Om så är fallet bör du återanvända den.

## <a name="prerequisites"></a>Förutsättningar

[Skapa ett Media Services-konto](./account-create-how-to.md).

## <a name="cli"></a>[CLI](#tab/cli/)

> [!NOTE]
> Du kan bara ange en sökväg till en anpassad standardkodad JSON-fil för [StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset)i [koda med ett exempel på en anpassad transformering](transform-custom-preset-cli-how-to.md) .
>
> Du kan inte skicka ett fil namn när du använder [BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset).

## <a name="example-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/create-transform/Create-Transform.sh "Create a transform")]

## <a name="rest"></a>[REST](#tab/rest/)

[!INCLUDE [task general transform creation](./includes/task-create-basic-audio-rest.md)]

---

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [transforms next steps](./includes/transforms-next-steps.md)]
