---
title: Visa en lista över Azure-bågen aktiverade PostgreSQL för storskaliga Server grupper som skapats i en Azure-datakontrollant för data
description: Visa en lista över Azure-bågen aktiverade PostgreSQL för storskaliga Server grupper som skapats i en Azure-datakontrollant för data
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 7577ca4b8a1d7db7ea99aadfef4fd2a445b66425
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "90942145"
---
# <a name="list-the-azure-arc-enabled-postgresql-hyperscale-server-groups-created-in-an-azure-arc-data-controller"></a>Visa en lista över Azure-bågen aktiverade PostgreSQL för storskaliga Server grupper som skapats i en Azure-datakontrollant för data

I den här artikeln förklaras hur du kan hämta listan över Server grupper som har skapats i din data styrenhet för båge.

Om du vill hämta den här listan använder du någon av följande metoder när du är ansluten till data styrenheten för bågen:

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="from-cli-with-azdata"></a>Från CLI med azdata
Det allmänna formatet för kommandot är:
```console
azdata arc postgres server list
```

Den returnerar utdata som:
```console
Name        State    Workers
----------  -------  ---------
postgres01  Ready    2
postgres02  Ready    2
```
Om du vill ha mer information om de parametrar som är tillgängliga för det här kommandot kör du:
```console
azdata arc postgres server list --help
```

## <a name="from-cli-with-kubectl"></a>Från CLI med kubectl
Kör något av följande kommandon.

**Om du vill visa en lista över Server grupper oavsett vilken version av postgres kör du:**
```console
kubectl get postgresqls
```
Den returnerar utdata som:
```console
NAME                                             STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
postgresql-12.arcdata.microsoft.com/postgres01   Ready   3/3          10.0.0.4:30499      51s
postgresql-12.arcdata.microsoft.com/postgres02   Ready   3/3          10.0.0.4:31066      6d
```

**Om du vill visa en lista över Server grupper med en angiven version av postgres kör du:**
```console
kubectl get postgresql-12
```

Om du vill visa en lista med Server grupper som kör version 11 av postgres ersätter du _postgresql-12_ med _postgresql-11_.

## <a name="next-steps"></a>Nästa steg:

* [Läs artikeln om hur du hämtar anslutnings slut punkter och skapar anslutnings strängarna för att ansluta till Server gruppen](get-connection-endpoints-and-connection-strings-postgres-hyperscale.md)
* [Läs artikeln om att visa konfigurationen av en Azure Arc-aktiverad PostgreSQL-Server grupp](show-configuration-postgresql-hyperscale-server-group.md)
