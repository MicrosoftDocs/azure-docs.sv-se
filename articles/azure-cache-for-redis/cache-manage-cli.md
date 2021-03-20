---
title: Hantera Azure cache för Redis med hjälp av den klassiska Azure-CLI
description: 'Lär dig hur du installerar den klassiska Azure-CLI: en på vilken plattform som helst, hur du använder den för att ansluta till ditt Azure-konto och hur du skapar och hanterar en Azure-cache för Redis från den klassiska CLI.'
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 01/23/2017
ms.author: yegu
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7643f882d5ac330046c169e0a3f2fa4920331d4e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92537702"
---
# <a name="how-to-create-and-manage-azure-cache-for-redis-using-the-azure-classic-cli"></a>Så här skapar och hanterar du Azure cache för Redis med hjälp av den klassiska Azure-CLI
> [!div class="op_single_selector"]
> * [PowerShell](cache-how-to-manage-redis-cache-powershell.md)
> * [Klassisk Azure-CLI](cache-manage-cli.md)
>

Den klassiska Azure CLI är ett bra sätt att hantera din Azure-infrastruktur från vilken plattform som helst. Den här artikeln visar hur du skapar och hanterar Azure cache för Redis-instanser med hjälp av den klassiska Azure-CLI: t.

[!INCLUDE [outdated-cli-content](../../includes/contains-classic-cli-content.md)]
> [!NOTE]
> De senaste skripten för Azure CLI-exempel finns i [Azure CLI Azure cache för Redis-exempel](cli-samples.md).

## <a name="prerequisites"></a>Förutsättningar
Du måste utföra följande steg för att skapa och hantera Azure cache för Redis-instanser med hjälp av den klassiska Azure-CLI: t.

* Du måste ha ett Azure-konto. Om du inte har någon kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/pricing/free-trial/) på bara några få minuter.
* [Installera den klassiska Azure CLI](/cli/azure/install-classic-cli).
* Anslut din Azure CLI-installation med ett personligt Azure-konto eller ett arbets-eller skol Azure-konto och logga in från den klassiska CLI: en med hjälp av `azure login` kommandot.
* Innan du kör något av följande kommandon, kan du växla klassisk CLI till Resource Manager-läge genom att köra `azure config mode arm` kommandot. Mer information finns i [använda den klassiska Azure-CLI: t för att hantera Azure-resurser och resurs grupper](../azure-resource-manager/management/manage-resources-cli.md).

## <a name="azure-cache-for-redis-properties"></a>Egenskaper för Azure Cache for Redis
Följande egenskaper används när du skapar och uppdaterar Azure cache för Redis-instanser.

| Egenskap | Switch | Beskrivning |
| --- | --- | --- |
| name |-n,--namn |Namnet på Azure-cachen för Redis. |
| Resursgrupp |-g,--resurs grupp |Namnet på resurs gruppen. |
| location |-l,--plats |Plats för att skapa cache. |
| ikoner |-z,--storlek |Storleken på Azure-cachen för Redis. Giltiga värden: [C0, C1, C2, C3, C4, C5, C6, P1, P2, P3, P4] |
| sku |-x,--SKU |Redis SKU. Ska vara en av: [Basic, standard, Premium] |
| EnableNonSslPort |-e,--aktivera-icke-SSL-port |Egenskapen EnableNonSslPort för Azure cache för Redis. Lägg till den här flaggan om du vill aktivera en icke-TLS/SSL-port för cacheminnet |
| Redis-konfiguration |-c,--Redis-Configuration |Redis-konfiguration. Ange en JSON-formaterad sträng med konfigurations nycklar och värden här. Format: "{" ":" "," ":" "}" |
| Redis-konfiguration |-f,--Redis-Configuration-File |Redis-konfiguration. Ange sökvägen till en fil som innehåller konfigurations nycklar och värden här. Format för fil posten: {"": "", "": ""} |
| Antal Shard |-r,--Shard-Count |Antalet Shards som ska skapas på en Premium-klustrad cache med klustring. |
| Virtual Network |-v,--virtuellt nätverk |När du är värd för din cache i ett VNET anger du det exakta ARM-resurs-ID: t för det virtuella nätverket för att distribuera Azure-cachen för Redis i. Exempel format:/subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| nyckel typ |-t,--nyckel-typ |Typ av nyckel som ska förnyas. Giltiga värden: [primär, sekundär] |
| StaticIP |-p,--statisk-IP \<static-ip\> |När du är värd för din cache i ett VNET, anger en unik IP-adress i under nätet för cachen. Om detta inte anges väljs en för dig från under nätet. |
| Undernät |t,--undernät \<subnet\> |När du är värd för din cache i ett VNET anger namnet på det undernät som cachen ska distribueras till. |
| VirtualNetwork |-v,--virtuellt nätverk \<virtual-network\> |När du är värd för din cache i ett VNET anger du det exakta ARM-resurs-ID: t för det virtuella nätverket för att distribuera Azure-cachen för Redis i. Exempel format:/subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| Prenumeration |-s,--prenumeration |Prenumerations-ID. |

## <a name="see-all-azure-cache-for-redis-commands"></a>Se alla Azure cache för Redis-kommandon
Om du vill se alla Azure cache för Redis-kommandon och deras parametrar använder du `azure rediscache -h` kommandot.

```azurecli
C:\>azure rediscache -h
help:    Commands to manage your Azure Cache for Redis(s)
help:
help:    Create an Azure Cache for Redis
help:      rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
help:
help:    Delete an existing Azure Cache for Redis
help:      rediscache delete [--name <name> --resource-group <resource-group> ]
help:
help:    List all Azure Cache for Redis within your Subscription or Resource Group
help:      rediscache list [options]
help:
help:    Show properties of an existing Azure Cache for Redis
help:      rediscache show [--name <name> --resource-group <resource-group>]
help:
help:    Change settings of an existing Azure Cache for Redis
help:      rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
help:
help:    Renew the authentication key for an existing Azure Cache for Redis
help:      rediscache renew-key [--name <name> --resource-group <resource-group> ]
help:
help:    Lists Primary and Secondary key of an existing Azure Cache for Redis
help:      rediscache list-keys [--name <name> --resource-group <resource-group>]
help:
help:    Options:
help:      -h, --help  output usage information
help:
help:    Current Mode: arm (Azure Resource Management)
```

## <a name="create-an-azure-cache-for-redis"></a>Skapa en Azure Cache for Redis
Använd följande kommando för att skapa en Azure-cache för Redis:

```azurecli
    azure rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
```

Om du vill ha mer information om det här kommandot kör du `azure rediscache create -h` kommandot.

```azurecli
C:\>azure rediscache create -h
help:    Create an Azure Cache for Redis
help:
help:    Usage: rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
help:
help:    Options:
help:      -h, --help                                               output usage information
help:      -v, --verbose                                            use verbose output
help:      -vv                                                      more verbose with debug output
help:      --json                                                   use json output
help:      -n, --name <name>                                        Name of the Azure Cache for Redis.
help:      -g, --resource-group <resource-group>                    Name of the Resource Group
help:      -l, --location <location>                                Location to create cache.
help:      -z, --size <size>                                        Size of the Azure Cache for Redis. Valid values: [C0, C1, C2, C3, C4, C5, C6, P1, P2, P3, P4]
help:      -x, --sku <sku>                                          Redis SKU. Should be one of : [Basic, Standard, Premium]
help:      -e, --enable-non-ssl-port                                EnableNonSslPort property of the Azure Cache for Redis. Add this flag if you want to enable the non-TLS/SSL Port for your cache
help:      -c, --redis-configuration <redis-configuration>          Redis Configuration. Enter a JSON formatted string of configuration keys and values here. Format:"{"<key1>":"<value1>","<key2>":"<value2>"}"
help:      -f, --redis-configuration-file <redisConfigurationFile>  Redis Configuration. Enter the path of a file containing configuration keys and values here. Format for the file entry: {"<key1>":"<value1>","<key2>":"<value2>"}
help:      -r, --shard-count <shard-count>                          Number of Shards to create on a Premium Cluster Cache
help:      -v, --virtual-network <virtual-network>                  The exact ARM resource ID of the virtual network to deploy the Azure Cache for Redis in. Example format: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1
help:      -t, --subnet <subnet>                                    Required when deploying an Azure Cache for Redis inside an existing Azure Virtual Network
help:      -p, --static-ip <static-ip>                              Required when deploying an Azure Cache for Redis inside an existing Azure Virtual Network
help:      -s, --subscription <id>                                  the subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```

## <a name="delete-an-existing-azure-cache-for-redis"></a>Ta bort en befintlig Azure-cache för Redis
Om du vill ta bort en Azure-cache för Redis, använder du följande kommando:

```azurecli
    azure rediscache delete [--name <name> --resource-group <resource-group> ]
```

Om du vill ha mer information om det här kommandot kör du `azure rediscache delete -h` kommandot.

```azurecli
C:\>azure rediscache delete -h
help:    Delete an existing Azure Cache for Redis
help:
help:    Usage: rediscache delete [--name <name> --resource-group <resource-group> ]
help:
help:    Options:
help:      -h, --help                             output usage information
help:      -v, --verbose                          use verbose output
help:      -vv                                    more verbose with debug output
help:      --json                                 use json output
help:      -n, --name <name>                      Name of the Azure Cache for Redis.
help:      -g, --resource-group <resource-group>  Name of the Resource Group under which the cache exists
help:      -s, --subscription <subscription>      the subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```

## <a name="list-all-azure-cache-for-redis-within-your-subscription-or-resource-group"></a>Lista alla Azure cache för Redis i din prenumeration eller resurs grupp
Om du vill visa en lista över Azure cache för Redis i din prenumeration eller resurs grupp använder du följande kommando:

```azurecli
    azure rediscache list [options]
```

Om du vill ha mer information om det här kommandot kör du `azure rediscache list -h` kommandot.

```azurecli
C:\>azure rediscache list -h
help:    List all Azure Cache for Redis within your Subscription or Resource Group
help:
help:    Usage: rediscache list [options]
help:
help:    Options:
help:      -h, --help                             output usage information
help:      -v, --verbose                          use verbose output
help:      -vv                                    more verbose with debug output
help:      --json                                 use json output
help:      -g, --resource-group <resource-group>  Name of the Resource Group
help:      -s, --subscription <subscription>      the subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```

## <a name="show-properties-of-an-existing-azure-cache-for-redis"></a>Visa egenskaper för en befintlig Azure-cache för Redis
Om du vill visa egenskaperna för en befintlig Azure-cache för Redis, använder du följande kommando:

```azurecli
    azure rediscache show [--name <name> --resource-group <resource-group>]
```

Om du vill ha mer information om det här kommandot kör du `azure rediscache show -h` kommandot.

```azurecli
C:\>azure rediscache show -h
help:    Show properties of an existing Azure Cache for Redis
help:
help:    Usage: rediscache show [--name <name> --resource-group <resource-group>]
help:
help:    Options:
help:      -h, --help                             output usage information
help:      -v, --verbose                          use verbose output
help:      -vv                                    more verbose with debug output
help:      --json                                 use json output
help:      -n, --name <name>                      Name of the Azure Cache for Redis.
help:      -g, --resource-group <resource-group>  Name of the Resource Group
help:      -s, --subscription <subscription>      the subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```

<a name="scale"></a>

## <a name="change-settings-of-an-existing-azure-cache-for-redis"></a>Ändra inställningar för en befintlig Azure-cache för Redis
Använd följande kommando om du vill ändra inställningarna för en befintlig Azure-cache för Redis:

```azurecli
    azure rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
```

Om du vill ha mer information om det här kommandot kör du `azure rediscache set -h` kommandot.

```azurecli
C:\>azure rediscache set -h
help:    Change settings of an existing Azure Cache for Redis
help:
help:    Usage: rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
help:
help:    Options:
help:      -h, --help                                               output usage information
help:      -v, --verbose                                            use verbose output
help:      -vv                                                      more verbose with debug output
help:      --json                                                   use json output
help:      -n, --name <name>                                        Name of the Azure Cache for Redis.
help:      -g, --resource-group <resource-group>                    Name of the Resource Group
help:      -c, --redis-configuration <redis-configuration>          Redis Configuration. Enter a JSON formatted string of configuration keys and values here.
help:      -f, --redis-configuration-file <redisConfigurationFile>  Redis Configuration. Enter the path of a file containing configuration keys and values here.
help:      -s, --subscription <subscription>                        the subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```

## <a name="renew-the-authentication-key-for-an-existing-azure-cache-for-redis"></a>Förnya autentiseringsnyckel för en befintlig Azure-cache för Redis
Om du vill förnya autentiseringsnyckel för en befintlig Azure-cache för Redis, använder du följande kommando:

```azurecli
    azure rediscache renew-key [--name <name> --resource-group <resource-group> --key-type <key-type>]
```

Ange `Primary` eller `Secondary` för `key-type` .

Om du vill ha mer information om det här kommandot kör du `azure rediscache renew-key -h` kommandot.

```azurecli
C:\>azure rediscache renew-key -h
help:    Renew the authentication key for an existing Azure Cache for Redis
help:
help:    Usage: rediscache renew-key [--name <name> --resource-group <resource-group> ]
help:
help:    Options:
help:      -h, --help                             output usage information
help:      -v, --verbose                          use verbose output
help:      -vv                                    more verbose with debug output
help:      --json                                 use json output
help:      -n, --name <name>                      Name of the Azure Cache for Redis.
help:      -g, --resource-group <resource-group>  Name of the Resource Group under which cache exists
help:      -t, --key-type <key-type>              type of key to renew. Valid values are: 'Primary', 'Secondary'.
help:      -s, --subscription <subscription>      the subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```

## <a name="list-primary-and-secondary-keys-of-an-existing-azure-cache-for-redis"></a>Lista primära och sekundära nycklar för en befintlig Azure-cache för Redis
Om du vill visa en lista över primära och sekundära nycklar för en befintlig Azure-cache för Redis, använder du följande kommando:

```azurecli
    azure rediscache list-keys [--name <name> --resource-group <resource-group>]
```

Om du vill ha mer information om det här kommandot kör du `azure rediscache list-keys -h` kommandot.

```azurecli
C:\>azure rediscache list-keys -h
help:    Lists Primary and Secondary key of an existing Azure Cache for Redis
help:
help:    Usage: rediscache list-keys [--name <name> --resource-group <resource-group>]
help:
help:    Options:
help:      -h, --help                             output usage information
help:      -v, --verbose                          use verbose output
help:      -vv                                    more verbose with debug output
help:      --json                                 use json output
help:      -n, --name <name>                      Name of the Azure Cache for Redis.
help:      -g, --resource-group <resource-group>  Name of the Resource Group under which Cache exists
help:      -s, --subscription <subscription>      the subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```