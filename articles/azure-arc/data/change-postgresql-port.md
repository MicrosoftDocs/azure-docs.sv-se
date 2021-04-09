---
title: Ändra PostgreSQL-port
description: Ändra den port som Azure-bågen aktiverade PostgreSQL för den storskaliga Server gruppen lyssnar på.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 11/02/2020
ms.topic: how-to
ms.openlocfilehash: 45424408c790e4921be48464818c55fe74313fd3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93328757"
---
# <a name="change-the-port-on-which-the-server-group-is-listening"></a>Ändra porten som server gruppen lyssnar på 

Att ändra porten är en vanlig redigerings åtgärd i Server gruppen. Kör följande kommando för att ändra porten:
```console
 azdata arc postgres server edit -n <server group name> --port <desired port number>
```

Anta till exempel att namnet på din server grupp är _postgres01_ och att du vill lyssna på port _866_. Du kör följande kommando:
```console
 azdata arc postgres server edit -n postgres01 --port 866
```

## <a name="verify-that-the-port-was-changed"></a>Kontrol lera att porten har ändrats

Kontrol lera att porten har ändrats genom att köra följande kommando för att visa konfigurationen av Server gruppen:
```console
azdata arc postgres server show -n <server group name>
```

I kommandots utdata tittar du på det port nummer som visas för objektet "Port" i avsnittet "tjänst" i specifikationerna för din server grupp.
Alternativt kan du kontrol lera i objektet externalEndpoint i status avsnittet i specifikationerna för Server gruppen att IP-adressen följs av det port nummer som du har konfigurerat.

Om vi fortsätter med exemplet ovan kan du köra kommandot:
```console
azdata arc postgres server show -n postgres01
```

och du ser port 866 som hänvisas till här:

```console
"service": {
      "port": 866,
      "type": "LoadBalancer"
    },
```
och här

```console
"status": {
    "externalEndpoint": "12.678.345.09:866",
    "logSearchDashboard": "https://12.345.678.90:30777/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:postgres01'))",
    "metricsDashboard": "https://12.345.678.90:30777/grafana/d/postgres-metrics?var-Namespace=arc&var-Name=postgres01",
    "readyPods": "3/3",
    "state": "Ready"
  }
```
## <a name="next-steps"></a>Nästa steg
- Läs om [hur du ansluter till din server grupp](get-connection-endpoints-and-connection-strings-postgres-hyperscale.md).
- Läs om hur du kan konfigurera andra aspekter av Server gruppen i avsnittet How-to\Manage\Configure & Scale i dokumentationen.
