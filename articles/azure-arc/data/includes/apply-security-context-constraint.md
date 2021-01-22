---
author: MikeRayMSFT
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.topic: include
ms.date: 01/15/2021
ms.author: mikeray
ms.openlocfilehash: 17a8c9580a8e69213c7f34e8ec889f7e46c6d17d
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98696079"
---
I det här avsnittet förklaras hur du använder en säkerhets kontext begränsning (SCC). För för hands versionen gör dessa säkerhets begränsningar begränsade. 

1. Hämta den anpassade säkerhets kontext begränsningen (SCC). Använd något av följande: 
   - [GitHub](https://github.com/microsoft/azure_arc/tree/main/arc_data_services/deploy/yaml/arc-data-scc.yaml) 
   - ([RAW](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/arc-data-scc.yaml))
   - `curl` Följande kommando hämtar Arc-data-SCC. yaml:

      ```console
      curl https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/arc-data-scc.yaml -o arc-data-scc.yaml
      ```

1. Skapa SCC.

   ```console
   oc create -f arc-data-scc.yaml
   ```

1. Tillämpa SCC på tjänst kontot.

   > [!NOTE]
   > Använd samma namnrymd här och i `azdata arc dc create` kommandot nedan. Exempel är `arc` .

   ```console
   oc adm policy add-scc-to-user arc-data-scc --serviceaccount default --namespace arc
   ```