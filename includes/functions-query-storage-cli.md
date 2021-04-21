---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 642989df8dca9d4ae121d80e30a9fb6d8dd06286
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107799850"
---
### <a name="query-the-storage-queue"></a>Fråga Storage-kön

Du kan använda kommandot [`az storage queue list`](/cli/azure/storage/queue#az_storage_queue_list) för att visa Storage-köerna i ditt konto, som i följande exempel:

```azurecli-interactive
az storage queue list --output tsv
```

Utdata från det här kommandot innehåller en kö med `outqueue` namnet , som är den kö som skapades när funktionen kördes.

Använd sedan kommandot [`az storage message peek`](/cli/azure/storage/message#az_storage_message_peek) för att visa meddelandena i den här kön, som i det här exemplet:

```azurecli-interactive
echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
```

Den returnerade strängen ska vara samma som det meddelande som du skickade för att testa funktionen.

> [!NOTE]  
> I föregående exempel avkodas den returnerade strängen från base64. Det beror på att Queue Storage-bindningar skriver till och läser från Azure Storage [som base64-strängar](../articles/azure-functions/functions-bindings-storage-queue-trigger.md#encoding).