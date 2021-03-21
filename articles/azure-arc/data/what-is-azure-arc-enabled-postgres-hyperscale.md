---
title: Vad är Azure Arc aktiverat PostgreSQL-skalning?
description: Vad är Azure Arc aktiverat PostgreSQL-skalning?
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 02/11/2021
ms.topic: how-to
ms.openlocfilehash: 8edf540c3f67a3a8bee075569f0a2588cae18a62
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100390019"
---
# <a name="what-is-azure-arc-enabled-postgresql-hyperscale"></a>Vad är Azure Arc aktiverat PostgreSQL-skalning?

Azure Arc Enabled PostgreSQL-skalning är en av databas tjänsterna som är tillgängliga som en del av Azure Arc-aktiverade data tjänster. Azure Arc gör det möjligt att köra Azure Data Services lokalt, vid gränsen och i offentliga moln med Kubernetes och den infrastruktur som du väljer. Det värde som är av Azure Arc-aktiverade data tjänster återledas runt:
- Alltid aktuell
- Elastisk skalning
- Själv tjänst etablering
- Enhetlig hantering
- Stöd för frånkopplat scenario

Mer information finns på:
- [Vad är Azure Arc-aktiverade data tjänster](overview.md)
- [Anslutningslägen och krav](connectivity.md)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="compare-solutions"></a>Jämför lösningar

I det här avsnittet beskrivs hur Azure Arc-aktiverade PostgreSQL skiljer sig från Azure Database for PostgreSQL storskalig (citus)?

## <a name="azure-database-for-postgresql-hyperscale-citus"></a>Hyperskala (Citus) för Azure Database for PostgreSQL

:::image type="content" source="media/postgres-hyperscale/postgresql-hyperscale.png" alt-text="Azure SQL Database för PostgreSQL-storskalig (citus)":::

Detta är den storskaliga form faktorn för postgres-databasmotorn som är tillgänglig som databas som en tjänst i Azure (PaaS). Den drivs av citus-tillägget som möjliggör skalnings upplevelsen. I den här formulärs faktorn körs tjänsten i Microsoft-datacenter och drivs av Microsoft.

## <a name="azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc Enabled PostgreSQL-skalning

:::image type="content" source="media/postgres-hyperscale/postgresql-hyperscale-arc.png" alt-text="Azure Arc Enabled PostgreSQL-skalning":::

Detta är den storskaliga form faktorn för postgres-databasmotorn som är tillgänglig med Azure Arc-aktiverade data tjänster. Den drivs också av citus-tillägget som möjliggör skalnings upplevelsen. I den här formulärs faktorn tillhandahåller våra kunder den infrastruktur som är värd för systemen och använder dem.

## <a name="next-steps"></a>Nästa steg
- **Prova.** Kom igång snabbt med [Azure Arc-rivstart med](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) på Azure Kubernetes service (AKS), AWS elastisk Kubernetes service (EKS), Google Cloud Kubernetes Engine (GKE) eller i en virtuell Azure-dator. 

- **Skapa en egen.** Följ de här stegen för att skapa på ditt eget Kubernetes-kluster: 
   1. [Installera klient verktyg](install-client-tools.md)
   2. [Skapa data styrenheten för Azure-bågen](create-data-controller.md)
   3. [Skapa en Azure Database for PostgreSQL storskalig Server grupp på Azure Arc](create-postgresql-hyperscale-server-group.md) 

- **Learn**
   - [Läs mer om Azure Arc-aktiverade data tjänster](https://azure.microsoft.com/services/azure-arc/hybrid-data-services)
   - [Läs om Azure-bågen](https://aka.ms/azurearc)
