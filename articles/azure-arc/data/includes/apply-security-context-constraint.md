---
author: MikeRayMSFT
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.topic: include
ms.date: 03/02/2021
ms.author: mikeray
ms.openlocfilehash: 0fca43f76b24a08ca96be749f7f2a822b0be2418
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101687602"
---
I det här avsnittet förklaras hur du använder en säkerhets kontext begränsning (SCC). För för hands versionen gör dessa säkerhets begränsningar begränsade. 

1. Hämta den anpassade säkerhets kontext begränsningen (SCC). Använd något av följande: 
   - [GitHub](https://github.com/microsoft/azure_arc/tree/main/arc_data_services/deploy/yaml/arc-data-scc.yaml) 
   - [Rådata](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/arc-data-scc.yaml)
   - `curl`
   
      Följande kommando hämtar Arc-data-SCC. yaml:

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

   ```console
   oc create rolebinding arc-data-rbac --clusterrole=system:openshift:scc:arc-data-scc --serviceaccount=arc:default
   ```
