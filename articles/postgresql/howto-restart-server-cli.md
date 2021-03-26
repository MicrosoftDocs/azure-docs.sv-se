---
title: Starta om Server – Azure CLI – Azure Database for PostgreSQL-enskild server
description: Den här artikeln beskriver hur du kan starta om en Azure Database for PostgreSQL-enskild server med Azure CLI
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: af870c495ae707dd8319645eca979f0906234b4f
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105608399"
---
# <a name="restart-azure-database-for-postgresql---single-server-using-the-azure-cli"></a>Starta om Azure Database for PostgreSQL – en server med hjälp av Azure CLI
I det här avsnittet beskrivs hur du kan starta om en Azure Database for PostgreSQL-Server. Du kan behöva starta om servern för underhålls orsaker, vilket orsakar ett kort avbrott eftersom servern utför åtgärden.

Servern kommer att startas om när tjänsten är upptagen. Tjänsten kan till exempel bearbeta en tidigare begärd åtgärd, till exempel skalnings virtuella kärnor.
 
> [!NOTE] 
> Tiden som krävs för att slutföra en omstart beror på återställnings processen för PostgreSQL. För att minska omstarts tiden rekommenderar vi att du minimerar mängden aktivitet som inträffar på servern innan du startar om. Du kanske också vill öka kontroll punkts frekvensen. Du kan också justera kontroll punkter relaterade parameter värden `max_wal_size` , inklusive. Vi rekommenderar också att du kör `CHECKPOINT` kommandot innan du startar om servern.

## <a name="prerequisites"></a>Förutsättningar
För att slutföra den här instruktions guiden:
- Skapa en [Azure Database for postgresql-server](quickstart-create-server-up-azure-cli.md).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Den här artikeln kräver version 2,0 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="restart-the-server"></a>Starta om servern

Starta om servern med följande kommando:

```azurecli-interactive
az postgres server restart --name mydemoserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>Nästa steg

Lär dig hur [du ställer in parametrar i Azure Database for PostgreSQL](howto-configure-server-parameters-using-cli.md)
