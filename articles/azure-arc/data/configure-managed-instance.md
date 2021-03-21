---
title: Konfigurera Azure Arc-aktiverad SQL-hanterad instans
description: Konfigurera Azure Arc-aktiverad SQL-hanterad instans
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 6e2443014f6788504a11784945078187a5a72de4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98985877"
---
# <a name="configure-azure-arc-enabled-sql-managed-instance"></a>Konfigurera Azure Arc-aktiverad SQL-hanterad instans

Den här artikeln förklarar hur du konfigurerar Azure Arc-aktiverade SQL-instansen.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="configure-resources"></a>Konfigurera resurser

### <a name="configure-using-azure-data-cli-azdata"></a>Konfigurera med [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]

Du kan redigera konfigurationen av Azure Arc-aktiverade SQL-hanterade instanser med [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] . Kör följande kommando för att Visa konfigurations alternativ. 

```
azdata arc sql mi edit --help
```

I följande exempel anges processor kärnor och minnes förfrågningar och-gränser.

```
azdata arc sql mi edit --cores-limit 4 --cores-request 2 --memory-limit 4Gi --memory-request 2Gi -n <NAME_OF_SQL_MI>
```

Om du vill visa de ändringar som gjorts i SQL-hanterad instans kan du använda följande kommandon för att visa konfigurationen yaml-filen:

```
azdata arc sql mi show -n <NAME_OF_SQL_MI>
```

## <a name="configure-server-options"></a>Konfigurera Server alternativ

Du kan konfigurera server konfigurations inställningar för Azure Arc-aktiverad SQL-hanterad instans när den skapades. Den här artikeln beskriver hur du konfigurerar inställningar som att aktivera eller inaktivera MSSQL-agenten, aktivera vissa spårnings flaggor för fel söknings scenarier.

Följ dessa steg om du vill ändra någon av dessa inställningar:

1. Skapa en anpassad `mssql-custom.conf` fil som innehåller inställningar för mål. I följande exempel aktive ras SQL-agenten och spårnings flaggan 1204.:

   ```
   [sqlagent]
   enabled=true
   
   [traceflag]
   traceflag0 = 1204
   ```

1. Kopiera `mssql-custom.conf` filen till `/var/opt/mssql` i `mssql-miaa` behållaren i `master-0` pod. Ersätt `<namespaceName>` med det Big data kluster namnet.

   ```bash
   kubectl cp mssql-custom.conf master-0:/var/opt/mssql/mssql-custom.conf -c mssql-server -n <namespaceName>
   ```

1. Starta om SQL Server instans.  Ersätt `<namespaceName>` med det Big data kluster namnet.

   ```bash
   kubectl exec -it master-0  -c mssql-server -n <namespaceName> -- /bin/bash
   supervisorctl restart mssql-server
   exit
   ```


**Kända begränsningar**
- Stegen ovan kräver Kubernetes-kluster administratörs behörighet
- Detta kan ändras i för hands versionen
