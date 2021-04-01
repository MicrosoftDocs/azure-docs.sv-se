---
author: burkeholland
ms.service: static-web-apps
ms.topic: include
ms.date: 05/08/2020
ms.author: buhollan
ms.openlocfilehash: 879d0c2e8c4cd66ce04c0298d00f7d6a1987acf5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "83597235"
---
#### <a name="local-proxy"></a>Lokal Proxy

Du kan konfigurera en proxy för det Live-serverprogram för Visual Studio Code som dirigerar alla förfrågningar till `/api` den pågående API-slutpunkten på `http://127.0.0.1:7071/api` .

1. Öppna filen _. VSCode/settings.jspå_ filen.

1. Lägg till följande inställningar för att ange en proxy för Live-servern.

   ```json
   "liveServer.settings.proxy": {
      "enable": true,
      "baseUri": "/api",
      "proxyUri": "http://127.0.0.1:7071/api"
   }
   ```

   Den här konfigurationen sparas bäst i filen med projekt inställningar i stället för i användar inställnings filen.

   Om du använder projekt inställningar ser du till att proxyn inte tillämpas på alla andra projekt som öppnas i Visual Studio Code.
