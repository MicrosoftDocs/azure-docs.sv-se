---
title: Hantera zonredundant hög tillgänglighet – Azure CLI – Azure Database for MySQL flexibel server
description: Den här artikeln beskriver hur du konfigurerar zonredundant hög tillgänglighet i Azure Database for MySQL flexibel server med Azure CLI.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 04/1/2021
ms.custom: references_regions
ms.openlocfilehash: fb53ad309c741fc898bcf3e27347038c0e382ea4
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107509146"
---
# <a name="manage-zone-redundant-high-availability-in-azure-database-for-mysql-flexible-server-with-azure-cli"></a>Hantera zonredundant hög tillgänglighet i Azure Database for MySQL flexibel server med Azure CLI

> [!NOTE]
> Azure Database for MySQL Flexibel server finns i offentlig förhandsversion. 

I artikeln beskrivs hur du kan aktivera eller inaktivera zonredundant konfiguration med hög tillgänglighet när servern skapas på din flexibla server. Du kan även inaktivera zonredundant hög tillgänglighet när servern har skapats. Det går inte att aktivera zonredundant hög tillgänglighet när servern har skapats.

Funktionen för hög tillgänglighet ger fysiskt separata primära repliker och väntelägesrepliker i olika zoner. Mer information finns i dokumentationen [om begrepp med hög tillgänglighet.](./concepts/../concepts-high-availability.md) Om du aktiverar eller inaktiverar hög tillgänglighet ändras inte dina andra inställningar, inklusive VNET-konfiguration, brandväggsinställningar och kvarhållning av säkerhetskopior. Inaktivering av hög tillgänglighet påverkar inte programmets anslutning och åtgärder.

> [!IMPORTANT]
> Zonredundant hög tillgänglighet är tillgänglig i en begränsad uppsättning regioner. Granska de regioner som stöds [här.](https://docs.microsoft.com/azure/mysql/flexible-server/overview#azure-regions) 

## <a name="prerequisites"></a>Förutsättningar
- Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.
- Installera eller uppgradera Azure CLI till den senaste versionen. Se [Installera Azure CLI.](/cli/azure/install-azure-cli)
-  Logga in på Azure-kontot med [kommandot az login.](/cli/azure/reference-index#az-login) Observera **id-egenskapen,** som refererar till **prenumerations-ID för** ditt Azure-konto.

    ```azurecli-interactive
    az login
    ````

- Om du har flera prenumerationer väljer du lämplig prenumeration där du vill skapa servern med ```az account set``` kommandot .
`
    ```azurecli
    az account set --subscription <subscription id>
    ```

## <a name="enable-high-availability-during-server-creation"></a>Aktivera hög tillgänglighet när servern skapas
Du kan bara skapa en server med prisnivåerna Generell användning eller Minnesoptimerad med hög tillgänglighet. Du kan endast aktivera hög tillgänglighet för en server under skapatiden.

**Användning:**

```azurecli
az mysql flexible-server create [--high-availability {Disabled, Enabled}]
                                [--resource-group]
                                [--name]
```

**Exempel:**
```azurecli
az mysql flexible-server create --name myservername --sku-name Standard-D2ds_v4 --resource-group myresourcegroup --high-availability Enabled
```

## <a name="disable-high-availability"></a>Inaktivera hög tillgänglighet

Du kan inaktivera hög tillgänglighet med hjälp av [kommandot az mysql flexible-server update.](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_update) Observera att inaktivering av hög tillgänglighet endast stöds om servern har skapats med hög tillgänglighet. 

```azurecli
az mysql flexible-server update [--high-availability {Disabled, Enabled}]
                                [--resource-group]
                                [--name]
```

**Exempel:**
```azurecli
az mysql flexible-server update --resource-group myresourcegroup --name myservername --high-availability Disabled
```


## <a name="next-steps"></a>Nästa steg

-   Lär dig mer om [affärskontinuhet](./concepts-business-continuity.md)
-   Läs mer om [zonredundant hög tillgänglighet](./concepts-high-availability.md)
