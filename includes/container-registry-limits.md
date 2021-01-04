---
title: ta med fil
description: ta med fil
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 06/18/2020
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 3fe1b966b56142fd312850ac5d77839b7d9db434
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/20/2020
ms.locfileid: "97706220"
---
| Resurs | Basic | Standard | Premium |
|---|---|---|---|
| Ingår lagring<sup>1</sup> (GIB) | 10 | 100 | 500 |
| Lagrings gräns (TiB) | 20| 20 | 20 |
| Maximal bild skikt storlek (GiB) | 200 | 200 | 200 |
| ReadOps per minut<sup>2, 3</sup> | 1 000 | 3 000 | 10 000 |
| WriteOps per minut<sup>2, 4</sup> | 100 | 500 | 2 000 |
| Ladda ned bandbredd Mbps<sup>2</sup> | 30 | 60 | 100 |
| Ladda upp bandbredd Mbps<sup>2</sup> | 10 | 20 | 50 |
| Webhooks | 2 | 10 | 500 |
| Geo-replikering | Saknas | Saknas | [Stöds][geo-replication] |
| Tillgänglighetszoner | Saknas | Saknas | [Förhandsgranskning][zones] |
| Förtroende för innehåll | Saknas | Saknas | [Stöds][content-trust] |
| Privat länk med privata slut punkter | Saknas | Saknas | [Stöds][plink] |
| &bull; Privata slut punkter | Saknas | Saknas | 10 |
| Åtkomst till tjänst slut punktens VNet | Saknas | Saknas | [Förhandsgranskning][vnet] |
| Kundhanterade nycklar | Saknas | Saknas | [Stöds][cmk] |
| Databas – begränsade behörigheter | Saknas | Saknas | [Förhandsgranskning][token]|
| &bull; Token | Saknas | Saknas | 20 000 |
| &bull; Omfångs kartor | Saknas | Saknas | 20 000 |
| &bull; Mappning av databaser per område | Saknas | Saknas | 500 |


<sup>1</sup> lagring ingår i den dagliga taxan för varje nivå. För ytterligare lagring debiteras du ytterligare en daglig taxa per GiB, upp till lagrings gränsen. Mer information om priser finns i [Azure Container Registry prissättning][pricing].

<sup>2</sup>*ReadOps*, *WriteOps* och *bandbredd* är minimala uppskattningar. Azure Container Registry strävar efter att förbättra prestandan när användningen kräver.

<sup>3</sup> En [Docker-pull](https://docs.docker.com/registry/spec/api/#pulling-an-image) översätter sig till flera Läs åtgärder baserat på antalet lager i bilden, plus manifest hämtning.

<sup>4</sup> En [Docker push](https://docs.docker.com/registry/spec/api/#pushing-an-image) översätts till flera Skriv åtgärder baserat på antalet lager som måste flyttas. En `docker push` innehåller *ReadOps* för att hämta ett manifest för en befintlig avbildning.

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/

<!-- LINKS - Internal -->
[geo-replication]: ../articles/container-registry/container-registry-geo-replication.md
[content-trust]: ../articles/container-registry/container-registry-content-trust.md
[vnet]: ../articles/container-registry/container-registry-vnet.md
[plink]: ../articles/container-registry/container-registry-private-link.md
[cmk]: ../articles/container-registry/container-registry-customer-managed-keys.md
[token]: ../articles/container-registry/container-registry-repository-scoped-permissions.md
[zones]: ../articles/container-registry/zone-redundancy.md
