---
title: ta med fil
description: ta med fil
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 08/17/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 579ea74c4767ba1afb6e0128c9f12ff83f7f3998
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97826695"
---
## <a name="enable-the-event-grid-resource-provider"></a>Aktivera Event Grid Resource Provider

Om du inte tidigare har använt Event Grid i din Azure-prenumeration kan du behöva registrera Event Grid Resource Provider. Registrera providern genom att köra följande kommando:

```azurecli-interactive
az provider register --namespace Microsoft.EventGrid
```

Det kan ta en stund innan registreringen har slutförts. Du kan kontrollera status genom att köra:

```azurecli-interactive
az provider show --namespace Microsoft.EventGrid --query "registrationState"
```

När `registrationState` är `Registered` kan du fortsätta.
