---
title: Exempel på Azure CLI-skript – publicera en till gång
description: Den här artikeln visar hur du använder Azure CLI-skriptet för att publicera en till gång.
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
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: c283c39f1ea90275c42de1481a9cb9006f2b2c5f
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897096"
---
# <a name="cli-example-publish-an-asset"></a>CLI-exempel: Publicera en tillgång

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure CLI-skriptet i den här artikeln visar hur du skapar en strömningspositionerare och får tillbaka strömnings-URL:er. 

## <a name="prerequisites"></a>Förutsättningar 

[Skapa ett Media Services-konto](./create-account-howto.md).

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

## <a name="example-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/publish-asset/Publish-Asset.sh "Publish an asset")]

## <a name="next-steps"></a>Nästa steg

[Översikt över Media Services](media-services-overview.md)
