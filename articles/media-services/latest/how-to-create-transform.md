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
ms.openlocfilehash: 4623610960d8f21a2dab3293c7499a2112416254
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "101718921"
---
# <a name="create-a-transform"></a>Skapa en transformering

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure CLI-skriptet i den här artikeln visar hur du skapar en transformering. Transformeringar beskriver ett enkelt arbetsflöde av uppgifter för att bearbeta video- eller ljudfiler (kallas ofta ”recept”). Du bör alltid kontrollera om det redan finns en transformering med önskat namn och ”recept”. Om så är fallet bör du återanvända den.

## <a name="prerequisites"></a>Förutsättningar

[Skapa ett Media Services-konto](./create-account-howto.md).

## <a name="cli"></a>[CLI](#tab/cli/)

> [!NOTE]
> Du kan bara ange en sökväg till en anpassad standardkodad JSON-fil för [StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset)i [koda med ett exempel på en anpassad transformering](custom-preset-cli-howto.md) .
>
> Du kan inte skicka ett fil namn när du använder [BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset).

## <a name="example-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/create-transform/Create-Transform.sh "Create a transform")]

## <a name="rest"></a>[REST](#tab/rest/)

[!INCLUDE [task general transform creation](./includes/task-create-basic-audio-rest.md)]

---

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [transforms next steps](./includes/transforms-next-steps.md)]
