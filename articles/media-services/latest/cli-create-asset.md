---
title: Ladda upp innehåll till en Azure Media Services till gång med Azure CLI
description: Azure CLI-skriptet i det här avsnittet visar hur du skapar en Media Services-tillgång för att överföra innehåll till.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/18/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7b362ba30baf13211dc41a6043dcedd05bd506ea
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88585430"
---
# <a name="create-an-asset"></a>Skapa en tillgång

Den här artikeln visar hur du skapar en Media Services till gång.  Du kommer att använda en till gång för att lagra medie innehåll för kodning och strömning.  Läs mer om hur du Media Services till gångar genom att läsa [till gångar i Azure Media Services v3](assets-concept.md)

## <a name="prerequisites"></a>Förutsättningar

Följ stegen i [skapa ett Media Services konto](./create-account-howto.md) för att skapa ett Media Services konto och en resurs grupp som krävs för att skapa en till gång.

## <a name="methods"></a>Metoder

## <a name="cli"></a>[CLI](#tab/cli/)

[!INCLUDE [Create an asset with CLI](./includes/task-create-asset-cli.md)]

## <a name="cli-shell"></a>[CLI-gränssnitt](#tab/clishell/)

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

## <a name="example-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/create-asset/Create-Asset.sh "Create an asset")]

---

## <a name="next-steps"></a>Nästa steg

[Hantera tillgångar](manage-asset-concept.md)
