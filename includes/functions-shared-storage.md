---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/20/2018
ms.author: glenga
ms.openlocfilehash: 82d122ed236dc72ced7ebafe2301ef5f1143897f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "76963671"
---
Använd ett separat lagrings konto för varje Function-app för att maximera prestanda. Detta är särskilt viktigt när du har inaktiverat funktioner i Durable Functions eller Event Hub, som båda genererar en stor mängd lagrings transaktioner. När din program logik interagerar med Azure Storage, antingen direkt (med Storage SDK) eller genom en av lagrings bindningarna, bör du använda ett dedikerat lagrings konto. Om du till exempel har en Event Hub-utlöst funktion som skriver vissa data till Blob Storage, använder du två lagrings konton &mdash; en för Function-appen och en annan för de blobbar som lagras av funktionen.