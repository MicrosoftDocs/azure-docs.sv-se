---
title: Konfigurera granskningsloggar och långsamma frågeloggar med Azure CLI – Azure Database for MySQL – flexibel server
description: Den här artikeln beskriver hur du konfigurerar och får åtkomst till loggar för långsamma frågor Azure Database for MySQL flexibel server från Azure CLI.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 03/30/2021
ms.openlocfilehash: b42aba84dcbff795ee4ca1f8d622527e8039f27a
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107509145"
---
# <a name="configure-slow-query-logs-for-azure-database-for-mysql---flexible-server-using-the-azure-cli"></a>Konfigurera långsamma frågeloggar för Azure Database for MySQL – flexibel server med hjälp av Azure CLI

> [!IMPORTANT]
> Azure Database for MySQL – Flexibel server är för närvarande i offentlig förhandsversion.

Artikeln visar hur du konfigurerar långsamma [frågeloggar för](concepts-slow-query-logs.md) din flexibla MySQL-server med Azure CLI. 

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

- Skapa en MySQL – flexibel server om du inte redan har skapat en med ```az mysql flexible-server create``` kommandot .

    ```azurecli
    az mysql flexible-server create --resource-group myresourcegroup --name myservername
    ```

## <a name="configure-slow-query-logs"></a>Konfigurera långsamma frågeloggar

>[!IMPORTANT]
> Vi rekommenderar att du endast loggar de händelsetyper och användare som krävs för granskningsändamål för att säkerställa att serverns prestanda inte påverkas kraftigt.

Aktivera och konfigurera långsamma frågeloggar för servern.

```azurecli
# Turn on statement level log
az mysql flexible-server parameter set \
--name log_statement \
--resource-group myresourcegroup \
--server-name mydemoserver \
--value all


# Set log_min_duration_statement time to 10 sec
# This setting will log all queries executing for more than 10 sec. Please adjust this threshold based on your definition for slow queries
az mysql server configuration set \
--name log_min_duration_statement \
--resource-group myresourcegroup \
--server mydemoserver \
--value 10000

# Enable Slow query logs
az mysql flexible-server parameter set \
--name slow_query_log \
--resource-group myresourcegroup \
--server-name mydemoserver \
--value ON

```

## <a name="next-steps"></a>Nästa steg
- Läs mer om [långsamma frågeloggar](concepts-slow-query-logs.md)
