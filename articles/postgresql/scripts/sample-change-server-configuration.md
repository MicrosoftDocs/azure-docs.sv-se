---
title: Azure CLI-skript – ändra serverkonfigurationer (PostgreSQL)
description: Det här CLI-exempelskriptet visar en lista över alla tillgängliga serverkonfigurationsalternativ och uppdaterar värdet för ett av alternativen.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 02/28/2018
ms.openlocfilehash: 313c3a59c1b47b4cc6ee6a38e46e85a2343bfb31
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94660533"
---
# <a name="list-and-update-configurations-of-an-azure-database-for-postgresql-server-using-azure-cli"></a>Visa och uppdatera konfigurationer av en Azure Database for PostgreSQL-server med Azure CLI
Det här CLI-exempelskriptet visar alla tillgängliga konfigurationsparametrar samt de tillåtna värdena för Azure Database for PostgreSQL-servern och anger *log_retention_days* till ett annat värde än standardvärdet.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Den här artikeln kräver version 2,0 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="sample-script"></a>Exempelskript
I det här exempelskriptet ändrar du de markerade raderna om du vill uppdatera administratörens användarnamn och lösenord till dina egna.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/change-server-configurations/change-server-configurations.sh?highlight=15-16 "List and update configurations of Azure Database for PostgreSQL.")]

## <a name="clean-up-deployment"></a>Rensa distribution
När skriptet har körts kan följande kommando användas för att ta bort resursgruppen och alla resurser som är kopplade till den. 
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/change-server-configurations/delete-postgresql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Förklaring av skript
Det här skriptet använder de kommandon som beskrivs i följande tabell:

| **Kommando** | **Kommentarer** |
|---|---|
| [az group create](/cli/azure/group) | Skapar en resursgrupp där alla resurser lagras. |
| [az postgres server create](/cli/azure/postgres/server) | Skapar en PostgreSQL-server som är värd för databaserna. |
| [az postgres server configuration list](/cli/azure/postgres/server/configuration) | Visa en lista med konfigurationerna av en Azure Database for PostgreSQL-server. |
| [az postgres server configuration set](/cli/azure/postgres/server/configuration) | Uppdatera konfigurationen av en Azure Database for PostgreSQL-server. |
| [az postgres server configuration show](/cli/azure/postgres/server/configuration) | Visa konfigurationen av en Azure Database for PostgreSQL-server. |
| [az group delete](/cli/azure/group) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg
- Läs mer om Azure CLI: [Azure CLI-dokumentation](/cli/azure).
- Prova ytterligare skript: [Azure CLI-exempel för Azure Database for PostgreSQL](../sample-scripts-azure-cli.md)
- Läs mer om serverparametrar i informationen om [hur du konfigurerar serverparametrar på Azure-portalen](../howto-configure-server-parameters-using-portal.md).
