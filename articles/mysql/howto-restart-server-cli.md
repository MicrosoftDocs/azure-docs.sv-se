---
title: Starta om Server – Azure CLI – Azure Database for MySQL
description: I den här artikeln beskrivs hur du startar om en Azure Database for MySQL-server med hjälp av Azure CLI.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 77f20bba76ee3a37e6a72481e0b3d13c9904b106
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94541835"
---
# <a name="restart-azure-database-for-mysql-server-using-the-azure-cli"></a>Starta om Azure Database for MySQL server med Azure CLI
I det här avsnittet beskrivs hur du kan starta om en Azure Database for MySQL-server. Du kan behöva starta om servern för underhålls orsaker, vilket orsakar ett kort avbrott eftersom servern utför åtgärden.

Servern kommer att startas om när tjänsten är upptagen. Tjänsten kan till exempel bearbeta en tidigare begärd åtgärd, till exempel skalnings virtuella kärnor.

Tiden som krävs för att slutföra en omstart beror på MySQL-återställnings processen. För att minska omstarts tiden rekommenderar vi att du minimerar mängden aktivitet som inträffar på servern innan du startar om.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här instruktions guiden:

- Du behöver en [Azure Database for MySQL-server](quickstart-create-server-up-azure-cli.md).
 
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Den här artikeln kräver version 2,0 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="restart-the-server"></a>Starta om servern

Starta om servern med följande kommando:

```azurecli-interactive
az mysql server restart --name mydemoserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>Nästa steg

Lär dig hur [du ställer in parametrar i Azure Database for MySQL](howto-configure-server-parameters-using-cli.md)
