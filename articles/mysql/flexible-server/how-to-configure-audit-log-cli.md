---
title: Konfigurera granskningsloggar med Azure CLI – Azure Database for MySQL – flexibel server
description: Den här artikeln beskriver hur du konfigurerar och kommer åt granskningsloggarna i Azure Database for MySQL flexibel server från Azure CLI.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 03/30/2021
ms.openlocfilehash: 757d765f44f09eeb6f7a24644abeb1ce4577f664
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107509097"
---
# <a name="configure-and-access-audit-logs-for-azure-database-for-mysql---flexible-server-using-the-azure-cli"></a>Konfigurera och få åtkomst till granskningsloggar för Azure Database for MySQL – flexibel server med hjälp av Azure CLI

> [!IMPORTANT]
> Azure Database for MySQL – flexibel server är för närvarande i offentlig förhandsversion.

Artikeln visar hur du konfigurerar [granskningsloggar för](concepts-audit-logs.md) din flexibla MySQL-server med Azure CLI.

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

## <a name="configure-audit-logging"></a>Konfigurera granskningsloggning

>[!IMPORTANT]
> Vi rekommenderar att du endast loggar de händelsetyper och användare som krävs för granskningsändamål för att säkerställa att serverns prestanda inte påverkas kraftigt.

Aktivera och konfigurera granskningsloggning.

```azurecli
# Enable audit logs
az mysql flexible-server parameter set \
--name audit_log_enabled \
--resource-group myresourcegroup \
--server-name mydemoserver \
--value ON
```

## <a name="next-steps"></a>Nästa steg
- Läs mer om [granskningsloggar](concepts-audit-logs.md)
