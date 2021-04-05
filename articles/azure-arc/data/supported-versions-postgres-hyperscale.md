---
title: Versioner som stöds postgres med Azure Arc Enabled PostgreSQL-storskalig
description: Versioner som stöds postgres med Azure Arc Enabled PostgreSQL-storskalig
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 0f3f9e7f4566800bdea6871cae1c5bafb3b37b95
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "90941588"
---
# <a name="supported-versions-of-postgres-with-azure-arc-enabled-postgresql-hyperscale"></a>Versioner av Postgres som stöds med Azure Arc-arkiverad PostgreSQL-hyperskala

I den här artikeln förklaras vilka versioner av postgres som är tillgängliga med Azure Arc Enabled PostgreSQL-skalning.
Listan över versioner som stöds utvecklas över tid. Idag är de viktigaste versionerna som stöds:
- Postgres 12 (standard)
- Postgres 11

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="how-to-chose-between-versions"></a>Hur väljer man mellan versioner?
Vi rekommenderar att du tittar på vilka versioner dina program har utformats för och vilka funktioner som finns i var och en av versionerna. Läs mer om varje version på den officiella postgres-webbplatsen:
- [Postgres 12 (standard)](https://www.postgresql.org/docs/12/index.html)
- [Postgres 11](https://www.postgresql.org/docs/11/index.html)

## <a name="how-to-create-a-particular-version-in-azure-arc-enabled-postgresql-hyperscale"></a>Hur skapar jag en viss version i Azure Arc-aktiverad PostgreSQL-skalning?
När du skapar kan du ange vilken version du vill skapa genom att skicka parametern _--motor version_ . Om du inte anger någon versions information skapas som standard en Server grupp med postgres version 12.

## <a name="how-do-be-notified-when-other-versions-are-available"></a>Hur meddelas när andra versioner är tillgängliga?
Kom tillbaka och Läs den här artikeln. Det kommer att uppdateras efter behov. Du kan också lista de olika typerna av anpassade resurs definitioner (CRD) i data styrenheten Arc i ditt Kubernetes-kluster.
Kör följande kommando:
```console
kubectl get crds
```

Den returnerar utdata som:
```console
NAME                                            CREATED AT
datacontrollers.arcdata.microsoft.com           2020-08-31T20:15:16Z
postgresql-11s.arcdata.microsoft.com            2020-08-31T20:15:16Z
postgresql-12s.arcdata.microsoft.com            2020-08-31T20:15:16Z
sqlmanagedinstances.sql.arcdata.microsoft.com   2020-08-31T20:15:16Z
```

I det här exemplet indikerar detta utdata det finns två typer av CRD relaterade till postgres:
- postgresql-12s.arcdata.microsoft.com är CRD för postgres 12
- postgresql-11s.arcdata.microsoft.com är CRD för postgres 11

Dessa är CRDs, inte Server grupper. Förekomsten av en CRD är inte en indikation på att du har skapat en Server grupp för den versionen eller inte.
CRD är en indikation på vilken typ av resurser som kan skapas.

## <a name="next-steps"></a>Nästa steg:
- [Läs om hur du skapar en Azure Arc-aktiverad PostgreSQL-skalning](create-postgresql-hyperscale-server-group.md)
- [Läs om hur du hämtar en lista över Azure Arc-aktiverade PostgreSQL för storskaliga Server grupper som skapats i din ARC-datakontrollant](list-server-groups-postgres-hyperscale.md)
