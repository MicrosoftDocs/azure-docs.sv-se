---
title: Starta om/stoppa/starta – Azure Portal – Azure Database for MySQL flexibel server
description: Den här artikeln beskriver hur du startar om/stoppar/startar om Azure Database for MySQL via Azure CLI.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 03/30/2021
ms.openlocfilehash: c9e646ad40c669e51f052ac9888cdd7c6883a848
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107509106"
---
# <a name="restartstopstart-an-azure-database-for-mysql---flexible-server-preview"></a>Starta om/stoppa/starta en Azure Database for MySQL – flexibel server (förhandsversion)

> [!IMPORTANT]
> Azure Database for MySQL – flexibel server är för närvarande i offentlig förhandsversion.

Den här artikeln visar hur du startar om, startar och stoppar en flexibel server med hjälp av Azure CLI.

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

- Skapa en flexibel MySQL-server om du inte redan har skapat en med ```az mysql flexible-server create``` kommandot .

    ```azurecli
    az mysql flexible-server create --resource-group myresourcegroup --name myservername
    ```

## <a name="stop-a-running-server"></a>Stoppa en server som körs
Om du vill stoppa en server kör du  ```az mysql flexible-server stop``` kommandot . Om du använder [lokal kontext](/cli/azure/config/param-persist)behöver du inte ange några argument.

**Användning:**
```azurecli
az mysql flexible-server stop  [--name]
                               [--resource-group]
                               [--subscription]
```

**Exempel utan lokal kontext:**
```azurecli
az mysql flexible-server stop  --resource-group --name myservername
```

**Exempel med lokal kontext:**
```azurecli
az mysql flexible-server stop
```

## <a name="start-a-stopped-server"></a>Starta en stoppad server
Starta en server genom att köra  ```az mysql flexible-server start``` kommandot . Om du använder [lokal kontext](/cli/azure/config/param-persist)behöver du inte ange några argument.

**Användning:**
```azurecli
az mysql flexible-server start [--name]
                               [--resource-group]
                               [--subscription]
```

**Exempel utan lokal kontext:**
```azurecli
az mysql flexible-server start  --resource-group --name myservername
```

**Exempel med lokal kontext:**
```azurecli
az mysql flexible-server start
```

> [!IMPORTANT]
> När servern har startats om är alla hanteringsåtgärder nu tillgängliga för den flexibla servern.

## <a name="restart-a-server"></a>Starta om en server
Starta om en server genom att köra  ```az mysql flexible-server restart``` kommandot . Om du använder [lokal kontext](/cli/azure/config/param-persist)behöver du inte ange några argument.

**Användning:**
```azurecli
az mysql flexible-server restart [--name]
                                 [--resource-group]
                                 [--subscription]
```

**Exempel utan lokal kontext:**
```azurecli
az mysql flexible-server restart  --resource-group --name myservername
```

**Exempel med lokal kontext:**
```azurecli
az mysql flexible-server restart
```


> [!IMPORTANT]
> När servern har startats om är alla hanteringsåtgärder nu tillgängliga för den flexibla servern.

## <a name="next-steps"></a>Nästa steg
- Läs mer om [nätverk i Azure Database for MySQL flexibel server](./concepts-networking.md)
- [Skapa och hantera virtuella Azure Database for MySQL Flexible Server med hjälp av Azure Portal](./how-to-manage-virtual-network-portal.md).

