---
title: Återställa Azure Database for MySQL – flexibel server med Azure CLI
description: Den här artikeln beskriver hur du utför återställningsåtgärder i Azure Database for MySQL via Azure CLI.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 04/01/2021
ms.openlocfilehash: 61a8439b770d8909e04d1b80a5219810dc72aa34
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107509105"
---
# <a name="point-in-time-restore-of-a-azure-database-for-mysql---flexible-server-with-azure-cli"></a>Återställning till tidpunkt av en Azure Database for MySQL – flexibel server med Azure CLI


> [!IMPORTANT]
> Azure Database for MySQL – flexibel server är för närvarande i offentlig förhandsversion.

Den här artikeln innehåller stegvisa instruktioner för att utföra återställningar till en annan tidpunkt på en flexibel server med hjälp av säkerhetskopior.

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

## <a name="restore-a-server-from-backup-to-a-new-server"></a>Återställa en server från en säkerhetskopia till en ny server

Du kan köra följande kommando för att återställa en server till en tidigaste befintlig säkerhetskopia.

**Användning**
```azurecli
az mysql flexible-server restore --restore-time
                                 --source-server
                                 [--ids]
                                 [--location]
                                 [--name]
                                 [--no-wait]
                                 [--resource-group]
                                 [--subscription]
```

**Exempel:** Återställ en server från den här ```2021-03-03T13:10:00Z``` ögonblicksbilden av säkerhetskopian.

```azurecli
az mysql server restore \
--name mydemoserver-restored \
--resource-group myresourcegroup \
--restore-point-in-time "2021-03-03T13:10:00Z" \
--source-server mydemoserver
```
Hur lång tid det tar att återställa beror på storleken på de data som lagras på servern.

## <a name="perform-post-restore-tasks"></a>Utföra åtgärder efter återställning
När återställningen är klar bör du utföra följande uppgifter för att få igång dina användare och program igen:

- Om den nya servern är avsedd att ersätta den ursprungliga servern omdirigerar du klienter och klientprogram till den nya servern
- Se till att rätt VNet-regler är på plats för användare att ansluta. Dessa regler kopieras inte över från den ursprungliga servern.
- Se till att rätt inloggningar och behörigheter på databasnivå finns på plats
- Konfigurera aviseringar efter behov för den nyligen återställda servern

## <a name="next-steps"></a>Nästa steg
Läs mer om [affärskontinuhet](concepts-business-continuity.md)

