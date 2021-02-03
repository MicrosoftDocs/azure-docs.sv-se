---
author: MikeRayMSFT
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.topic: include
ms.date: 01/15/2021
ms.author: mikeray
ms.openlocfilehash: 6c8dbeea83cba306cfb788cf447236088045ffc9
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99494028"
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

   > [!NOTE]
   > RedHat OpenShift 4,5 eller senare, ändrar hur SCC ska tillämpas på tjänst kontot.
   > Använd samma namnrymd här och i `azdata arc dc create` kommandot nedan. Exempel är `arc` . 
   > 
   > Om du använder RedHat OpenShift 4,5 eller senare, kör: 
   >
   >```console
   >oc create rolebinding arc-data-rbac --clusterrole=system:openshift:scc:arc-data-scc --serviceaccount=arc:default
   >```
