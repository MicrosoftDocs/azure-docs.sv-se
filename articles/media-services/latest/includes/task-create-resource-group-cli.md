---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: d567a4f7d9b9429887d6396cb2b03dfc34c6ac93
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88602244"
---
<!-- Create a resource group -->

Använd följande kommando för att skapa en resurs grupp. Välj den geografiska region som ska användas för att lagra media-och metadata-poster för ditt Media Services-konto. Den här regionen kommer att användas för att bearbeta och strömma dina media.

```azurecli
az group create --name amsResourceGroup --location westus2
```
