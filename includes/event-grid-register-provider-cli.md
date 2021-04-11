---
title: ta med fil
description: ta med fil
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 08/17/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: cd778da5fd53cb8744a9f267384fcc8e11941582
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105645464"
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
